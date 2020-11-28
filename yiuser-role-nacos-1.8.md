# 前言
Sentinel Dashboard管理界面配置的规则默认存储在内存，Sentinel Dashboard一旦重启，配置规则就会消失，不适用于生成环境。

以下对`Sentinel Dashboard`的源码进行进行修改，实现规则持久化至`nacos`中，基于`sentinel 1.8`版本的master分支修改

# 1.改造Sentinel-Dashboard
## 1.1 修改依赖
修改sentinel-dashboard模块的pom.xml，将依赖`sentinel-datasource-nacos`的test注释掉
```
<!-- for Nacos rule publisher sample -->
<dependency>
    <groupId>com.alibaba.csp</groupId>
    <artifactId>sentinel-datasource-nacos</artifactId>
    <!--<scope>test</scope>-->
</dependency>
```
## 1.2 修改nacos相关java代码
找到如下目录
```
sentinel-dashboard/src/test/java/com/alibaba/csp/sentinel/dashboard/rule/nacos
```
将整个目录拷贝到
```
sentinel-dashboard/src/main/java/com/alibaba/csp/sentinel/dashboard/rule/nacos
```

改造NacosConfig类的ConfigService bean，修改nacos相关连接配置,以便启动时可以通过JVM参数指定对应的配置项
```
@Bean
public ConfigService nacosConfigService(NacosConfigProperties configProperties) throws Exception {
    Properties properties = new Properties();
    properties.put(PropertyKeyConst.SERVER_ADDR, configProperties.getServerAddr());
    properties.put(PropertyKeyConst.NAMESPACE, configProperties.getServerAddr());
    return ConfigFactory.createConfigService(properties);
}

@Bean
@ConfigurationProperties("spring.cloud.nacos.config")
public NacosConfigProperties nacosConfigProperties() {
    return new NacosConfigProperties();
}

public static class NacosConfigProperties{
    private String serverAddr;
    private String namespace;

    public String getServerAddr() {
        return serverAddr;
    }

    public void setServerAddr(String serverAddr) {
        this.serverAddr = serverAddr;
    }

    public String getNamespace() {
        return namespace;
    }

    public void setNamespace(String namespace) {
        this.namespace = namespace;
    }
}
```
## 1.3 增加NacosProvider和NacosPublisher
```

```

