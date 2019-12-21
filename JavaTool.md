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

