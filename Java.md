# 0.Maven

这个是要从aliyun仓库里下载包

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.3.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.lzkj.server</groupId>
    <artifactId>agent</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>agent</name>
    <description>总控后台管理</description>

    <properties>
        <java.version>1.8</java.version>
        <swagger.version>2.7.0</swagger.version>
    </properties>

    <repositories>
        <repository>
            <id>central</id>
            <name>alibaba</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
            <layout>default</layout>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

    <dependencies>
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>5.3.1.Final</version>
        </dependency>
    </dependencies>

</project>
```

```xml
<mirrors>
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>        
    </mirror>
</mirrors>
```

# 1.POI

读

```java
public static void importFromExcel(InputStream is) throws IOException {
    HSSFWorkbook wb = new HSSFWorkbook(is);  //就代表一个 Excel 文件
    HSSFSheet sheet = wb.getSheetAt(0);//注意表格索引从 0 开始！

    for (int rowIndex = 1; rowIndex <= sheet.getLastRowNum(); rowIndex++) {
        // HSSFRow 代表一行数据
        HSSFRow row = sheet.getRow(rowIndex);
        if (row == null) {
            continue;
        }

        HSSFCell 厂商uuid = row.getCell(0);// 姓名列
        HSSFCell skuUUID = row.getCell(1); // 性别列
        HSSFCell 厂商 = row.getCell(2); // 年龄列
        HSSFCell 产品名称 = row.getCell(3); // 体重列
        HSSFCell 产品SKU = row.getCell(4); // 收入列
        HSSFCell 价格 = row.getCell(5);// 姓名列

        StringBuilder employeeInfoBuilder = new StringBuilder();
        employeeInfoBuilder.append("厂商uuid:"+厂商uuid)
                .append(", skuUUID : "+skuUUID)
                .append(" , 厂商 : "+厂商)
                .append(" , 产品名称 : "+产品名称)
                .append(" , 产品SKU : "+产品SKU)
                .append(" , 价格 : " +价格);
        System.out.println(employeeInfoBuilder.toString());
    }
}
```

写

```java
//统计的
    @GetMapping("/statistics")
    public void statistics(HttpServletResponse response) throws IOException {

        OutputStream os = response.getOutputStream();
        response.reset();
        response.setHeader("Content-disposition", "attachment; filename=" + new String("在线报考.xls".getBytes("GB2312"), "ISO8859-1"));
        response.setContentType("application/msexcel");

        String[] title = new String[]{
                "序号", "姓名", "身份证号", "电话", "方向",
                "准考证号", "缴费金额(元)", "支付方式", "订单号", "支付订单号",
                "支付时间", "考区", "报考专业",
                "报考课程一", "报考课程二", "报考课程三", "报考课程四", "报考课程五",
                "报考课程六", "报考课程七", "报考课程八", "报考课程九", "报考课程十",
                "报考课程十一", "报考课程十二", "报考课程十三", "报考课程十四"};

        HSSFWorkbook sheets = new HSSFWorkbook();
        HSSFSheet sheet = sheets.createSheet("demo");   //一个表
        HSSFRow row = sheet.createRow(0);   //第一行

        for (int i = 0; i < title.length; i++) {
            row.createCell(i).setCellValue(title[i]);   //序号
        }

        //查出已经购买的人
        List<String> ids = userMapper.selectAlreadyBuy();

        Long num = 1l;

        //查出每个人查出他们的订单号,
        for (String id : ids) {

            //一个用户有多个订单
            List<Orders> orders = userMapper.selectOrdersSnById(id);

            //查出所有的订单号,一个订单号就是一条数据
            for (Orders orders1 : orders) {

                //查出每个用户所有购买的课程
                List<ProductBuy> buys = userMapper.selectProductBuy(orders1.getOrderSn());

                String level = buys.get(0).getStatus().substring(6);//(平面)
                String zhuanye = buys.get(0).getStatus().substring(0, 6);// 报考专业

                HSSFRow nrow = sheet.createRow(num.intValue());    //第二......三.............行
                nrow.createCell(0).setCellValue(num);   //序号
                nrow.createCell(1).setCellValue(orders1.getMemberName());   //姓名
                nrow.createCell(2).setCellValue(orders1.getMemberIdcard());   //身份证号
                nrow.createCell(3).setCellValue(orders1.getMemberPhone());   //电话
                nrow.createCell(4).setCellValue(level);   //方向
                nrow.createCell(5).setCellValue(orders1.getOkCard());   //准考证号
                nrow.createCell(6).setCellValue(orders1.getActAmount().toString());   //缴费金额
                nrow.createCell(7).setCellValue(orders1.getPayStatus().equals("1") ? "支付宝" : "微信");   ////支付方式
                nrow.createCell(8).setCellValue(orders1.getOrderSn());   //订单号
                nrow.createCell(9).setCellValue(orders1.getPayOrgSn());   //支付订单号
                nrow.createCell(10).setCellValue(DateUtil.getDayMonthYearYYYYmmdd1(orders1.getUpdateTime()));   //支付时间
                nrow.createCell(11).setCellValue(orders1.getCity());   //考区
                nrow.createCell(12).setCellValue(zhuanye);   //报考专业


                for (int i = 0; i < buys.size(); i++) {
                    switch (buys.get(i).getProductName()) {
                        case "(专科)10351平面构成(一)":
                            nrow.createCell(13).setCellValue(buys.get(i).getProductName());
                            break;
                        default:
                            System.out.println("没有匹配到=======>"+buys.get(i).getProductName());
                            break;
                    }
                }
                num++;
            }
        }

        sheets.write(os);
        sheets.close();
    }
```

# 2.FTP

```xml
<dependency>
    <groupId>commons-net</groupId>
    <artifactId>commons-net</artifactId>
    <version>3.6</version>
</dependency>
```

```java
@Slf4j
public class FtpUtil {

    /**
     * Description: 向FTP服务器上传文件
     *
     * @param host     FTP服务器hostname
     * @param port     FTP服务器端口
     * @param username FTP登录账号
     * @param password FTP登录密码
     * @param basePath FTP服务器基础目录
     * @param filePath FTP服务器文件存放路径。例如分日期存放：/2015/01/01。文件的路径为basePath+filePath
     * @param filename 上传到FTP服务器上的文件名
     * @param input    输入流
     * @return 成功返回true，否则返回false
     */
    public static boolean uploadFile(String host, int port, String username, String password, String basePath,
                                     String filePath, String filename, InputStream input) {
        boolean result = false;
        FTPClient ftp = new FTPClient();
        ftp.setControlEncoding("GBK");
        log.info("filePath:{},filename:{}", filePath, filename);
        try {
            int reply;
            ftp.connect(host, port);// 连接FTP服务器
            // 如果采用默认端口，可以使用ftp.connect(host)的方式直接连接FTP服务器
            ftp.login(username, password);// 登录
            reply = ftp.getReplyCode();
            if (!FTPReply.isPositiveCompletion(reply)) {
                ftp.disconnect();
                return result;
            }
            //切换到上传目录
            if (!ftp.changeWorkingDirectory(basePath + filePath)) {
                //如果目录不存在创建目录
                String[] dirs = filePath.split("/");

                for (String dir : dirs) {
                    if (null == dir || "".equals(dir)) continue;
                    basePath += "/" + dir;
                    if (!ftp.changeWorkingDirectory(basePath)) {
                        if (!ftp.makeDirectory(basePath)) {
                            return result;
                        } else {
                            ftp.changeWorkingDirectory(basePath);
                        }
                    }
                }
            }
            //设置上传文件的类型为二进制类型
            ftp.setFileType(FTP.BINARY_FILE_TYPE);
            //上传文件
            ftp.enterLocalPassiveMode();//重要 外网服务器的时候必加这句
            if (!ftp.storeFile(filename, input)) {
                return result;
            }
            input.close();
            ftp.logout();
            result = true;
        } catch (IOException e) {
            log.error("上传文件异常：{}", e);
        } finally {
            if (ftp.isConnected()) {
                try {
                    ftp.disconnect();
                } catch (IOException ioe) {
                }
            }
        }
        return result;
    }

    /**
     * Description: 从FTP服务器下载文件
     *
     * @param host       FTP服务器hostname
     * @param port       FTP服务器端口
     * @param username   FTP登录账号
     * @param password   FTP登录密码
     * @param remotePath FTP服务器上的相对路径
     * @param fileName   要下载的文件名
     * @param localPath  下载后保存到本地的路径
     * @return
     */
    public static boolean downloadFile(String host, int port, String username, String password, String remotePath,
                                       String fileName, String localPath) {
        boolean result = false;
        FTPClient ftp = new FTPClient();
        try {
            int reply;
            ftp.connect(host, port);
            // 如果采用默认端口，可以使用ftp.connect(host)的方式直接连接FTP服务器
            ftp.login(username, password);// 登录
            reply = ftp.getReplyCode();
            if (!FTPReply.isPositiveCompletion(reply)) {
                ftp.disconnect();
                return result;
            }
            ftp.changeWorkingDirectory(remotePath);// 转移到FTP服务器目录
            FTPFile[] fs = ftp.listFiles();
            for (FTPFile ff : fs) {
                if (ff.getName().equals(fileName)) {
                    File localFile = new File(localPath + "/" + ff.getName());

                    OutputStream is = new FileOutputStream(localFile);
                    ftp.retrieveFile(ff.getName(), is);
                    is.close();
                }
            }

            ftp.logout();
            result = true;
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (ftp.isConnected()) {
                try {
                    ftp.disconnect();
                } catch (IOException ioe) {
                }
            }
        }
        return result;
    }

}
```

# 3.阿里云OSS

```xml
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
    <version>2.8.3</version>
</dependency>
```

```java
public static void uploadFile(AliOssVO aliOssVO,
      InputStream inputStream) {
   log.info("AliyunOssUpload>>>>>>>uploadFile参数aliOssVO:{}", aliOssVO);
   // 创建OSSClient实例。
   OSSClient ossClient = new OSSClient(aliOssVO.getEndpoint(), aliOssVO.getAccessKeyId(), aliOssVO.getAccessKeySecret());
   // 上传文件流。
   // InputStream inputStream = new FileInputStream("<yourlocalFile>")
   ossClient.putObject(aliOssVO.getBucketName(), aliOssVO.getFileName(), inputStream);

   // 关闭OSSClient。
   ossClient.shutdown();
}

public static void main(String[] args) {
   
   // Endpoint以杭州为例，其它Region请按实际情况填写。
   String endpoint = "http://oss-cn-hongkong.aliyuncs.com";
   // 云账号AccessKey有所有API访问权限，建议遵循阿里云安全最佳实践，创建并使用RAM子账号进行API访问或日常运维，请登录 https://ram.console.aliyun.com 创建。
   String accessKeyId = "LTAIkp4F2t1Hp6DV";
   String accessKeySecret = "YuPbNIxxwkG2AeIDOUqosrYW4je5ZN";
   AliOssVO aliOssVO = new AliOssVO(endpoint, accessKeyId, accessKeySecret);
   aliOssVO.setBucketName("app007");
   aliOssVO.setFileName("aaa.jpg");
   
   //AliyunOssUpload alioss = new AliyunOssUpload()
   try {
      File file = new File("C:\\img\\a.jpg");
      InputStream inputStream = new FileInputStream(file);
      AliyunOssUpload.uploadFile(aliOssVO, (FileInputStream) inputStream);
   } catch (FileNotFoundException e) {
      log.info("上传异常：{}",e);
   }
}
```

# 4.生成验证码

```java
/**
 *
 * @Title: generateCode
 * @Description: (生成验证码)
 * @param request
 * @param response
 * @throws IOException void
 */
@ApiOperation(value = "生成验证码")
@GetMapping("/generateCode")
public void generateCode(@RequestParam("account") String account
        , @RequestParam("stationMark") String stationMark
        , @RequestParam("flag") String flag
        , HttpServletRequest request
        , HttpServletResponse response) throws IOException {
    // 生成验证码
    BufferedImage bi = new BufferedImage(68, 22, BufferedImage.TYPE_INT_BGR);
    Graphics g = bi.getGraphics();
    Color c = new Color(234, 219, 160);
    g.setColor(c);
    g.fillRect(0, 0, 68, 22);
    //验证码style，可以随意变动你想要的风格
    Font font = new Font("Fixedsys", Font.BOLD, 15);
    g.setFont(font);

    String[] str = {"+", "-"};
    int result = 0;
    Random r = new Random();
    StringBuilder sb = new StringBuilder();
    //查询全局参数获取厅主代理后台选择的验证码生成规则
    GlobeResponse<TbGlobal> tbGlobal = commonService.getGlobalByStationMark(stationMark);
    //默认验证码字符集合
    char[] ch = "ABCDEFHJKMNPRSTUVWXY345678".toCharArray();
    //验证码不为空就拿当前验证码字符串，为空就用默认的
    if(StringUtils.isNotBlank(tbGlobal.getData().getValidateCode())){
        ch = tbGlobal.getData().getValidateCode().toCharArray();
    }
    //根据验证码类型生成验证码图形
    if("3".equals(tbGlobal.getData().getValidateType())){// 3 加减法
        // 创建集合存放生成的两个数字
        List list = new ArrayList ();
        for (int i = 0; i < 2; i++) {
            String rand = String.valueOf(ch[r.nextInt(ch.length)]);
            list.add(rand);
        }
        if(StringUtils.equals("+", str[new Random().nextInt(2)])){
            // 先取出来两个数字的和,然后再给list集合添加“+”和“=”以便组成加法算式
            result = Integer.parseInt((String)list.get(0)) + Integer.parseInt((String)list.get(1));
            log.debug("生成的验证码为:" + list.get(0) + "+" + list.get(1) + "=");
            list.add(1, "+");
        }else {
            if(Integer.parseInt((String)list.get(0)) > Integer.parseInt((String)list.get(1))){
                result = Integer.parseInt((String)list.get(0)) - Integer.parseInt((String)list.get(1));
                log.debug("生成的验证码为:" + list.get(0) + "-" + list.get(1) + "=");
                list.add(1, "-");
            }else{
                result = Integer.parseInt((String)list.get(0)) + Integer.parseInt((String)list.get(1));
                log.debug("生成的验证码为:" + list.get(0) + "+" + list.get(1) + "=");
                list.add(1, "+");
            }
        }
        // 遍历集合list将加法算是生成图片
        for (int j = 0; j < list.size(); j++) {
            g.setColor(new Color(r.nextInt(88), r.nextInt(188), r.nextInt(255)));
            g.drawString(list.get(j) + "", (j * 15) + 3, 18);
            sb.append(list.get(j) + "");
        }

    }else {
        int len = ch.length;
        int index;
        for (int k = 0; k < 4; ++k) {
            index = r.nextInt(len);
            //设置验证码字符随机颜色
            g.setColor(new Color(r.nextInt(88), r.nextInt(188), r.nextInt(255)));
            //画出对应随机的验证码字符
            g.drawString(ch[index] + "", (k * 15) + 3, 18);
            sb.append(ch[index]);
        }
    }
    //把验证码字符串放入Session
    request.getSession().setAttribute("piccode", sb.toString());
    //在HttpServletResponse中写入验证码图片信息
    response.setContentType("image/jpeg");
    response.setHeader("Pragma", "no-cache");
    response.setHeader("Cache-Control", "no-cache");
    response.setDateHeader("Expires", 0);
    OutputStream outputStream = response.getOutputStream();
    ImageIO.write(bi, "JPG", outputStream);
    outputStream.flush();
    outputStream.close();
    // 存入redis
    String key = RedisKeyPrefix.VERIFICATION_CODE + stationMark + ":";
    if("0".equals(flag)) {
        if(!"3".equals(tbGlobal.getData().getValidateType())){
            redisTemplate.opsForValue().set(key + account, sb.toString(), 5, TimeUnit.MINUTES);
        }else{
            redisTemplate.opsForValue().set(key + account, result + "", 5, TimeUnit.MINUTES);
        }
    }else{
        if(!"3".equals(tbGlobal.getData().getValidateType())){
            redisTemplate.opsForValue().set(key + flag, sb.toString(), 5, TimeUnit.MINUTES);
        }else{
            redisTemplate.opsForValue().set(key + flag, result + "", 5, TimeUnit.MINUTES);
        }
    }
}
```



# 5.HttpServletResponse 中获取到输出流

```java
@RequestMapping(value = "/no_checkCodeServlet3", method = RequestMethod.GET)
public void checkCodeServlet(HttpServletRequest request, HttpServletResponse response, HttpSession session)
        throws ServletException, IOException {
    response.setHeader("Pragma", "No-cache");
    response.setHeader("Cache-Control", "no-cache");
    response.setDateHeader("Expires", 0);
    response.setContentType("image/jpeg");
    String textCode = ValidateCode.generateTextCode(ValidateCode.TYPE_NUM_ONLY, 4, null);
    redisTemplate.opsForValue().set(RedisKeyConstant.USER_VERIFY_CODE + session.getId(), textCode, 30L,TimeUnit.MINUTES);
    BufferedImage image = ValidateCode.generateImageCode(textCode, 260, 80, 5, true, new Color(187, 255, 255), null,
            null);
    response.setContentType("image/jpeg");
    OutputStream outputStream = response.getOutputStream();
    ImageIO.write(image, "jpg", outputStream);
    outputStream.close();

}
```



# 6.Calendar日历类

```java
Calendar calendar=Calendar.getInstance();
calendar.setTime(new Date());

//改变时间,把今天的时分秒改成0点,0分,0秒,0毫秒
calendar.set(Calendar.HOUR, 0);  //小时
calendar.set(Calendar.MINUTE, 0); //分钟
calendar.set(Calendar.SECOND, 0); //秒
calendar.set(Calendar.MILLISECOND, 0);//毫秒

//打印今天的 0点,0分,0秒,0毫秒 的时间戳
System.out.println("指定时间的最后一秒的时间戳:"+calendar.getTimeInMillis());
System.out.println("指定时间的最后一秒:"+new SimpleDateFormat("yy:MM:dd hh:mm:ss").format(calendar.getTimeInMillis()));

System.out.println("--------------------------------------------------");
//打印今天的最后一秒前的时间
calendar.set(Calendar.HOUR, 23);  //小时
calendar.set(Calendar.MINUTE, 59); //分钟
calendar.set(Calendar.SECOND, 59); //秒
System.out.println("指定时间的最后一秒的时间戳:"+calendar.getTimeInMillis());
System.out.println("指定时间的最后一秒:"+new SimpleDateFormat("yy:MM:dd hh:mm:ss").format(calendar.getTimeInMillis()));

System.out.println("--------------------------------------------------");

System.out.println("获取修改过的今天日期:"+calendar.get(Calendar.DAY_OF_MONTH));//今天的日期

//第一个参数是要加的年份,月份,还是日期,第二个参数是更改的数字
calendar.set(Calendar.DAY_OF_MONTH,calendar.get(Calendar.DAY_OF_MONTH)+1);//让日期加1
System.out.println("获取修改过的今天日期+1的时间:"+calendar.get(Calendar.DATE));//加1之后的日期Top
```

```java
public static void main(String[] args) {
    Date date = new Date();
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd hh-mm:ss ");
    System.out.println(sdf.format(date));

	//long time = date.getTime();
    Calendar instance = Calendar.getInstance();
    instance.setTime(date);
    instance.set(Calendar.SECOND, 0);
    instance.set(Calendar.MILLISECOND,0);
    Date time = instance.getTime();
    System.out.println(sdf.format(time));
}
```

# 7.BigDecimal的应用

进一法

```java
double time1 = queryTotal.getTime()-new Date().getTime();
double time2 = 86400000;
if(time1 - time2 >0){
    BigDecimal bigDecimal = new BigDecimal(time1);
    BigDecimal bigDecima2 = new BigDecimal(time2);
    queryTotal.setTime(bigDecimal.divide(bigDecima2, BigDecimal.ROUND_UP).longValue());
}else{
    queryTotal.setTime(0l);
}
```

判断大小

```java
## BigDecimal判断大小
BigDecimal bigDecimal = new BigDecimal("1");
BigDecimal bigDecimal2 = new BigDecimal("2");
 
// -1 是小于
System.out.println(bigDecimal.compareTo(bigDecimal2));
// -1 是大于
System.out.println(bigDecimal2.compareTo(bigDecimal));
// 0是相等
System.out.println(bigDecimal.compareTo(bigDecimal));
```

# 1.Continue 和 Break



# **2.List转成string,去掉两边的中括号**

```java
StringUtils.strip(partnerApplyDetail1.getList().toString().toString(),"[]");
```

# 3.Long类型的比较

```java
public static void main(String[] args) {
    Long long1 = new Long(120);
    Long long2 = new Long(120);
    System.out.println(long1 == long2);  //false

    long long3 = 120;
    long long4 = 120;
    System.out.println(long3 == long4);  //true
}
```

# 基础

## IO

## 运算符





# 知识点

## equals()

### ==

对于基本类型和引用类型 == 的作用效果是不同的，如下所示：

- 基本类型：比较的是值是否相同；
- 引用类型：比较的是引用是否相同；

```java
String x = "string";
String y = "string";
String z = new String("string");
System.out.println(x==y); // true
System.out.println(x==z); // false
System.out.println(x.equals(y)); // true
System.out.println(x.equals(z)); // true
```

代码解读：因为 x 和 y 指向的是同一个引用，所以 == 也是 true，而 new String()方法则重写开辟了内存空间，所以 == 结果为 false，而 equals 比较的一直是值，所以结果都为 true。

### equals

equals 本质上就是 ==，只不过 String 和 Integer 等重写了 equals 方法，把它变成了值比较。看下面的代码就明白了。

首先来看默认情况下 equals 比较一个有相同值的对象，代码如下：

```java
class Cat {
    public Cat(String name) {
        this.name = name;
    }

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

Cat c1 = new Cat("王磊");
Cat c2 = new Cat("王磊");
System.out.println(c1.equals(c2)); // false
System.out.println(c1 == c2); // false
```

输出结果出乎我们的意料，竟然是 false？这是怎么回事，看了 equals 源码就知道了，源码如下：

```java
public boolean equals(Object obj) {
		return (this == obj);
}
```

`原来 equals 本质上就是 ==。`

`那问题来了，两个相同值的 String 对象，为什么返回的是 true？代码如下：`

```java
String s1 = new String("老王");
String s2 = new String("老王");
System.out.println(s1.equals(s2)); // true

public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

原来是 String 重写了 Object 的 equals 方法，把引用比较改成了值比较。

总体来说，== 对于基本类型来说是值比较，对于引用类型来说是比较的是引用；而 equals 默认情况下是引用比较，只是很多类重写了 equals 方法，比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。

## equals的重写规则

```java
public class Car {
	private int batch;
	public Car(int batch) {
		this.batch = batch;
	}
	public static void main(String[] args) {
		Car c1 = new Car(1);
		Car c2 = new Car(1);
		Car c3 = new Car(1);
		System.out.println("自反性->c1.equals(c1)：" + c1.equals(c1));
		System.out.println("对称性：");
		System.out.println(c1.equals(c2));
		System.out.println(c2.equals(c1));
		System.out.println("传递性：");
		System.out.println(c1.equals(c2));
		System.out.println(c2.equals(c3));
		System.out.println(c1.equals(c3));
		System.out.println("一致性：");
		for (int i = 0; i < 50; i++) {
			if (c1.equals(c2) != c1.equals(c2)) {
				System.out.println("equals方法没有遵守一致性！");
				break;
			}
		}
		System.out.println("equals方法遵守一致性！");
		System.out.println("与null比较：");
		System.out.println(c1.equals(null));
	}
	@Override
	public boolean equals(Object obj) {
		if (obj instanceof Car) {
			Car c = (Car) obj;
			return batch == c.batch;
		}
		return false;
	}
}
```

##  重写equals()的同时还得重写hashCode()

要是针对映射相关的操作（Map接口）。

Map接口的类会使用到键对象的哈希码，当我们调用put方法或者get方法对Map容器进行操作时，都是根据键对象的哈希码来计算存储位置的，因此如果我们对哈希码的获取没有相关保证，就可能会得不到预期的结果

```java
public class HashCodeTest {
	public static void main(String[] args) {
		String s="ok";
		StringBuilder sb =new StringBuilder(s);		
		System.out.println(s.hashCode()+"  "+sb.hashCode());
		
		String t = new String("ok");
		StringBuilder tb =new StringBuilder(s);
		System.out.println(t.hashCode()+"  "+tb.hashCode());
	}
}

# 运行结果
3548  1829164700
3548  2018699554
```

jdk中的`hashCode()`源码

```java
# object的方法
public native int hashCode();

# objects的方法
public static int hashCode(Object o) {
       return o != null ? o.hashCode() : 0;
}
```

重写hashCode()

```java
public class Model {

    private   String name;
    private double salary;
    private int sex;

    public Model(String name, double salary, int sex) {
        this.name = name;
        this.salary = salary;
        this.sex = sex;
    }

    @Override
    public int hashCode() {
        return Objects.hashCode(name)+new Double(salary).hashCode()+ new Integer(sex).hashCode();
    }

    public static void main(String[] args) {
        Model anthony = new Model("anthony", 23, 1);
        Model anthony2 = new Model("anthony", 23, 1);
        System.out.println(anthony.hashCode());
        System.out.println(anthony2.hashCode());
    }
}

# 输出结果
230433658
230433658
```

重写hadcode在map中的应用

```java
import java.util.HashMap;
import java.util.Map;
public class MapTest {
    public static void main(String[] args) {
        Map<String,Value> map1 = new HashMap<String,Value>();
        String s1 = new String("key");
        String s2 = new String("key");
        Value value = new Value(2);
        map1.put(s1, value);
        System.out.println("s1.equals(s2):"+s1.equals(s2));
        System.out.println("map1.get(s1):"+map1.get(s1));
        System.out.println("map1.get(s2):"+map1.get(s2));


        Map<Key,Value> map2 = new HashMap<Key,Value>();
        Key k1 = new Key("A");
        Key k2 = new Key("A");
        map2.put(k1, value);
        System.out.println("k1.equals(k2):"+s1.equals(s2));
        System.out.println("map2.get(k1):"+map2.get(k1));
        System.out.println("map2.get(k2):"+map2.get(k2));
    }

    static class Key{
        private String k;
        public Key(String key){
            this.k=key;
        }

        @Override
        public boolean equals(Object obj) {
            if(obj instanceof Key){
                Key key=(Key)obj;
                return k.equals(key.k);
            }
            return false;
        }

        /**
         *
         * 没有重写hashcode方法,结果是:
         * s1.equals(s2):true
         * map1.get(s1):类Value的值－－>2
         * map1.get(s2):类Value的值－－>2
         * k1.equals(k2):true
         * map2.get(k1):类Value的值－－>2
         * map2.get(k2):类Value的值－－>null
         *
         *
         * 重写了hadcode方法,结果是:
         * s1.equals(s2):true
         * map1.get(s1):类Value的值－－>2
         * map1.get(s2):类Value的值－－>2
         * k1.equals(k2):true
         * map2.get(k1):类Value的值－－>2
         * map2.get(k2):类Value的值－－>2
         */
        @Override
        public int hashCode() {
            return k.hashCode();
        }
    }

    static class Value{
        private int v;

        public Value(int v){
            this.v=v;
        }

        @Override
        public String toString() {
            return "类Value的值－－>"+v;
        }
    }
}
```

## 基本类型包装类对象常量池

（1）只有使用`引号`包含文本的方式创建的String对象之间使用“+”连接产生的新对象才会被加入字符串池中。
 （2）对于所有包含new方式新建对象（包括null）的“+”连接表达式，它所产生的新对象都不会被加入字符串池中。

```java
public class StringDemo {
    public static void main(String[] args) {
        String str1 = "str";
        String str2 = "ing";

        String str3 = "str" + "ing";
        // 在编译器中实际是创建了StringBuilder,犹如下面的例子
        String str4 = str1 + str2;
        System.out.println(str3 == str4);//false
        // 例子
        StringBuilder stringBuilder = new StringBuilder();
        stringBuilder.append("str");
        stringBuilder.append("ing");
        System.out.println(str3==stringBuilder.toString());//false

        String str5 = "string";
        System.out.println(str3 == str5);//true
    }
}
# 所以s4没有使用的引号,才是false
```

`String str4 = str1 + str2;`的反编译

```java
C:\lzkj-item\api-agent\src\main\java>javap -c StringDemo  
Compiled from "StringDemo.java"   
public class StringDemo {         
  public StringDemo();
    Code: 
       0: aload_0     
       1: invokespecial #1      // Method java/lang/Object."<init>":()V           
       4: return      
          
  public static void main(java.lang.String[]);
    Code:    
       0: ldc           #2      // String str 
       2: astore_1    
       3: ldc           #3      // String ing 
       5: astore_2    
       6: ldc           #4      // String string          
       8: astore_3    
       9: new           #5      // class java/lang/StringBuilder      
      12: dup         
      13: invokespecial #6      // Method java/lang/StringBuilder."<init>":()V    
      16: aload_1     
      17: invokevirtual #7      // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      20: aload_2     
      21: invokevirtual #7      // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      24: invokevirtual #8      // Method java/lang/StringBuilder.toString:()Ljava/lang/String;           
      27: astore        4         
      29: getstatic     #9      // Field java/lang/System.out:Ljava/io/PrintStream;           
      32: aload_3     
      33: aload         4         
      35: if_acmpne     42        
      38: iconst_1    
      39: goto          43        
      42: iconst_0    
      43: invokevirtual #10     // Method java/io/PrintStream.println:(Z)V        
      46: return      
}
```

特例:

```java
public static final String A = "ab"; // 常量A
public static final String B = "cd"; // 常量B
public static void main(String[] args) {
     String s = A + B;  // 将两个常量用+连接对s进行初始化 
     String t = "abcd";   
    if (s == t) {   
         System.out.println("s等于t，它们是同一个对象");   
     } else {   
         System.out.println("s不等于t，它们不是同一个对象");   
     }   
 } 
# s等于t，它们是同一个对象
# A和B都是常量，值是固定的，因此s的值也是固定的，它在类被编译时就已经确定了。也就是说：String s=A+B; 等同于：String s="ab"+"cd";

C:\lzkj-item\api-agent\src\main\java>javap -c StringDemo   
Compiled from "StringDemo.java"      
public class StringDemo { 
  public static final java.lang.String A;       
    
  public static final java.lang.String B;       
    
  public StringDemo();    
    Code:      
       0: aload_0         
       1: invokespecial #1       // Method java/lang/Object."<init>":()V         
       4: return          
    
  public static void main(java.lang.String[]);  
    Code:      
       0: ldc#3       // String abcd 
       2: astore_1        
       3: ldc#3       // String abcd 
       5: astore_2        
       6: aload_1         
       7: aload_2         
       8: if_acmpne     22
      11: getstatic     #4       // Field java/lang/System.out:Ljava/io/PrintStream;        
      14: ldc#5       // String s等于t，它们是同一个对象   
      16: invokevirtual #6       // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      19: goto          30
      22: getstatic     #4       // Field java/lang/System.out:Ljava/io/PrintStream;        
      25: ldc#7       // String s不等于t，它们不是同一个对象 
      27: invokevirtual #6       // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      30: return          
}
# 第30行代码的abcd,指的是s
# 第32行代码的abcd,指的是t

# ----------------------------------------------------------------------------------------------

public static final String A; // 常量A
public static final String B;    // 常量B
static {   
     A = "ab";   
     B = "cd";   
 }   
 public static void main(String[] args) {   
    // 将两个常量用+连接对s进行初始化   
     String s = A + B;   
     String t = "abcd";   
    if (s == t) {   
         System.out.println("s等于t，它们是同一个对象");   
     } else {   
         System.out.println("s不等于t，它们不是同一个对象");   
     }   
 } 
# s不等于t，它们不是同一个对象
# A和B虽然被定义为常量，但是它们都没有马上被赋值。在运算出s的值之前，他们何时被赋值，以及被赋予什么样的值，都是个变数。因此A和B在被赋值之前，性质类似于一个变量。那么s就不能在编译期被确定，而只能在运行时被创建了。
    
C:\lzkj-item\api-agent\src\main\java>javap -c StringDemo
Compiled from "StringDemo.java"
public class StringDemo {
  public static final java.lang.String A;

  public static final java.lang.String B;

  public StringDemo();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: new           #2                  // class java/lang/StringBuilder
       3: dup
       4: invokespecial #3                  // Method java/lang/StringBuilder."<init>":()V
       7: getstatic     #4                  // Field A:Ljava/lang/String;
      10: invokevirtual #5                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      13: getstatic     #6                  // Field B:Ljava/lang/String;
      16: invokevirtual #5                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      19: invokevirtual #7                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      22: astore_1
      23: ldc           #8                  // String abcd
      25: astore_2
      26: aload_1
      27: aload_2
      28: if_acmpne     42
      31: getstatic     #9                  // Field java/lang/System.out:Ljava/io/PrintStream;
      34: ldc           #10                 // String s等于t，它们是同一个对象
      36: invokevirtual #11                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      39: goto          50
      42: getstatic     #9                  // Field java/lang/System.out:Ljava/io/PrintStream;
      45: ldc           #12                 // String s不等于t，它们不是同一个对象
      47: invokevirtual #11                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      50: return

  static {};
    Code:
       0: ldc           #13                 // String ab
       2: putstatic     #4                  // Field A:Ljava/lang/String;
       5: ldc           #14                 // String cd
       7: putstatic     #6                  // Field B:Ljava/lang/String;
      10: return
}

# 第94的abcd指的是t
```

## 字符串常量池

### String Pool机制

目的:JVM为了提高性能和减少内存开销,实现数据共享

1.为字符串开辟一个字符串常量池(存在于方法区)

2.创建字符串常量时,首页坚持字符串常量池是否存在该字符串

3.存在该字符串,返回引用实例,不存在,实例化该字符串并放入池中

### intern()

String的intern()方法会查找在常量池中是否存在一份equal相等的字符串,如果有则返回该字符串的引用,如果没有则添加自己的字符串进入常量池

```java
public static void main(String[] args) {    
      String s1 = new String("计算机");
      String s2 = s1.intern();
      String s3 = "计算机";
      System.out.println("s1 == s2? " + (s1 == s2));
      System.out.println("s3 == s2? " + (s3 == s2));
}
s1 == s2? false
s3 == s2? true
```

## Integer常量池

```java
public class ArrayTest {
    public static void main(String[] args) {
        Integer i1 = new Integer(1);
        Integer i2 = new Integer(1);
        System.out.println(i1.hashCode()); // 1
        System.out.println(i2.hashCode()); // 1
        System.out.println(i1 == i2);      // false,new出来的地址不一样
        System.out.println(i1.equals(i2)); // true,数值内容是一样的

        System.out.println("-------------------");

        Integer i3 = 1;
        Integer i4 = 1;
        System.out.println(i3.hashCode()); // 1
        System.out.println(i4.hashCode()); // 1
        System.out.println(i3 == i4);  	   // true,在常量池汇中,应用一样	
        System.out.println(i3.equals(i4)); // true,数值内容一样
    }
}
```



```java
# 源码
public class ArrayTest {
    public static void main(String[] args) {
        Integer i1 = 25;
        Integer i2 = new Integer(26);
    }
}

# 反编译
public class ArrayTest{
  public static void main(String[] paramArrayOfString){
    Integer localInteger1 = Integer.valueOf(25);
    Integer localInteger2 = new Integer(26);
  }
}

# valueOf源码
public static Integer valueOf(int i) {
    assert IntegerCache.high >= 127;
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

## Class常量池

###  class常量池简介(Class Constant Pool)

我们写的每一个Java类被编译后，就会形成一份class文件；class文件中除了包含类的版本、字段、方法、接口等描述信息外，还有一项信息就是常量池(constant pool table)，用于存放编译器生成的各种字面量(Literal)和符号引用(Symbolic References)；
每个class文件都有一个class常量池

###  什么是字面量和符号引用

- 字面量包括：1.文本字符串 2.八种基本类型的值 3.被声明为final的常量等;
- 符号引用包括：1.类和方法的全限定名 2.字段的名称和描述符 3.方法的名称和描述符

## 运行时常量池(Runtime Constant Pool)

运行时常量池存在于内存中，也就是class常量池被加载到内存之后的版本，不同之处是：它的字面量可以动态的添加(String#intern()),符号引用可以被解析为直接引用
JVM在执行某个类的时候，必须经过加载、连接、初始化，而连接又包括验证、准备、解析三个阶段。而当类加载到内存中后，jvm就会将class常量池中的内容存放到运行时常量池中，由此可知，运行时常量池也是每个类都有一个。在解析阶段，会把符号引用替换为直接引用，解析的过程会去查询字符串常量池，也就是我们上面所说的StringTable，以保证运行时常量池所引用的字符串与字符串常量池中是一致的

## 集合框架

![](https://image.yanganlin.com/blog/20200717134605.jpg)

### HashMap源码

![img](https://raw.githubusercontent.com/YangAnLin/images/master/20200831115117.webp)

#### put方法具体实现







# 并发

## Java 中用到的线程调度算法是什么

JAVA 虚拟机的一项任务就是负责线程的调度，线程调度是指按照特定机制为多个线程分配 CPU 的使用权。

有两种调度模型：`分时调度模型`和`抢占式调度模型`

分时调度模型是指让所有的线程轮流获得 cpu 的使用权，并且平均分配每个线程占用的 CPU 的时间片这个也比较好理解。

Java虚拟机采用抢占式调度模型，是指优先让可运行池中优先级高的线程占用CPU，如果可运行池中的线程优先级相同，那么就随机选择一个线程，使其占用CPU。处于运行状态的线程会一直运行，直至它不得不放弃 CPU。

## runnable 和 callable 有什么区别

- Runnable 接口 run 方法无返回值；Callable 接口 call 方法有返回值，是个泛型，和Future、FutureTask配合可以用来获取异步执行的结果
- Runnable 接口 run 方法只能抛出运行时异常，且无法捕获处理；Callable 接口 call 方法允许抛出异常，可以获取异常信息

**注**：Callalbe接口支持返回执行结果，需要调用FutureTask.get()得到，此方法会阻塞主进程的继续往下执行，如果不调用不会阻塞。

## 线程的 run()和 start()有什么区别

## start() 方法时会执行 run() 方法

new 一个 Thread，线程进入了新建状态。调用 start() 方法，会启动一个线程并使线程进入了就绪状态，当分配到时间片后就可以开始运行了。 start() 会执行线程的相应准备工作，然后自动执行 run() 方法的内容，这是真正的多线程工作。

而直接执行 run() 方法，会把 run 方法当成一个 main 线程下的普通方法去执行，并不会在某个线程中执行它，所以这并不是多线程工作。

总结： 调用 start 方法方可启动线程并使线程进入就绪状态，而 run 方法只是 thread 的一个普通方法调用，还是在主线程里执行。

## 什么是 FutureTask

FutureTask 表示一个异步运算的任务。FutureTask 里面可以传入一个 Callable 的具体实现类，可以对这个异步运算的任务的结果进行等待获取、判断是否已经完成、取消任务等操作。只有当运算完成的时候结果才能取回，如果运算尚未完成 get 方法将会阻塞。一个 FutureTask 对象可以对调用了 Callable 和 Runnable 的对象进行包装，由于 FutureTask 也是Runnable 接口的实现类，所以 FutureTask 也可以放入线程池中。

## 线程的 sleep()方法和 yield()方法有什么区别？

（1） sleep()方法给其他线程运行机会时不考虑线程的优先级，因此会给低优先级的线程以运行的机会；yield()方法只会给相同优先级或更高优先级的线程以运行的机会；

（2） 线程执行 sleep()方法后转入阻塞（blocked）状态，而执行 yield()方法后转入就绪（ready）状态；

（3）sleep()方法声明抛出 InterruptedException，而 yield()方法没有声明任何异常；

（4）sleep()方法比 yield()方法（跟操作系统 CPU 调度相关）具有更好的可移植性，通常不建议使用yield()方法来控制并发线程的执行。

## 如何停止一个正在运行的线程？

1. 使用退出标志，使线程正常退出，也就是当run方法完成后线程终止。
2. 使用stop方法强行终止，但是不推荐这个方法，因为stop和suspend及resume一样都是过期作废的方法。
3. 使用interrupt方法中断线程。

## 如果你提交任务时，线程池队列已满，这时会发生什么

这里区分一下：

（1）如果使用的是无界队列 LinkedBlockingQueue，也就是无界队列的话，没关系，继续添加任务到阻塞队列中等待执行，因为 LinkedBlockingQueue 可以近乎认为是一个无穷大的队列，可以无限存放任务

（2）如果使用的是有界队列比如 ArrayBlockingQueue，任务首先会被添加到ArrayBlockingQueue 中，ArrayBlockingQueue 满了，会根据maximumPoolSize 的值增加线程数量，如果增加了线程数量还是处理不过来，ArrayBlockingQueue 继续满，那么则会使用拒绝策略RejectedExecutionHandler 处理满了的任务，默认是 AbortPolicy

## 什么叫线程安全？servlet 是线程安全吗?

线程安全是编程中的术语，指某个方法在多线程环境中被调用时，能够正确地处理多个线程之间的共享变量，使程序功能正确完成。

Servlet 不是线程安全的，servlet 是单实例多线程的，当多个线程同时访问同一个方法，是不能保证共享变量的线程安全性的。

Struts2 的 action 是多实例多线程的，是线程安全的，每个请求过来都会 new 一个新的 action 分配给这个请求，请求完成后销毁。

SpringMVC 的 Controller 是线程安全的吗？不是的，和 Servlet 类似的处理流程。

Struts2 好处是不用考虑线程安全问题；Servlet 和 SpringMVC 需要考虑线程安全问题，但是性能可以提升不用处理太多的 gc，可以使用 ThreadLocal 来处理多线程的问题。

## 线程类的构造方法、静态块是被哪个线程调用的

线程类的构造方法、静态块是被 new这个线程类所在的线程所调用的，而 run 方法里面的代码才是被线程自身所调用的。

举个例子，假设 Thread2 中 new 了Thread1，main 函数中 new 了 Thread2，那么：

（1）Thread2 的构造方法、静态块是 main 线程调用的，Thread2 的 run()方法是Thread2 自己调用的

（2）Thread1 的构造方法、静态块是 Thread2 调用的，Thread1 的 run()方法是Thread1 自己调用的

## 为什么代码会重排序？

在执行程序时，为了提供性能，处理器和编译器常常会对指令进行重排序，但是不能随意重排序，不是你想怎么排序就怎么排序，它需要满足以下两个条件：

- 在单线程环境下不能改变程序运行的结果；
- 存在数据依赖关系的不允许重排序

需要注意的是：重排序不会影响单线程环境的执行结果，但是会破坏多线程的执行语义。

## ThreadLocal

### ThreadLocal 是什么？有哪些使用场景？

ThreadLocal 是一个本地线程副本变量工具类，在每个线程中都创建了一个 ThreadLocalMap 对象，简单说 ThreadLocal 就是一种以空间换时间的做法，每个线程可以访问自己内部 ThreadLocalMap 对象内的 value。通过这种方式，避免资源在多线程间共享。

原理：线程局部变量是局限于线程内部的变量，属于线程自身所有，不在多个线程间共享。Java提供ThreadLocal类来支持线程局部变量，是一种实现线程安全的方式。但是在管理环境下（如 web 服务器）使用线程局部变量的时候要特别小心，在这种情况下，工作线程的生命周期比任何应用变量的生命周期都要长。任何线程局部变量一旦在工作完成后没有释放，Java 应用就存在内存泄露的风险。

经典的使用场景是为每个线程分配一个 JDBC 连接 Connection。这样就可以保证每个线程的都在各自的 Connection 上进行数据库的操作，不会出现 A 线程关了 B线程正在使用的 Connection； 还有 Session 管理 等问题。

### ThreadLocal造成内存泄漏的原因？

`ThreadLocalMap` 中使用的 key 为 `ThreadLocal` 的弱引用,而 value 是强引用。所以，如果 `ThreadLocal` 没有被外部强引用的情况下，在垃圾回收的时候，key 会被清理掉，而 value 不会被清理掉。这样一来，`ThreadLocalMap` 中就会出现key为null的Entry。假如我们不做任何措施的话，value 永远无法被GC 回收，这个时候就可能会产生内存泄露。ThreadLocalMap实现中已经考虑了这种情况，在调用 `set()`、`get()`、`remove()` 方法的时候，会清理掉 key 为 null 的记录

### ThreadLocal内存泄漏解决方案？

- 每次使用完ThreadLocal，都调用它的remove()方法，清除数据。
- 在使用线程池的情况下，没有及时清理ThreadLocal，不仅是内存泄漏的问题，更严重的是可能导致业务逻辑出现问题

##  BlockingQueue

### 什么是阻塞队列

阻塞队列（BlockingQueue）是一个支持两个附加操作的队列。

这两个附加的操作是：

* 在队列为空时，获取元素的线程会等待队列变为非空。

* 当队列满时，存储元素的线程会等待队列可用。

阻塞队列常用于生产者和消费者的场景，生产者是往队列里添加元素的线程，消费者是从队列里拿元素的线程。阻塞队列就是生产者存放元素的容器，而消费者也只从容器里拿元素。

BlockingQueue 接口是 Queue 的子接口，它的主要用途并不是作为容器，而是作为线程同步的的工具

JDK7 提供了 7 个阻塞队列。分别是：

ArrayBlockingQueue ：一个由数组结构组成的有界阻塞队列。

LinkedBlockingQueue ：一个由链表结构组成的有界阻塞队列。

PriorityBlockingQueue ：一个支持优先级排序的无界阻塞队列。

DelayQueue：一个使用优先级队列实现的无界阻塞队列。

SynchronousQueue：一个不存储元素的阻塞队列。

LinkedTransferQueue：一个由链表结构组成的无界阻塞队列。

LinkedBlockingDeque：一个由链表结构组成的双向阻塞队列。

### ConcurrentLinkedQueue详解

### ArrayBlockingQueue详解

### LinkedBlockingQueue详解

## 线程池

### 创建线程池

线程池顾名思义就是事先创建若干个可执行的线程放入一个池（容器）中，需要的时候从池中获取线程不用自行创建，使用完毕不需要销毁线程而是放回池中，从而减少创建和销毁线程对象的开销

（1）**newSingleThreadExecutor**：创建一个单线程的线程池。这个线程池只有一个线程在工作，也就是相当于单线程串行执行所有任务。如果这个唯一的线程因为异常结束，那么会有一个新的线程来替代它。此线程池保证所有任务的执行顺序按照任务的提交顺序执行。

（2）**newFixedThreadPool**：创建固定大小的线程池。每次提交一个任务就创建一个线程，直到线程达到线程池的最大大小。线程池的大小一旦达到最大值就会保持不变，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程。如果希望在服务器上使用线程池，建议使用 newFixedThreadPool方法来创建线程池，这样能获得更好的性能。

（3） newCachedThreadPool：创建一个可缓存的线程池。如果线程池的大小超过了处理任务所需要的线程，那么就会回收部分空闲（60 秒不执行任务）的线程，当任务数增加时，此线程池又可以智能的添加新线程来处理任务。此线程池不会对线程池大小做限制，线程池大小完全依赖于操作系统（或者说 JVM）能够创建的最大线程大小。

（4）newScheduledThreadPool：创建一个大小无限的线程池。此线程池支持定时以及周期性执行任务的需求。

### 线程池都有哪些状态

- RUNNING：这是最正常的状态，接受新的任务，处理等待队列中的任务。
- SHUTDOWN：不接受新的任务提交，但是会继续处理等待队列中的任务。
- STOP：不接受新的任务提交，不再处理等待队列中的任务，中断正在执行任务的线程。
- TIDYING：所有的任务都销毁了，workCount 为 0，线程池的状态在转换为 TIDYING 状态时，会执行钩子方法 terminated()。
- TERMINATED：terminated()方法结束后，线程池的状态就会变成这个

### Executor 框架

Executor 框架是一个根据一组执行策略调用，调度，执行和控制的异步任务的框架。

每次执行任务创建线程 new Thread()比较消耗性能，创建一个线程是比较耗时、耗资源的，而且无限制的创建线程会引起应用程序内存溢出。

所以创建一个线程池是个更好的的解决方案，因为可以限制线程的数量并且可以回收再利用这些线程。利用Executors 框架可以非常方便的创建一个线程池。

### Executor 和 Executors 的区别

- Executors 工具类的不同方法按照我们的需求创建了不同的线程池，来满足业务的需求。
- Executor 接口对象能执行我们的线程任务。
- ExecutorService 接口继承了 Executor 接口并进行了扩展，提供了更多的方法我们能获得任务执行的状态并且可以获取任务的返回值。
- 使用 ThreadPoolExecutor 可以创建自定义线程池。
- Future 表示异步计算的结果，他提供了检查计算是否完成的方法，以等待计算的完成，并可以使用 get()方法获取计算的结果

### 线程池中 submit() 和 execute()

接收参数：execute()只能执行 Runnable 类型的任务。submit()可以执行 Runnable 和 Callable 类型的任务。

返回值：submit()方法可以返回持有计算结果的 Future 对象，而execute()没有

异常处理：submit()方便Exception处理

### ThreadPoolExecutor详解

![图解线程池实现原理](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxOS8xMS8yNS8xNmVhMDQ3NjEyOTVlNzY2?x-oss-process=image/format,png)

#### Executors和ThreaPoolExecutor创建线程池的区别

《阿里巴巴Java开发手册》中强制线程池不允许使用 Executors 去创建，而是通过 ThreadPoolExecutor 的方式，这样的处理方式让写的同学更加明确线程池的运行规则，规避资源耗尽的风险

Executors 各个方法的弊端：

- newFixedThreadPool 和 newSingleThreadExecutor:
  主要问题是堆积的请求处理队列可能会耗费非常大的内存，甚至 OOM。
- newCachedThreadPool 和 newScheduledThreadPool:
  主要问题是线程数最大数是 Integer.MAX_VALUE，可能会创建数量非常多的线程，甚至 OOM。

ThreaPoolExecutor创建线程池方式只有一种，就是走它的构造函数，参数自己指定

#### ThreadPoolExecutor构造函数重要参数分析

**`ThreadPoolExecutor`** **3 个最重要的参数：**

- **`corePoolSize`** ：核心线程数，线程数定义了最小可以同时运行的线程数量。
- **`maximumPoolSize`** ：线程池中允许存在的工作线程的最大数量
- **`workQueue`**：可以用前面的阻塞队列,当新任务来的时候会先判断当前运行的线程数量是否达到核心线程数，如果达到的话，任务就会被存放在队列中。

`ThreadPoolExecutor`其他常见参数:

1. **`keepAliveTime`**：线程池中的线程数量大于 `corePoolSize` 的时候，如果这时没有新的任务提交，核心线程外的线程不会立即销毁，而是会等待，直到等待的时间超过了 `keepAliveTime`才会被回收销毁；
2. **`unit`** ：`keepAliveTime` 参数的时间单位。
3. **`threadFactory`**：为线程池提供创建新线程的线程工厂
4. **`handler`** ：线程池任务队列超过 maxinumPoolSize 之后的拒绝策略

#### ThreadPoolExecutor饱和策略

**`ThreadPoolExecutor`** **饱和策略定义:**

如果当前同时运行的线程数量达到最大线程数量并且队列也已经被放满了任时，`ThreadPoolTaskExecutor` 定义一些策略:

- **`ThreadPoolExecutor.AbortPolicy`**：抛出 `RejectedExecutionException`来拒绝新任务的处理。
- **`ThreadPoolExecutor.CallerRunsPolicy`**：调用执行自己的线程运行任务。您不会任务请求。但是这种策略会降低对于新任务提交速度，影响程序的整体性能。另外，这个策略喜欢增加队列容量。如果您的应用程序可以承受此延迟并且你不能任务丢弃任何一个任务请求的话，你可以选择这个策略。
- **`ThreadPoolExecutor.DiscardPolicy`**：不处理新任务，直接丢弃掉。
- **`ThreadPoolExecutor.DiscardOldestPolicy`**： 此策略将丢弃最早的未处理的任务请求。

举个例子： Spring 通过 `ThreadPoolTaskExecutor` 或者我们直接通过 `ThreadPoolExecutor` 的构造函数创建线程池的时候，当我们不指定 `RejectedExecutionHandler` 饱和策略的话来配置线程池的时候默认使用的是 `ThreadPoolExecutor.AbortPolicy`。

在默认情况下，`ThreadPoolExecutor` 将抛出 `RejectedExecutionException` 来拒绝新来的任务 ，这代表你将丢失对这个任务的处理。

对于可伸缩的应用程序，建议使用 `ThreadPoolExecutor.CallerRunsPolicy`。当最大池被填满时，此策略为我们提供可伸缩队列。

### ScheduledThreadPoolExecutor详解

### ScheduledThreadPoolExecutor详解(没有)

### FutureTask详解(没有)

### 原子操作类

原子操作（atomic operation）意为”不可被中断的一个或一系列操作” 。

int++并不是一个原子操作，所以当一个线程读取它的值并加 1 时，另外一个线程有可能会读到之前的值，这就会引发错误。

java.util.concurrent 这个包里面提供了一组原子类。其基本的特性就是在多线程环境下，当有多个线程同时执行这些类的实例包含的方法时，具有排他性，即当某个线程进入方法，执行其中的指令时，不会被其他线程打断，而别的线程就像自旋锁一样，一直等到该方法执行完成，才由 JVM 从等待队列中选择另一个线程进入，这只是一种逻辑上的理解。

原子类：AtomicBoolean，AtomicInteger，AtomicLong，AtomicReference

原子数组：AtomicIntegerArray，AtomicLongArray，AtomicReferenceArray

原子属性更新器：AtomicLongFieldUpdater，AtomicIntegerFieldUpdater，AtomicReferenceFieldUpdater

解决 ABA 问题的原子类：AtomicMarkableReference（通过引入一个 boolean来反映中间有没有变过），AtomicStampedReference（通过引入一个 int 来累加来反映中间有没有变过）

#### Atomic 的原理 

Atomic包中的类基本的特性就是在多线程环境下，当有多个线程同时对单个（包括基本类型及引用类型）变量进行操作时，具有排他性，即当多个线程同时对该变量的值进行更新时，仅有一个线程能成功，而未成功的线程可以向自旋锁一样，继续尝试，一直等到执行成功。

```java
// setup to use Unsafe.compareAndSwapInt for updates（更新操作时提供“比较并替换”的作用）
private static final Unsafe unsafe = Unsafe.getUnsafe();
private static final long valueOffset;

static {
	try {
		valueOffset = unsafe.objectFieldOffset
		(AtomicInteger.class.getDeclaredField("value"));
	} catch (Exception ex) { throw new Error(ex); }
}

private volatile int value;
```

AtomicInteger 类主要利用 CAS (compare and swap) + volatile 和 native 方法来保证原子操作，从而避免 synchronized 的高开销，执行效率大为提升。

CAS的原理是拿期望的值和原本的一个值作比较，如果相同则更新成新的值。UnSafe 类的 objectFieldOffset() 方法是一个本地方法，这个方法是用来拿到“原来的值”的内存地址，返回值是 valueOffset。另外 value 是一个volatile变量，在内存中可见，因此 JVM 可以保证任何时刻任何线程总能拿到该变量的最新值。

## 并发工具

### CountDownLatch与CyclicBarrier

CountDownLatch与CyclicBarrier都是用于控制并发的工具类，都可以理解成维护的就是一个计数器，但是这两者还是各有不同侧重点的：

- CountDownLatch一般用于某个线程A等待若干个其他线程执行完任务之后，它才执行；
- CyclicBarrier一般用于一组线程互相等待至某个状态，然后这一组线程再同时执行；
- CountDownLatch强调一个线程等多个线程完成某件事情。
- CyclicBarrier是多个线程互等，等大家都完成，再携手共进。
- 调用CountDownLatch的countDown方法后，当前线程并不会阻塞，会继续往下执行；
- 调用CyclicBarrier的await方法，会阻塞当前线程，直到CyclicBarrier指定的线程全部都到达了指定点的时候，才能继续往下执行；
- CountDownLatch方法比较少，操作比较简单，
- CyclicBarrier提供的方法更多，比如能够通过getNumberWaiting()，isBroken()这些方法获取当前多个线程的状态，并且CyclicBarrier的构造方法可以传入barrierAction，指定当所有线程都到达时执行的业务功能；
- CountDownLatch是不能复用的
- CyclicLatch是可以复用的。

### Semaphore与Exchanger

# Java内存区域

### JVM 的主要组成部分及其作用

![img](https://img-blog.csdnimg.cn/20200103213149526.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly90aGlua3dvbi5ibG9nLmNzZG4ubmV0,size_16,color_FFFFFF,t_70)

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020031416414486.jpeg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RoaW5rV29u,size_16,color_FFFFFF,t_70)

![img](https://snailclimb.gitee.io/javaguide/docs/java/jvm/pictures/java%E5%86%85%E5%AD%98%E5%8C%BA%E5%9F%9F/2019-3Java%E8%BF%90%E8%A1%8C%E6%97%B6%E6%95%B0%E6%8D%AE%E5%8C%BA%E5%9F%9FJDK1.8.png)

JVM包含两个子系统和两个组件

* 两个子系统
  * Class loader(类装载器)
  * Execution engine(执行引擎)
* 两个组件
  * Native Interface(本地接口)
  * Runtime data area(运行时数据区)
    * 程序计数器（Program Counter Register）
      * 当前线程所执行的字节码的行号指示器，字节码解析器的工作是通过改变这个计数器的值，来选取下一条需要执行的字节码指令，分支、循环、跳转、异常处理、线程恢复等基础功能，都需要依赖这个计数器来完成；
      * 程序计数器是唯一一个不会出现 `OutOfMemoryError` 的内存区域，它的生命周期随着线程的创建而创建，随着线程的结束而死亡
    * Java 虚拟机栈（Java Virtual Machine Stacks）
      * Java 虚拟机栈是由一个个栈帧组成，而每个栈帧中都拥有：局部变量表、操作数栈、动态链接、方法出口信息
      * **Java 虚拟机栈会出现两种错误**
        * **`StackOverFlowError`** : 若 Java 虚拟机栈的内存大小不允许动态扩展，那么当线程请求栈的深度超过当前 Java 虚拟机栈的最大深度的时候，就抛出 StackOverFlowError 错误
        * **`OutOfMemoryError`**:若 Java 虚拟机堆中没有空闲内存，并且垃圾回收器也无法提供更多内存的话。就会抛出 OutOfMemoryError 错误
    * 本地方法栈（Native Method Stack）
      * 与虚拟机栈的作用是一样的，只不过虚拟机栈是服务 Java 方法的，而本地方法栈是为虚拟机调用 Native 方法服务的；
    * Java 堆（Java Heap）
      * Java 虚拟机中内存最大的一块，是被所有线程共享的，几乎所有的对象实例都在这里分配内存；
    * 方法区（Methed Area）
      * 用于存储已被虚拟机加载的类信息、常量、静态变量、即时编译后的代码等数据。
      * 方法区也被称为永久代
      * **Non-Heap（非堆）**

- Class loader(类装载)：根据给定的全限定名类名(如：java.lang.Object)来装载class文件到Runtime data area中的method area。
- Execution engine（执行引擎）：执行classes中的指令。
- Native Interface(本地接口)：与native libraries交互，是其它编程语言交互的接口。
- Runtime data area(运行时数据区域)：这就是我们常说的JVM的内存。

**作用** ：首先通过编译器把 Java 代码转换成字节码，类加载器（ClassLoader）再把字节码加载到内存中，将其放在运行时数据区（Runtime data area）的方法区内，而字节码文件只是 JVM 的一套指令集规范，并不能直接交给底层操作系统去执行，因此需要特定的命令解析器执行引擎（Execution Engine），将字节码翻译成底层系统指令，再交由 CPU 去执行，而这个过程中需要调用其他语言的本地库接口（Native Interface）来实现整个程序的功能。

### 深拷贝和浅拷贝

浅拷贝（shallowCopy）只是增加了一个指针指向已存在的内存地址，

深拷贝（deepCopy）是增加了一个指针并且申请了一个新的内存，使这个增加的指针指向这个新的内存，

使用深拷贝的情况下，释放内存的时候不会因为出现浅拷贝时释放同一个内存的错误。

浅复制：仅仅是指向被复制的内存地址，如果原地址发生改变，那么浅复制出来的对象也会相应的改变。

深复制：在计算机中开辟一块**新的内存地址**用于存放复制的对象。

### 说一下堆栈的区别

|              | 堆                                                           | 栈                                                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 物理地址     | 堆的物理地址分配对对象是不连续的。因此性能慢些。在GC的时候也要考虑到不连续的分配，所以有各种算法。比如，标记-消除，复制，标记-压缩，分代（即新生代使用复制算法，老年代使用标记——压缩） | 数据结构中的栈，先进后出的原则，物理地址分配是连续的。所以性能快。 |
| 内存分别     | 为是不连续的，所以分配的内存是在`运行期`确认的，因此大小不固定。一般堆大小远远大于栈 | 栈是连续的，所以分配的内存大小要在`编译期`就确认，大小是固定的。 |
| 存放的内容   | 存放的是对象的实例和数组。因此该区更关注的是数据的存储       | 存放：局部变量，操作数栈，返回结果。该区更关注的是程序方法的执行。 |
| 程序的可见度 | 堆对于整个应用程序都是共享、可见的                           | 栈只对于线程是可见的。所以也是线程私有。他的生命周期和线程相同。 |

|            | 队列                                       | 栈                                                 |
| ---------- | ------------------------------------------ | -------------------------------------------------- |
| 操作的名称 | 队列的插入称为入队，队列的删除称为出队     | 栈的插入称为进栈，栈的删除称为出栈                 |
| 操作的方式 | 队列是在队尾入队，队头出队，即两边都可操作 | 进栈和出栈都是在栈顶进行的，无法对栈底直接进行操作 |
| 操作的方法 | 队列是先进先出（FIFO）                     | 栈为后进先出（LIFO）                               |

# 堆

1. 新生代内存(Young Generation)
2. 老生代(Old Generation)
3. 永生代(Permanent Generation)

![JVM堆内存结构-JDK8](https://snailclimb.gitee.io/javaguide/docs/java/jvm/pictures/java%E5%86%85%E5%AD%98%E5%8C%BA%E5%9F%9F/JVM%E5%A0%86%E5%86%85%E5%AD%98%E7%BB%93%E6%9E%84-jdk8.png)

**示的 Eden 区、两个 Survivor 区都属于新生代（为了区分，这两个 Survivor 区域按照顺序被命名为 from 和 to），中间一层属于老年代。**

堆这里最容易出现的就是 OutOfMemoryError 错误，并且出现这种错误之后的表现形式还会有几种，比如：

1. **`OutOfMemoryError: GC Overhead Limit Exceeded`** ： 当JVM花太多时间执行垃圾回收并且只能回收很少的堆空间时，就会发生此错误。
2. **`java.lang.OutOfMemoryError: Java heap space`** :假如在创建新的对象时, 堆内存中的空间不足以存放新创建的对象, 就会引发`java.lang.OutOfMemoryError: Java heap space` 错误。(和本机物理内存无关，和你配置的内存大小有关！)

# 内存溢出异常

## Java会存在内存泄漏吗

内存泄漏是指不再被使用的对象或者变量一直被占据在内存中。理论上来说，Java是有GC垃圾回收机制的，也就是说，不再被使用的对象，会被GC自动回收掉，自动从内存中清除。

但是，即使这样，Java也还是存在着内存泄漏的情况，java导致内存泄露的原因很明确：长生命周期的对象持有短生命周期对象的引用就很可能发生内存泄露，尽管短生命周期对象已经不再需要，但是因为长生命周期对象持有它的引用而导致不能被回收，这就是java中内存泄露的发生场景。

# 垃圾收集器

### 简述Java垃圾回收

在java中，程序员是不需要显示的去释放一个对象的内存的，而是由虚拟机自行执行。在JVM中，有一个垃圾回收线程，它是低优先级的，在正常情况下是不会执行的，只有在虚拟机空闲或者当前堆内存不足时，才会触发执行，扫面那些没有被任何引用的对象，并将它们添加到要回收的集合中，进行回收。

### GC

GC 是垃圾收集的意思（Gabage Collection）,内存处理是编程人员容易出现问题的地方，忘记或者错误的内存

回收会导致程序或系统的不稳定甚至崩溃，Java 提供的 GC 功能可以自动监测对象是否超过作用域从而达到自动

回收内存的目的，Java 语言没有提供释放已分配内存的显示操作方法。

### 原理

垃圾回收器通常作为一个单独的低级别的线程运行，在不可预知的情况下对内存堆中已经死亡的或很长时间没有用过的对象进行清除和回收。

垃圾回收有分代复制垃圾回收、标记垃圾回收、增量垃圾回收。

对于GC来说，创建对象时，GC就开始监控这个对象的地址、大小以及使用情况。

通常，GC采用有向图的方式记录和管理堆(heap)中的所有对象。通过这种方式确定哪些对象是"可达的"，哪些对象是"不可达的"。当GC确定一些对象为"不可达"时，GC就有责任回收这些内存空间。

# HotSpot虚拟机对象

## 对象的创建

![Java创建对象的过程](https://snailclimb.gitee.io/javaguide/docs/java/jvm/pictures/java%E5%86%85%E5%AD%98%E5%8C%BA%E5%9F%9F/Java%E5%88%9B%E5%BB%BA%E5%AF%B9%E8%B1%A1%E7%9A%84%E8%BF%87%E7%A8%8B.png)

### Step1:类加载检查

虚拟机遇到一条 new 指令时，首先将去检查这个指令的参数是否能在常量池中定位到这个类的符号引用，并且检查这个符号引用代表的类是否已被加载过、解析和初始化过。如果没有，那必须先执行相应的类加载过程。

### Step2:分配内存

在**类加载检查**通过后，接下来虚拟机将为新生对象**分配内存**。

对象所需的内存大小在类加载完成后便可确定，为对象分配空间的任务等同于把一块确定大小的内存从 Java 堆中划分出来。

**分配方式**有 **“指针碰撞”** 和 **“空闲列表”** 两种，**选择哪种分配方式由 Java 堆是否规整决定，而 Java 堆是否规整又由所采用的垃圾收集器是否带有压缩整理功能决定**。

选择以上两种方式中的哪一种，取决于 Java 堆内存是否规整。而 Java 堆内存是否规整，取决于 GC 收集器的算法是"标记-清除"，还是"标记-整理"（也称作"标记-压缩"），值得注意的是，复制算法内存也是规整的

![image-20200824194144806](https://raw.githubusercontent.com/YangAnLin/images/master/20200824194149.png)

**内存分配并发问题**

在创建对象的时候有一个很重要的问题，就是线程安全，因为在实际开发过程中，创建对象是很频繁的事情，作为虚拟机来说，必须要保证线程是安全的，通常来讲，虚拟机采用两种方式来保证线程安全：

- **CAS+失败重试：** CAS 是乐观锁的一种实现方式。所谓乐观锁就是，每次不加锁而是假设没有冲突而去完成某项操作，如果因为冲突失败就重试，直到成功为止。**虚拟机采用 CAS 配上失败重试的方式保证更新操作的原子性。**
- **TLAB：** 为每一个线程预先在 Eden 区分配一块儿内存，JVM 在给线程中的对象分配内存时，首先在 TLAB 分配，当对象大于 TLAB 中的剩余内存或 TLAB 的内存已用尽时，再采用上述的 CAS 进行内存分配

### Step3:初始化零值

内存分配完成后，虚拟机需要将分配到的内存空间都初始化为零值（不包括对象头），这一步操作保证了对象的实例字段在 Java 代码中可以不赋初始值就直接使用，程序能访问到这些字段的数据类型所对应的零值。

### Step4:设置对象头

初始化零值完成之后，**虚拟机要对对象进行必要的设置**，例如这个对象是哪个类的实例、如何才能找到类的元数据信息、对象的哈希码、对象的 GC 分代年龄等信息。 **这些信息存放在对象头中。** 另外，根据虚拟机当前运行状态的不同，如是否启用偏向锁等，对象头会有不同的设置方式。

### Step5:执行 init 方法

在上面工作都完成之后，从虚拟机的视角来看，一个新的对象已经产生了，但从 Java 程序的视角来看，对象创建才刚开始，`<init>` 方法还没有执行，所有的字段都还为零。所以一般来说，执行 new 指令之后会接着执行 `<init>` 方法，把对象按照程序员的意愿进行初始化，这样一个真正可用的对象才算完全产生出来。

## 对象的访问定位

建立对象就是为了使用对象，我们的 Java 程序通过栈上的 reference 数据来操作堆上的具体对象。对象的访问方式由虚拟机实现而定，目前主流的访问方式有**①使用句柄**和**②直接指针**两种：

**句柄：** 如果使用句柄的话，那么 Java 堆中将会划分出一块内存来作为句柄池，reference 中存储的就是对象的句柄地址，而句柄中包含了对象实例数据与类型数据各自的具体地址信息

**直接指针：** 如果使用直接指针访问，那么 Java 堆对象的布局中就必须考虑如何放置访问类型数据的相关信息，而 reference 中存储的直接就是对象的地址。

![image-20200824194559886](https://raw.githubusercontent.com/YangAnLin/images/master/20200824194601.png)

# CPU占用率高查看

## 第一种方法

```shell
# 1.查看java的pid
jps

# 2.查看各个子进程
top -H -p pid

# 3.显示16进制
printf "%x\n" 子pid

jstack pid |grep tid(第3步出来的) -A 30
```

## 第二种方法:show-busy-java-threads

https://github.com/oldratlee/useful-scripts/blob/dev-2.x/bin/show-busy-java-threads

使用的命令:

```shell
sh show-busy-java-threads.sh -p pid
```