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
