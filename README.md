springcloud-LCN
基于springcloud的LCN分布式事务处理

TX_LCN官网地址：https：//www.txlcn.org/zh-cn/index.html

demo环境TM：1，配置TM之前需要准备中间件JRE1.8 +，Mysql5.6 +，Redis3.2 + 2，创建MySQL数据库，名称为：tx-manager sql问如下

CREATE TABLE t_tx_exception （ idbigint（20）NOT NULL AUTO_INCREMENT， group_idvarchar（64）CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL， unit_idvarchar（32）CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL， mod_idvarchar（128）CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL， transaction_statetinyint（4）NULL DEFAULT NULL， registrartinyint（4）NULL DEFAULT NULL， remarkvarchar（4096）NULL DEFAULT NULL， ex_statetinyint（4）NULL DEFAULT NULL COMMENT'0未解决1已解决'， create_timedatetime（0）NULL DEFAULT NULL，PRIMARY KEY（id）使用BTREE）ENGINE = InnoDB AUTO_INCREMENT = 1 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_general_ci ROW_FORMAT = Dynamic;

4，创建springboot工程，添加maven依赖com.codingapi.txlcn txlcn-tm 5.0.2.RELEASE

5，然后在应用中加入注解@EnableTransactionManagerServer

在TM中的属性配置中根据自己的需求配置我的配置如下/ ** spring.application.name = tx-manager server.port = 7970

spring.datasource.driver-class-name = com.mysql.cj.jdbc.Driver spring.datasource.url = jdbc：mysql：// localhost：3306 / tx-manager？characterEncoding = UTF-8 spring.datasource.username = root spring.datasource.password = root＃指定注册中心地址eureka.client.serviceUrl.defaultZone = http：// localhost：8761 / eureka / eureka.instance.instance-id = $ {spring.cloud.client.ip-address }：$ {server.port} eureka.instance.prefer-ip-address = true

mybatis.configuration.map-underscore-to-camel-case = true mybatis.configuration.use-generated-keys = true

TxManager主机IP
tx-lcn.manager.host = 127.0.0.1

TxClient连接请求端口
TX-lcn.manager.port = 8070

心跳检测时间（ms）的
TX-lcn.manager.heart时间= 15000

分布式事务执行总时间
tx-lcn.manager.dtx-time = 30000＃参数延迟删除时间单位ms tx-lcn.message.netty.attr-delay-time = 10000 tx-lcn.manager.concurrent-level = 128

开启日志
tx-lcn.logger.enabled = true logging.level.com.codingapi = debug #redis主机spring.redis.host = 127.0.0.1 #redis端口spring.redis.port = 6379 #redis密码spring.redis.password = shen123 ** / 7，添加完以上配置后启动，浏览器访问TxManager管理后台http：// localhost：7970，默认密码为：codingapi。二，创建bank-a服务和bank-b服务所有TC，需要事务处理的都要加入如果maven依赖：com.codingapi.txlcn txlcn-tc 5.0.2.RELEASE

com.codingapi.txlcn txlcn-txmsg-netty 5.0.2.RELEASE然后在启动类加上注解@EnableDistributedTransaction
三，

默认之配置为TM的本机默认端口如果是默认可以不用写，demo中使用的默认地址，所有配置文件中没有
TX-lcn.client.manager地址127.0.0.1 =：8070

四，此demospringboot版本是springboot2.1.0，注册中心用的eureka，然后还用了feign
