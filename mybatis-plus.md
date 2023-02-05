# Mybatis-plus

[TOC]

这里把用到的一些作笔记，但是具体的内容还是参照官方文档：https://baomidou.com/pages/24112f/





## @MapKey("key")

```
MapKey注解:
   1.用于返回map的key值
     通常情况下使用唯一键作为key
   2.此注解常用于dao文件上
```

用List<Map<String, Object>> 接收，也得用@mapKey不然爆红，不过此时的mapKey不起总用就是了

类似的例子

```java
@MapKey("id")
List<Map<String, String>> projectQuery(String year);
```



## @InterceptorIgnore(tenantLine = "true")

多租户屏蔽某个特定mapper的方法，则在特定mapper方法上加注解：@InterceptorIgnore(tenantLine = "true")

多租户数据隔离：https://blog.csdn.net/new_com/article/details/106693726?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-106693726-blog-112253735.pc_relevant_recovery_v2&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-106693726-blog-112253735.pc_relevant_recovery_v2&utm_relevant_index=1





## \<sql>

抽取共同是使用的sql语句，用id引用

```xml
<sql id="queryBatchManageTable">
       FROM t_pe_batch_manage a
LEFT JOIN t_pe_assessment_type b ON a.assessment_type_id_ = b.id_ AND b.delete_flag_ = '0'
</sql>

<sql id="queryBatchManageWhere">
    <where>
        AND a.delete_flag_ = '0'
        <if test="search != null and search != ''">
            AND a.name_ LIKE CONCAT('%',#{search},'%')
        </if>
        <if test="year != null and year != ''">
            AND a.year_ = #{year}
        </if>
    </where>
</sql>

<select id="countBatchManage" resultType="java.lang.Integer">
    SELECT COUNT(*)
    <include refid="queryBatchManageTable"></include>
    <include refid="queryBatchManageWhere"></include>
</select>
```

配套使用：\<include refid="">\</include>引用sql语句



## if语句要判断非空和不为null

```xml
<if test="year != null and year != ''">
	AND a.year_ = #{year}
</if>
```



## resultMap

```xml
<resultMap id="BatchManageDtoMap" type="com.string.pe.domain.dto.BatchManageDto">
    <id property="id" column="aid" jdbcType="VARCHAR"/>
    <result property="name" column="aname" jdbcType="VARCHAR"/>
    <result property="year" column="ayear" jdbcType="VARCHAR"/>
    <result property="assessmentTypeId" column="aassessmentTypeId" jdbcType="VARCHAR"/>
    <result property="assessmentTypeName" column="bassessmentTypeName" jdbcType="VARCHAR"/>
    <result property="startTime" column="astartTime" jdbcType="TIMESTAMP"/>
    <result property="endTime" column="aendTime" jdbcType="TIMESTAMP"/>
    <result property="content" column="acontent" jdbcType="VARCHAR"/>
    <result property="calculationTime" column="acalculationTime" jdbcType="TIMESTAMP"/>
    <result property="releaseState" column="areleaseState" jdbcType="TINYINT"/>
</resultMap>
```





## \<bind>使用

```xml
 <if test="vo.search != null and vo.search != ''">
        <bind value="'%' + vo.search + '%'" name="likeValue"></bind>
        and A.name_ LIKE #{likeValue}
    </if>
```





## count和case使用

```xml
select count(*),
       count(city_name),
       count(distinct city_name),
       count(case
               when xs_code like '23%' or xs_code like '24%' then
                city_name
             end),
       count(distinct case
               when xs_code like '23%' or xs_code like '24%' then
                city_name
             end)
  from tb_county
```





## LambdaQueryWrapper

```java
LambdaQueryWrapper<实例> educationGroupLambdaQueryWrapper = new LambdaQueryWrapper<实例>()
        .le(PeEducationGroup::getStartTime, formatEnd)
        .ge(PeEducationGroup::getStartTime, formatStart);
```

```
LambdaQueryWrapper<BannerItem> wrapper = new LambdaQueryWrapper<>();
wrapper.eq(BannerItem::getBannerId, id);
List<BannerItem> bannerItems = bannerItemMapper.selectList(wrapper);
```

