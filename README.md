#快速搭建springboot web

```
1.先执行mvn install

2.再引入下面maven依赖包
 <dependency>
    <groupId>cn.bucheng</groupId>
    <artifactId>spring-boot-quick-starter-web</artifactId>
    <version>1.0-SNAPSHOT</version>
 </dependency>

3.添加下面配置
spring.mvc.throw-exception-if-no-handler-found=true
spring.resources.add-mappings=false
#配置http输出让浏览器采用utf-8
spring.http.encoding.charset=utf-8
spring.http.encoding.force=true
spring.http.encoding.enabled=true



4.创建log4j2.xml文件
<?xml version="1.0" encoding="UTF-8"?>
<configuration status="WARN" monitorInterval="600">

    <Properties>
        <!--自定义日志文件名前缀，建议使用模块/项目名称，具体的日志级别在Appenders里面配置-->
        <Property name="LOG_NAME_PREFIX">rockmq-boot-</Property>
        <!--自定义日志输出格式%-5p|%-d{yyyy-MM-dd HH:mm:ss.SSS}|%C.%M.%3L|%X{intellif_log_id}%m%n  -->
        <Property name="patternStyle">%p|%-d{yyyy-MM-dd HH:mm:ss.SSS}|[%t]|%C{1.}.%M.%4L|%X{intellif_log_id}%m%n
        </Property>
        <!--自定义日志存放路径，这为项目根路径-->
        <Property name="LOG_HOME">./logs</Property>
        <!--自定义日志文件生成策略,每月/每天/每小时生成新的日志文件-->
        <Property name="LOG_CREATE_INTERVAL">yyyy-MM-dd</Property>
        <!--自定义日志文件切分策略,大小超过阈值后创建一个新的日志文件MB KB-->
        <Property name="LOG_FILE_SIZE">100MB</Property>
        <!--自定义日志备份文件个数，如每天生成日志文件，按照大小切割后只保留最新的几个-->
        <Property name="LOG_BACK_NUM">10</Property>
        <!--自定义日志文件保存时间,超过该时间之前的清除，m代表分钟，d代表天-->
        <Property name="LOG_SAVE_TIME">7d</Property>
    </Properties>


    <Appenders>
        <!--输出控制台的配置-->
        <console name="Console" target="SYSTEM_OUT">
            <!--只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
            <!--输出日志的格式-->
            <PatternLayout pattern="${patternStyle}"/>
        </console>

        <!--配置DEBUG日志-->
        <RollingFile name="RollingFileDebug" fileName="${LOG_HOME}/${LOG_NAME_PREFIX}debug.log"
                     filePattern="${LOG_HOME}/$${date:${LOG_CREATE_INTERVAL}}/${LOG_NAME_PREFIX}debug.%i.log.gz">

            <!--只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <Filters>
                <ThresholdFilter level="info" onMatch="DENY" onMismatch="NEUTRAL"/>
                <ThresholdFilter level="debug" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <!--输出日志的格式-->
            <PatternLayout pattern="${patternStyle}"/>

            <!-- max配置每个策略下最多保存文件的数目，比如每天最多保存max个 -->
            <DefaultRolloverStrategy max="${LOG_BACK_NUM}">
                <Delete basePath="${LOG_HOME}" maxDepth="2">
                    <IfFileName glob="*/*.log*"/>
                    <IfLastModified age="${LOG_SAVE_TIME}"/>
                </Delete>
            </DefaultRolloverStrategy>

            <Policies>
                <!-- 每到size大小时生成一个日志文件 -->
                <SizeBasedTriggeringPolicy size="${LOG_FILE_SIZE}"/>
            </Policies>
        </RollingFile>



        <!-- 打印debug及以上级别的日志，fileName配置文件路径，filePattern配置文件备份策略，按照日期命名文件夹饼自动归档-->
        <RollingFile name="RollingFileInfo" fileName="${LOG_HOME}/${LOG_NAME_PREFIX}info.log"
                     filePattern="${LOG_HOME}/$${date:${LOG_CREATE_INTERVAL}}/${LOG_NAME_PREFIX}info.%i.log.gz">

            <!--只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <Filters>
                <ThresholdFilter level="warn" onMatch="DENY" onMismatch="NEUTRAL"/>
                <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
            </Filters>
            <!--输出日志的格式-->
            <PatternLayout pattern="${patternStyle}"/>

            <!-- max配置每个策略下最多保存文件的数目，比如每天最多保存max个 -->
            <DefaultRolloverStrategy max="${LOG_BACK_NUM}">
                <!--这里配置的是删除旧日志文件的时候的目录递归深度，-->
                <Delete basePath="${LOG_HOME}" maxDepth="2">
                    <!-- 删除旧日志文件时的文件名称匹配规则-->
                    <IfFileName glob="*/*.log*"/>
                    <!-- 删除旧日志文件时，判断文件的保存时间,只保留最近的LOG_SAVE_TIME的日志，旧的会删除-->
                    <IfLastModified age="${LOG_SAVE_TIME}"/>
                </Delete>
            </DefaultRolloverStrategy>

            <Policies>
                <!-- 每到size大小时生成一个新的日志文件 -->
                <SizeBasedTriggeringPolicy size="${LOG_FILE_SIZE}"/>
            </Policies>
        </RollingFile>


        <!--配置WARN，ERROR日志,考虑到WARN和ERROR日志相对少很多，打在一个文件里面-->
        <RollingFile name="RollingFileWarnAndError" fileName="${LOG_HOME}/${LOG_NAME_PREFIX}warn.log"
                     filePattern="${LOG_HOME}/$${date:${LOG_CREATE_INTERVAL}}/${LOG_NAME_PREFIX}warn.%i.log.gz">

            <!--只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
            <ThresholdFilter level="warn" onMatch="ACCEPT" onMismatch="DENY"/>
            <!--输出日志的格式-->
            <PatternLayout pattern="${patternStyle}"/>

            <!-- max配置每个策略下最多保存文件的数目，比如每天最多保存max个 -->
            <DefaultRolloverStrategy max="${LOG_BACK_NUM}">
                <Delete basePath="${LOG_HOME}" maxDepth="2">
                    <IfFileName glob="*/*.log*"/>
                    <IfLastModified age="${LOG_SAVE_TIME}"/>
                </Delete>
            </DefaultRolloverStrategy>

            <Policies>
                <!-- 每到size大小时生成一个日志文件 -->
                <SizeBasedTriggeringPolicy size="${LOG_FILE_SIZE}"/>
            </Policies>
        </RollingFile>

    </Appenders>


    <!-- 定义logger，logger引入的appender才会生效-->
    <loggers>
        <!--过滤掉spring的一些无用的DEBUG信息,自行配置-->
        <Logger name="org.hibernate.validator.internal.util.Version"
                level="warn"/>
        <Logger name="org.apache.coyote.http11.Http11NioProtocol"
                level="warn"/>
        <Logger name="org.apache.tomcat.util.net.NioSelectorPool"
                level="warn"/>
        <Logger name="org.apache.catalina.startup.DigesterFactory"
                level="error"/>
        <Logger name="com.mangofactory.swagger"
                level="error"/>


        <!--这里需要把上面Appenders里面配置的的RollingFile的名字添加进来，没有添加进来的话不会生效-->
        <root level="info">
            <appender-ref ref="Console"/>
            <appender-ref ref="RollingFileDebug"/>
            <appender-ref ref="RollingFileInfo"/>
            <appender-ref ref="RollingFileWarnAndError"/>
        </root>
    </loggers>

</configuration>

5.在springboot中排除原始的日志依赖
   <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <exclusions>
                <exclusion>
                    <artifactId>spring-boot-starter-logging</artifactId>
                    <groupId>org.springframework.boot</groupId>
                </exclusion>
            </exclusions>
    </dependency>

6.配置导入的log4j2.xml文件路径
#log4j2配置
logging.config= classpath:log4j2.xml

7.使用抛出业务异常请使用抛出BusinessException异常

8.controller层访问如果是想返回最原始的数据格式请在方法或者类上面填的IgnoreAdvice注解

9.业务返回格式如下
{
    "code": 0,
    "data": "hi",
    "message": "operation ok"
}
其中code 
0表示操作成功     返回数据会在data中
-1表示系统出现异常  message是基本错误信息，详情在data中
其他表示业务错误    提示在message中 data为null

```