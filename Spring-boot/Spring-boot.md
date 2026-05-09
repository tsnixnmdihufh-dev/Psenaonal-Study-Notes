## Spirngboot核心框架理解
***
### 核心特性
ioc容器,Aop,事务管理,MVC框架.
那么什么是ioc呢? 那什么又是aoo?事务管理又是什么?
********

关于 IoC 控制反转，我是从传统开发的痛点和 Spring 的解决方案这两个方面来理解的。

首先，在传统的 Java 开发中，如果我们要实现一个业务，比如外卖系统里的订单服务，往往需要手动去 new 各种依赖的 Mapper 对象或者工具类。这种方式会让类与类之间高度耦合，一旦底层实现改变，上层代码就要跟着大改，后期的维护成本可以说是致命的。

而 Spring 提出 IoC 思想，就是为了解耦。它本质上是把创建对象和管理对象生命周期的‘控制权’，从我们开发者手中‘反转’交给了 Spring 的 IoC 容器。

在实际编码中，我们只需要遵循面向接口编程的原则，声明我们需要什么对象（比如通过 @Autowired 注解），Spring 容器就会像一个‘大管家’一样，自动把实例化好的对象注入给我们（也就是 DI 依赖注入）。这样一来，代码的冗余度大大降低，系统的扩展性和可维护性也得到了极大的提升。
***
## MVC理解
***
首先，我认为 MVC 和 Java 里的抽象类、接口一样，本质都是为了职责分离和解耦，方便后期代码的维护。

如果拿一个点餐系统来打比方：

C 就是 Controller 层 (控制器)：它就像是餐厅的前台服务员。它的核心职责只有两个：一是接收前端发来的 HTTP 请求并做基本的参数校验；二是把处理后的结果返回给前端。它本身不处理复杂的业务。

M 就是 Model 层 (模型)：这是系统的绝对核心，相当于餐厅的后厨。它包含了负责复杂业务逻辑的 Service 层、负责和数据库打交道的 Mapper 层，以及 Entity 实体类。前台把请求交过来后，后厨负责去数据库调取数据并完成核心的计算加工。

V 就是 View 层 (视图)：在我们目前主流的前后端分离开发中，后端的 View 层其实已经转变成了通用的 JSON 数据格式。后厨（Model）把处理好的对象交给前台（Controller），Controller 再将对象序列化为 JSON 发给前端。最终的页面解析和渲染展示，是交由前端小程序或网页来完成的
在一个餐厅里面只有mvc分工合作配合才能运营好
***

## 消息转换器
在前后端分离项目,我经常会遇到一个比较麻烦的问题,那就是数据从后端转移到前端时,由于long格式或者其他问题,前端js会因为数据长度太大,导致最后变成又或者时返还localdtaetime时候,前端之返还一个数字串
### 解决方案是什么?
有两种解决方案,首先就是直接单独的在每个controller层里面,手动配置接收数据
还有一个比较重要的解决方案就是,自己配置一个消息转换逻辑
就像这样
```java
@Configuration
@Slf4j
public class WebMvcConfiguration extends WebMvcConfigurationSupport {

    /**
     * 扩展 Spring MVC 的消息转换器
     */
    @Override
    protected void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        log.info("开始扩展自定义消息转换器...");
        
        // 1. 创建一个新的消息转换器对象（高薪聘请一位专门处理 JSON 的高级翻译官）
        MappingJackson2HttpMessageConverter converter = new MappingJackson2HttpMessageConverter();
        
        // 2. 为消息转换器设置一个对象映射器（给翻译官配备一本咱们项目特制的“翻译字典”）
        // 这里的 JacksonObjectMapper 是我们自定义的类，里面规定了 Long 转 String，日期按指定格式转换的规则
        converter.setObjectMapper(new JacksonObjectMapper());
        
        // 3. 将我们自己的转换器加入到 Spring 容器的集合中
        // 重点：必须放在索引 0 的位置，赋予最高优先级，否则会被默认转换器抢先处理
        converters.add(0, converter);
    }
}

```
我们可以核心拆解一下这段代码,形象一点

List<HttpMessageConverter<?>> converters 是什么？

在餐厅里，Spring 经理手下有一个“翻译官团队”（List 集合）。

团队里有负责翻译普通文本的，有负责翻译 XML 的，也有负责翻译 JSON 的。

这个参数 converters，就是 Spring 把现有的翻译官名册递给你，对你说：“喏，名单在这，你想怎么改，自己看着办。
这个集合就像是一个中央的集成器,控制着整个文本的信息转换的,当一条文本传进来,他会自动的调用集合里面对应的方法

代码里的 MappingJackson2HttpMessageConverter converter = new ...，意思是你自己掏钱，在外面重新高薪聘请了一个精通 JSON 的高级翻译官。他是一个干活的人（对象），不是一个动作（方法）。
也就是我重新创造一个工具,这个工具依照一个新的格式来进行解析.
converter.setObjectMapper(new JacksonObjectMapper());
将我们自己的方法或者时字典,给这个工具,或者说是翻译官,让他来使用,最后
再将翻译官添加到集合0号位置上,这样他能第一个开始翻译;这样就可以完美避开spring1的默认规则
总的来说