# Mybatis使用总结
> 写于：2018-05-11

### config配置
```
<?xml version="1.0" encoding="utf-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--根据官网配置 http://www.mybatis.org/mybatis-3/zh/configuration.html#settings -->
    <settings>
        <!--配置缓存-->
        <setting name="cacheEnabled" value="false"/>
        <!--开启驼峰转换-->
        <setting name="mapUnderscoreToCamelCase" value="true"></setting>
        <!--自动生成主键-->
        <setting name="useGeneratedKeys" value="true"></setting>
        <!--配置Executor的执行类型-->
        <setting name="defaultExecutorType" value="REUSE"></setting>
        <!--事物,单位秒-->
        <setting name="defaultStatementTimeout" value="600"></setting>
    </settings>

    <!--全局别名配置-->
    <typeAliases>
        <typeAlias type="com.imooc.house.model.User" alias="user"></typeAlias>
        <typeAlias type="com.imooc.house.model.House" alias="house"></typeAlias>
    </typeAliases>

    <!--引入mapper-->
    <mappers>
        <mapper resource="mapper/user.xml" />
        <mapper resource="mapper/house.xml" />
    </mappers>
</configuration>
```
### 注解
**@Param** 传参
```
// 在xml中就可以获取对象的字段了
public List<House> selectHouses(@Param("house") House house,
        @Param("pageParams") PageParams pageParams);
```

### 标签

**sql标签** 将重用的抽出来复用
```
<!--sql 这个标签好用，如果复用的话就可以替换-->
<sql id="houseField">
    a.id,
    a.name,
    a.type
   //需要返回的字段
</sql>
<select id="selectHouses" resultType="house">
    SELECT <include refid="houseField"></include>
    FROM house
    <if test="pageParams.offset != null and pageParams.limit != null">
        limit #{pageParams.offset},#{pageParams.limit}
    </if>
    <if test="pageParams.offset == null and pageParams.limit != null">
        limit #{pageParams.limit}
    </if>
</select>
```