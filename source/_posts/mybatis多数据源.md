---
title: mybatis多数据源
date: 2018-05-11 15:50:06
categories: 
- [Web开发]
---

*对于多数据源，一般都是问了解决主从模式或者业务相对复杂需要分库来支持业务的场景。搜索解决方案，要么是采用spring多数据源的方案，要么是利用aop动态切换，感觉有点小复杂，本文采用一种相对简单的解决方案，配置过程中遇到一些问题，特以此文记录*

#### 配置yml

```yaml
############################# Multiple DataSource
multi:
  datasource:
    enabled: true #控制是否开启多数据源
    druid:
      master:
        url: jdbc:mysql://ip:port/master?useUnicode=true&characterEncoding=utf-8&characterSetResults=utf8&autoReconnect=true&failOverReadOnly=false&useSSL=false
        username: root
        password: root
        driver-class-name: com.mysql.jdbc.Driver
      slave:
        url: jdbc:mysql://ip:port/slave?useUnicode=true&characterEncoding=utf-8&characterSetResults=utf8&autoReconnect=true&failOverReadOnly=false&useSSL=false
        username: root
        password: root
        driver-class-name: com.mysql.jdbc.Driver
    mybatis:
      master:
        base-packages: com.fly.mapper.master
        mapper-locations: classpath:mapper/master/*.xml
      slave:
        base-packages: com.fly.mapper.slave
        mapper-locations: classpath:mapper/slave/*.xml
        
################################## mybaits
mybatis:
  type-aliases-package: com.fly.bean.mysql
  # 配置
  configuration:
    cache-enabled: true #  全局映射器启用缓存
    lazy-loading-enabled: true # 查询时，关闭关联对象即时加载以提高性能
    aggressive-lazy-loading: false # 设置关联对象加载的形态，此处为按需加载字段(加载字段由SQL指定)，不会加载关联表的所有字段，以提高性能 -->
    multiple-result-sets-enabled: true # 对于未知的SQL查询，允许返回不同的结果集以达到通用的效果 -->
    use-column-label: true # 允许使用列标签代替列名 -->
    use-generated-keys: true # 允许使用自定义的主键值(比如由程序生成的UUID 32位编码作为键值)，数据表的PK生成策略将被覆盖 -->
    auto-mapping-behavior: full # 给予被嵌套的resultMap以字段-属性的映射支持 -->
    default-executor-type: batch # 对于批量更新操作缓存SQL以提高性能  -->
    default-statement-timeout: 25000 # 数据库超过25000秒仍未响应则超时 -->
    mapUnderscoreToCamelCase: true # mybatis 驼峰命名 -->
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl # 命令行输出 -->
```

#### 配置数据源

```java
/**
 * 多数据源
 * 1. mybatis-spring-boot-starter的MybatisAutoConfiguration不支持Multiple DataSource
 *    仅支持Single DataSource或@Primary DataSource生效
 * 2. 多数据源的配置必须要配置主库@Primary否则报错
 *    或者@EnableAutoConfiguration(exclude = MybatisAutoConfiguration.class)禁止AutoConfig
 * 3. 多数据源采用主，副库的配置方式，通过不同包进行隔离
 */
@org.springframework.context.annotation.Configuration
@ConditionalOnProperty(name = "multi.datasource.enabled", havingValue = "true")
public class MultiDataSourceConfig {
    /**
     * *******************************************************************
     * 主数据源配置 @Primary
     * *******************************************************************
     */
    @org.springframework.context.annotation.Configuration
    @ConditionalOnProperty(name = "multi.datasource.enabled", havingValue = "true")
    @MapperScan(
            basePackages = "com.fly.mapper.master",
            sqlSessionFactoryRef = "masterSqlSessionFactory"
    )
    @Getter
    @Setter
    @ConfigurationProperties(
            prefix = "multi.datasource.mybatis.master"
    )
    static class MasterDataSourceConfig {

        @javax.annotation.Resource
        MybatisConfigurationSupport support;

        private String[] mapperLocations;

        /**
         * Primary Druid DataSource
         * @return
         */
        @ConfigurationProperties(
                prefix = "multi.datasource.druid.master"
        )
        @Primary
        @Bean
        public DataSource masterDataSource() {
            return DruidDataSourceBuilder
                    .create()
                    .build();
        }

        @Bean
        @Primary
        public SqlSessionFactoryBean masterSqlSessionFactory(
                @Qualifier("masterDataSource") DataSource dataSource
        ) throws Exception {
            return support.createSqlSessionFactoryBean(dataSource, mapperLocations);
        }

    }

    /**
     * *******************************************************************
     * 从数据源配置
     * *******************************************************************
     */
    @org.springframework.context.annotation.Configuration
    @ConditionalOnProperty(name = "multi.datasource.enabled", havingValue = "true")
    @MapperScan(
            basePackages = "com.fly.mapper.slave",
            sqlSessionFactoryRef = "slaveSqlSessionFactory"
    )
    @Getter
    @Setter
    @ConfigurationProperties(
            prefix = "multi.datasource.mybatis.slave"
    )
    static class SlaveDataSourceConfig {
        private String[] mapperLocations;

        @javax.annotation.Resource
        MybatisConfigurationSupport support;

        /**
         * Slave Druid DataSource
         * @return
         */
        @ConfigurationProperties(
                prefix = "multi.datasource.druid.slave"
        )
        @Bean
        public DataSource slaveDataSource() {
            return DruidDataSourceBuilder
                    .create()
                    .build();
        }

        @Bean
        public SqlSessionFactoryBean slaveSqlSessionFactory(
                @Qualifier("slaveDataSource") DataSource dataSource
        ) throws Exception {
            return support.createSqlSessionFactoryBean(dataSource, mapperLocations);
        }
    }

    /**
     * *******************************************************************
     * 用于简化MybatisProperties的配置
     * *******************************************************************
     */
    @Component
    @ConditionalOnProperty(name = "multi.datasource.enabled", havingValue = "true")
    static class MybatisConfigurationSupport {
        private final MybatisProperties properties;
        private final ResourceLoader resourceLoader;

        public MybatisConfigurationSupport(
                MybatisProperties properties,
                ResourceLoader resourceLoader) {
            this.properties = properties;
            this.resourceLoader = resourceLoader;
        }

        /**
         * 创建SqlSessionFactoryBean
         * @param dataSource
         * @param mapperLocations
         * @return
         */
        public SqlSessionFactoryBean createSqlSessionFactoryBean(DataSource dataSource, String[] mapperLocations) {
            SqlSessionFactoryBean factory = new SqlSessionFactoryBean();
            factory.setDataSource(dataSource);
            factory.setVfs(SpringBootVFS.class);

            /**
             * 处理Configuration
             * 必须Copy否则会抛出配置多个分页插件的异常
             */
            Configuration configuration = new Configuration();
            if (properties.getConfiguration() != null) {
                // 必须Copy否则会抛出配置多个分页插件的异常
                BeanUtils.copyProperties(properties.getConfiguration(), configuration);
            }
            factory.setConfiguration(configuration);

            // 处理MapperLocations
            if (!ObjectUtils.isEmpty(mapperLocations)) {
                factory.setMapperLocations(resolveMapperLocations(mapperLocations));
            }

//            if (StringUtils.hasLength(properties.getTypeAliasesPackage())) {
//                factory.setTypeAliasesPackage(properties.getTypeAliasesPackage());
//            }

            return factory;
        }

        /**
         * MapperLocations 转化为Resource[]
         * @param mapperLocations
         * @return
         */
        public Resource[] resolveMapperLocations(String[] mapperLocations) {
            ResourcePatternResolver resourceResolver = new PathMatchingResourcePatternResolver();
            List<Resource> resources = new ArrayList<>();
            if (mapperLocations != null) {
                for (String mapperLocation : mapperLocations) {
                    try {
                        Resource[] mappers = resourceResolver.getResources(mapperLocation);
                        resources.addAll(Arrays.asList(mappers));
                    } catch (IOException ignore) {
                        // ignore
                        ignore.printStackTrace();
                    }
                }
            }
            return resources.toArray(new Resource[resources.size()]);
        }
    }
}
```

#### 参考链接

- [mybatis-spring-boot-multi-ds-demo](https://github.com/kazuki43zoo/mybatis-spring-boot-multi-ds-demo)
- [Multi Datasource](https://github.com/mybatis/spring-boot-starter/issues/78)