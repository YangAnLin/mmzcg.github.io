1. 现在用的是SpringBoot整合Mybatis,配置文件还没有去看

2. 要注意Mapper层需要@Mapper注解

3. Mapping层用Provider这种方式

   1.一定要注意type的类名.class 和 method方法名,还要注意形参也得是一样的

   2.Provider的方法,大概就三个方法sql.SELECT,sql.WHERE,sql.FROM

   3.SQL 对象里的方法名跟别的不一样,小写的不行,idea也识别不到,要用大写,比如SLELECT

   4.Provider里返回的是String



![img](../img/clipboard.png)