org.springframework.web.servlet.HttpServletBean#init

```java
	@Override
	public final void init() throws ServletException {
		if (logger.isDebugEnabled()) {
			logger.debug("Initializing servlet '" + getServletName() + "'");
		}

		// Set bean properties from init parameters.
		try {
            // 找导web.xml的配置文件,转换成ServletConfigPropertyValues对象
			PropertyValues pvs = new ServletConfigPropertyValues(getServletConfig(), this.requiredProperties);
            // 使用BeanWrapper构建DispatchServlet
			BeanWrapper bw = PropertyAccessorFactory.forBeanPropertyAccess(this);
			ResourceLoader resourceLoader = new ServletContextResourceLoader(getServletContext());
			bw.registerCustomEditor(Resource.class, new ResourceEditor(resourceLoader, getEnvironment()));
			initBeanWrapper(bw);
			bw.setPropertyValues(pvs, true);
		}
		catch (BeansException ex) {
			logger.error("Failed to set bean properties on servlet '" + getServletName() + "'", ex);
			throw ex;
		}

		// Let subclasses do whatever initialization they like.
        // 子类实现
		initServletBean();

		if (logger.isDebugEnabled()) {
			logger.debug("Servlet '" + getServletName() + "' configured successfully");
		}
	}
```

org.springframework.web.servlet.FrameworkServlet#initServletBean

```java
	@Override
	protected final void initServletBean() throws ServletException {
		getServletContext().log("Initializing Spring FrameworkServlet '" + getServletName() + "'");
		if (this.logger.isInfoEnabled()) {
			this.logger.info("FrameworkServlet '" + getServletName() + "': initialization started");
		}
		long startTime = System.currentTimeMillis();

		try {
            // 初始化spring上下文
			this.webApplicationContext = initWebApplicationContext();
            // 子类实现
			initFrameworkServlet();
		}
		catch (ServletException ex) {
			this.logger.error("Context initialization failed", ex);
			throw ex;
		}
		catch (RuntimeException ex) {
			this.logger.error("Context initialization failed", ex);
			throw ex;
		}

		if (this.logger.isInfoEnabled()) {
			long elapsedTime = System.currentTimeMillis() - startTime;
			this.logger.info("FrameworkServlet '" + getServletName() + "': initialization completed in " +
					elapsedTime + " ms");
		}
	}
```

org.springframework.web.servlet.FrameworkServlet#initWebApplicationContext

```java
protected WebApplicationContext initWebApplicationContext() {
    	// 得到跟上下文
        WebApplicationContext rootContext =
        WebApplicationContextUtils.getWebApplicationContext(getServletContext());
    WebApplicationContext wac = null;

    if (this.webApplicationContext != null) {
        // A context instance was injected at construction time -> use it
        wac = this.webApplicationContext;
        if (wac instanceof ConfigurableWebApplicationContext) {
            ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext) wac;
            if (!cwac.isActive()) {
                // The context has not yet been refreshed -> provide services such as
                // setting the parent context, setting the application context id, etc
                if (cwac.getParent() == null) {
                    // The context instance was injected without an explicit parent -> set
                    // the root application context (if any; may be null) as the parent
                    cwac.setParent(rootContext);
                }
                configureAndRefreshWebApplicationContext(cwac);
            }
        }
    }
    if (wac == null) {
        // No context instance was injected at construction time -> see if one
        // has been registered in the servlet context. If one exists, it is assumed
        // that the parent context (if any) has already been set and that the
        // user has performed any initialization such as setting the context id
        wac = findWebApplicationContext();
    }
    if (wac == null) {
        // No context instance is defined for this servlet -> create a local one
        wac = createWebApplicationContext(rootContext);
    }

    if (!this.refreshEventReceived) {
        // Either the context is not a ConfigurableApplicationContext with refresh
        // support or the context injected at construction time had already been
        // refreshed -> trigger initial onRefresh manually here.
        onRefresh(wac);
    }

    if (this.publishContext) {
        // Publish the context as a servlet context attribute.
        String attrName = getServletContextAttributeName();
        getServletContext().setAttribute(attrName, wac);
        if (this.logger.isDebugEnabled()) {
            this.logger.debug("Published WebApplicationContext of servlet '" + getServletName() +
                              "' as ServletContext attribute with name [" + attrName + "]");
        }
    }

    return wac;
}

```

org.springframework.web.servlet.DispatcherServlet#onRefresh

```java
@Override
protected void onRefresh(ApplicationContext context) {
    initStrategies(context);
}

/**
	 * Initialize the strategy objects that this servlet uses.
	 * <p>May be overridden in subclasses in order to initialize further strategy objects.
	 */
// 初始化各种策略
protected void initStrategies(ApplicationContext context) {
    initMultipartResolver(context);
    initLocaleResolver(context);
    initThemeResolver(context);
    // 找到正确的controller
    initHandlerMappings(context);
    initHandlerAdapters(context);
    // 异常机制
    initHandlerExceptionResolvers(context);
    initRequestToViewNameTranslator(context);
    // 视图机制
    initViewResolvers(context);
    initFlashMapManager(context);
}
```

# 临时用的

https://zhuanlan.zhihu.com/p/62562499

## 设置属性

```java
// 1. 设置属性
// Make web application context available
request.setAttribute(WEB_APPLICATION_CONTEXT_ATTRIBUTE, getWebApplicationContext());

// Make locale resolver available
request.setAttribute(LOCALE_RESOLVER_ATTRIBUTE, this.localeResolver);

// Make theme resolver available
request.setAttribute(THEME_RESOLVER_ATTRIBUTE, this.themeResolver);
```

## 根据 Request 请求的 URL 得到对应的 handler 执行链，其实就是拦截器和 Controller 代理对象。

```java
// 2. 找 handler 返回执行链
HandlerExecutionChain mappedHandler = getHandler(request);
```

## 得到 handler 的适配器

```java
// This will throw an exception if no adapter is found
// 3. 返回 handler 的适配器
HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
```

## 循环执行 handler 的 pre 拦截器

```java
// 4. 循环执行 handler 的 pre 拦截器
for (int i = 0; i < mappedHandler.getInterceptors().length; i++) {
    HandlerInterceptor interceptor = mappedHandler.getInterceptors()[i];
    // pre 拦截器
    if (!interceptor.preHandle(request, response, mappedHandler.getHandler())) {
        return;
    }
}
```

## 执行真正的 handler，并返回 ModelAndView(Handler 是个代理对象，可能会执行 AOP )

```java
// 5. 执行真正的 handler，并返回  ModelAndView(Handler 是个代理对象，可能会执行 AOP )
ModelAndView mv = ha.handle(request, response, mappedHandler.getHandler());
```

## 循环执行 handler 的 post 拦截器

```java
// 6. 循环执行 handler 的 post 拦截器
for (int i = mappedHandler.getInterceptors().length - 1; i >=0 ; i--) {
    HandlerInterceptor interceptor = mappedHandler.getInterceptors()[i];
    // post 拦截器
    interceptor.postHandle(request, response, mappedHandler.getHandler());
}

# 根据 ModelAndView 信息得到 View 实例
  View view = null;
if (mv.isReference()) {
    // We need to resolve this view name
    // 7. 根据 ModelAndView 信息得到 View 实例
    view = this.viewResolver.resolveViewName(mv.getViewName(), locale);
}


# 渲染 View 返回
// 8. 渲染 View 返回
view.render(mv.getModel(), request, response);
```

其实理解这些才是最重要的。

1. 用户发送请求至前端控制器DispatcherServlet
2. DispatcherServlet收到请求调用HandlerMapping处理器映射器。
3. 处理器映射器根据请求url找到具体的处理器，生成处理器对象及处理器拦截器(如果有则生成)一并返回给DispatcherServlet。
4. DispatcherServlet通过HandlerAdapter处理器适配器调用处理器
5. HandlerAdapter执行处理器(handler，也叫后端控制器)。
6. Controller执行完成返回ModelAndView
7. HandlerAdapter将handler执行结果ModelAndView返回给DispatcherServlet
8. DispatcherServlet将ModelAndView传给ViewReslover视图解析器
9. ViewReslover解析后返回具体View对象
10. DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）。
11. DispatcherServlet响应用户