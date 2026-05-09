## 消息转换器
*** 
每日必看理解
***
```java
    /**
     * 消息转换器配置重写
     * @param converters the list of configured converters to extend
     */
    @Override
    protected void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        log.info("正在配置消息转换器");
        MappingJackson2HttpMessageConverter converter =new MappingJackson2HttpMessageConverter();
        converter.setObjectMapper(new JacksonObjectMapper());
        converters.add(0,converter);

    }
```
#### 理解:
首先HTTPmessageconverter是一个集合,也就是翻译官集合,然后调用一个对象叫做新的翻译官也就是这个Mappingjason格式转化成mess格式的对象,这个converters里面装的全是里面的集合;
### 自己遇到的问题
首先我的日志写的非常不清晰,导致有时候运行,报错查找原因十分缓慢,需要借助Gemini才能,日志很重要需要时时刻刻都需要写
养成看报错日志的抓手思维：遇到 SQL 相关的异常：

第一步看 ==> Preparing:：检查最终拼接的 SQL 骨架是否残缺。

第二步看 ==> Parameters:：核对传入的参数值是否符合预期。本次正是通过日志发现 Parameters: null 才精准定位到了 Java 层的对象传参漏洞
## 关于xml语句语法
****
```xml
MyBatis 核心 XML 标签速查 (全局总览)
在日常开发中，MyBatis 通过标签将 SQL 语句动态化。我们需要熟练掌握以下三类核心标签：

1. 基础 CRUD 标签
<select>：用于查询，必须配置 resultType（返回单行结果的类型）或 resultMap（复杂映射）。

<insert>：用于插入。结合 useGeneratedKeys="true" keyProperty="id" 可以实现主键返回。

<update>：用于更新操作。

<delete>：用于删除操作。

2. 动态 SQL 标签 (最常用)
<if test="...">：条件判断。如果 test 里的表达式为 true，则拼接标签内的 SQL。

<where>：智能 WHERE 关键字。它会自动移除内部 SQL 语句开头多余的 AND 或 OR。

<set>：智能 SET 关键字。用于 UPDATE 语句，它会自动移除内部 SQL 语句末尾多余的逗号 ,。

<foreach>：用于遍历集合（如 List 或数组），常用于 IN 查询或批量插入。

核心属性：collection (集合名), item (元素别名), separator (分隔符), open (前缀), close (后缀)。

3. 分支选择标签 (类似 switch-case)
<choose>、<when>、<otherwise>：当需要互斥的条件时使用（只会执行其中一个满足条件的逻辑）。
```
####样例一
```xml
<update id="updateEmployee" parameterType="com.sky.entity.Employee">
<!-- 注意包名,还有就是自己的更新的名字叫什么,比如这个 updateEmployee,要根据自己的什么来,要根据自己的这个mapper接口来写-->
    update employee
    <set>
        <if test="username != null"> username = #{username}, </if>
        <if test="password != null"> password = #{password}, </if>
        <!-- 注意：数据库字段是下划线，Java 属性是驼峰 -->
        <if test="idNumber != null"> id_number = #{idNumber}, </if>
        <if test="updateTime != null"> update_time = #{updateTime}, </if>
    </set>
    where id = #{id}
</update>