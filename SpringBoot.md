### 2.静态变量注入
```properties
application.properties中配置下面两个配置项
ccb.ip.address=10.25.177.31
ccb.ip.port=1600
下面问题代码中读取不到application.properties配置文件中的配置
```
```java
@Component
public class BISFrontFileUtil {
    private static Logger logger = LoggerFactory.getLogger(BISFrontFileUtil.class);

    private static String CCBIPADDRESS;

    private static int CCBIPPORT;

    @Value("${ccb.ip.address}")
    public void setCCBIPADDRESS(String cCBIPADDRESS) {
        CCBIPADDRESS = cCBIPADDRESS;
    }

    @Value("${ccb.ip.port}")
    public void setCCBIPPORT(int cCBIPPORT) {
        CCBIPPORT = cCBIPPORT;
    }

}
```
注意:
1. 修正代码中的@Component不可丢掉了
2. set方法要是非静态的