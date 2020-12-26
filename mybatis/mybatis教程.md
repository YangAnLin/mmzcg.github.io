



# 1.一张表是一个对象,如果插入的是对象,有的属性没有赋值,update了之后,相应的字段就会变成null

所以要写成这样的方式



# 2.如果需要更新的是两个字段,如果两个字段分别是Integer 和 String 就不要写parametype 因为在mapper接口里已经写好了

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226165037.png)

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226165047.png)



# 3.要注意Mapper层需要@Mapper注解

# 4.Provider的使用

​	1.一定要注意type的类名.class 和 method方法名,还要注意形参也得是一样的

​	2.Provider的方法,大概就三个方法sql.SELECT,sql.WHERE,sql.FROM

​	3.SQL 对象里的方法名跟别的不一样,小写的不行,idea也识别不到,要用大写,比如SLELECT

​	4.Provider里返回的是String

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226165256.png)

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226165059.png)



# 5.动态SQL

@Insert ：@InsertProvider

@Select ：@SelectProvider

@Update ：@UpdateProvider

@Delete ：@DeleteProvider



四个provider注解标识 使用了动态SQL, 使用语法格式 ：

```java
@UpdateProvider(type = UserProvider.class, method = "updateSQL")
```



# 6.mybatis特殊符号处理

1.mybatis中，使用到大于号，小于号，所以就在SQL中直接使用了

```sql
SELECT
    *
FROM
    test
WHERE
    1 = 1
AND start_date <= CURRENT_DATE
AND end_date >= CURRENT_DATE
```

2.可是，在执行时，总报错误

```tex
Error creating document instance.  Cause: org.xml.sax.SAXParseException; lineNumber: 74; columnNumber: 17; 元素内容必须由格式正确的字符数据或标记组成。
```

3.符号换成这样,就可以

```sql
SELECT * FROM test WHERE 1 = 1 AND start_date  &lt;= CURRENT_DATE AND end_date &gt;= CURRENT_DATE
```
4.XML转义字符
| &lt;   | <    | 小于号 |
| ------ | ---- | ------ |
| &gt;   | >    | 大于号 |
| &amp;  | &    | 和     |
| &apos; | ’    | 单引号 |
| &quot; | "    | 双引号 |



# 7.注解 一对多查询(多个参数)

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226165112.png)

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226165121.png)

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226165132.png)

# 8.注解 一对多查询(多个参数)

```java
@SelectProvider(method="queryPageFloors",type=BarterGroupProvider.class)
@Results({
        @Result(property = "userId",column="user_id"),
        @Result(property = "floorNum",column="floor_num"),
        @Result(property = "floorSecond",column="id"),
        @Result(property = "say",column="note"),
        @Result(property = "joinOrPublish",column="join_or_publish"),
        @Result(property="categorys",javaType = List.class,column="id",many=@Many(select="com.ecloud.hobay.marketing.service.infrastructure.mapper.bartergroup.BarterGroupMapper.queryCategory")),
        @Result(property="productIds",javaType = List.class,column="id",many=@Many(select="com.ecloud.hobay.marketing.service.infrastructure.mapper.bartergroup.BarterGroupMapper.queryProducts")),
        @Result(property="beforeResult",javaType = List.class,column="id",many=@Many(select="com.ecloud.hobay.marketing.service.infrastructure.mapper.bartergroup.BarterGroupEvaluationMapper.queryAll")),
        @Result(property="barterGroupUser",javaType = BarterGroupUser.class,column="{user_id=user_id,id = barter_group_id }",many=@Many(select="com.ecloud.hobay.marketing.service.infrastructure.mapper.bartergroup.BarterGroupMapper.isHead")),
        @Result(property="futureResultNum",column="id",many=@Many(select="com.ecloud.hobay.marketing.service.infrastructure.mapper.bartergroup.BarterGroupMapper.futureResultNum"))
})
List<QueryFloors> queryFloors3(Page<QueryFloors> page);

/**
 * 分类
 * @param id
 * @return
 */
@Select("SELECT * FROM ecloud_marketing.barter_group_category WHERE barter_group_details_id = #{id} limit 0,7 ")
List<BarterGroupCategory> queryCategory(@Param("id") Long id);

/**
 * 产品
 * @param id
 * @return
 */
@Select("SELECT product_id from ecloud_marketing.barter_group_product_category WHERE barter_group_details_id = #{id} limit 0,3 ")
List<Long> queryProducts(@Param("id") Long id);


/**
 * 团员
 * @param userId
 * @param id
 * @return
 */
@Select("SELECT * FROM ecloud_marketing.barter_group_user WHERE user_id =#{user_id} and barter_group_id = #{id} and status = 1")
BarterGroupUser isHead(Map<String,Object> map);

@Select("SELECT count(*) from ecloud_marketing.barter_group_evaluation WHERE barter_group_details_id = #{id}")
Integer futureResultNum(@Param("id") Long id);
```

```java
public class QueryFloors implements Serializable {
 
 
    @ApiModelProperty("我有的产品的id")
    List<ProductBarter> list;
 
    @ApiModelProperty("封装前的评论数据")
    private List<BarterGroupEvaluation> beforeResult;
 
    @ApiModelProperty("封装后的评论数据有分页给功能")
    private Page<BarterGroupEvaluation> beforeResultPage;
 
    @ApiModelProperty("封装后的评论数据")
    private List<BarterGroupEvaluationResult> futureResult;
 
    @ApiModelProperty("剩余评论条数")
    private Integer futureResultNum;
 
    @ApiModelProperty("分类")
    List<BarterGroupCategory> categorys;
 
    @ApiModelProperty(value="楼层数")
    private Integer floorNum;
 
    @ApiModelProperty(value="楼层id")
    private Long floorSecond;
 
    @ApiModelProperty(value="要说的")
    private String say;
 
    @ApiModelProperty(value="加入或者是发布")
    private Integer joinOrPublish;
 
    @ApiModelProperty("会员表")
    private BarterGroupUser barterGroupUser;
 
    @ApiModelProperty(value="加入时间")
    private Long joinData;
}
```



# 9. **注解 一对多查询(一个参数)**

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226165320.png)

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226180342.png)



# 10.xml  for循环

```xml
<if test=" onlineState!=null and onlineState!='' and onlineState == 1 "> <!-- 在线账号 -->
    and ac.account in (<foreach collection="accountList" index="index" item="item" open="" separator="," close="">
        #{item}
    </foreach>)
</if>
<if test=" onlineState!=null and onlineState!='' and onlineState == 2 "> <!-- 离线账号 -->
    and ac.account not in (<foreach collection="accountList" index="index" item="item" open="" separator="," close="">
        #{item}
    </foreach>)
</if>
<if test=" identity!=null and identity!='' "> <!-- 是否正式账号-->
    and ac.identity = #{identity}
</if>
<choose>
    <when test="accountType!=null and accountType==11 and myId!=null and myId!=''">
        and ac.id&lt;&gt;#{myId} and ac.account_type = #{accountType}
    </when>
    <otherwise>
        <if test=" accountType!=null and accountType!='' ">
            and ac.account_type = #{accountType}
        </if>
    </otherwise>
</choose>
```



# Finally.使用Mybatis的注意事项

## 1.查询最大的时候,返回的影响行数,最好是Ineger 免得为null的时候报错

## 2.insert的插入，需要用到对象

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226165420.png)

## 3.keyProperty的使用

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226165504.png)

`keyProperty="id"` 这样就会报错

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226165453.png)

`keyProperty="agentAreaId"`  这样才是对的

或者是传入一个对象的话,就可以写id,因为mybatis传送的是对象的话,就会返回keyProperty,在传入的对象中去找,所以上面报错,就是因为,传入的参数里没有id

void saveAreas(对象 对象名);

## 4.in的问题,#号不起作用

![](https://cdn.jsdelivr.net/gh/YangAnLin/images/copy_20201226165525.png)