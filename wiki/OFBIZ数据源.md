# OFBIZ数据源
* 要从多个数据库中获取数据，那么必然要连接多个数据库，所以必须配置对应的多个datasource。
* 配置连接数据库在%OFBIZ_HOME%\framework\entity\config\entityengine.xml文件中定义
* OFBIZ自带的数据库是Apache Derby，是测试系统的数据库，不适合开发用
* 如何切换到mysql？
* 把%OFBIZ_HOME%\framework\entity\config\entityengine.xml中所有 delagator的mysql的注释去掉，然后把相应的derby的设置注释掉
```xml
    <delegator name="default" entity-model-reader="main" entity-group-reader="main" entity-eca-reader="main" distributed-cache-clear-enabled="false">
        <group-map group-name="org.ofbiz" datasource-name="localmysql"/>
        <group-map group-name="org.ofbiz.olap" datasource-name="localmysqlolap"/>
        <group-map group-name="org.ofbiz.tenant" datasource-name="localmysqltenant"/>
<!--        <group-map group-name="org.apache.ofbiz" datasource-name="localderby"/>-->
<!--        <group-map group-name="org.apache.ofbiz.olap" datasource-name="localderbyolap"/>-->
<!--        <group-map group-name="org.apache.ofbiz.tenant" datasource-name="localderbytenant"/>-->
    </delegator>
    <!-- May be used when you create a service that manages many data for massive imports, this for performance reason or to escape functional cases --> 
    <delegator name="default-no-eca" entity-model-reader="main" entity-group-reader="main" entity-eca-reader="main" entity-eca-enabled="false" distributed-cache-clear-enabled="false">
        <group-map group-name="org.ofbiz" datasource-name="localmysql"/>
        <group-map group-name="org.ofbiz.olap" datasource-name="localmysqlolap"/>
        <group-map group-name="org.ofbiz.tenant" datasource-name="localmysqltenant"/>
        <!--        <group-map group-name="org.apache.ofbiz" datasource-name="localderby"/>-->
<!--        <group-map group-name="org.apache.ofbiz.olap" datasource-name="localderbyolap"/>-->
<!--        <group-map group-name="org.apache.ofbiz.tenant" datasource-name="localderbytenant"/>-->
    </delegator>

    <!-- Be sure that your default delegator (or the one you use) uses the same datasource for test. You must run "gradlew loadAll" before running "gradlew testIntegration" -->
    <delegator name="test" entity-model-reader="main" entity-group-reader="main" entity-eca-reader="main">
        <group-map group-name="org.ofbiz" datasource-name="localmysql"/>
        <group-map group-name="org.ofbiz.olap" datasource-name="localmysqlolap"/>
        <group-map group-name="org.ofbiz.tenant" datasource-name="localmysqltenant"/>
<!--        <group-map group-name="org.apache.ofbiz" datasource-name="localderby"/>-->
<!--        <group-map group-name="org.apache.ofbiz.olap" datasource-name="localderbyolap"/>-->
<!--        <group-map group-name="org.apache.ofbiz.tenant" datasource-name="localderbytenant"/>-->
    </delegator>
```

Mysql Version: > 5.6.4 (supports datetime milliseconds)
```xml
    <datasource name="localmysql"
            helper-class="org.apache.ofbiz.entity.datasource.GenericHelperDAO"
            field-type-name="mysql"
            check-on-start="true"
            add-missing-on-start="true"
            check-pks-on-start="false"
            use-foreign-keys="true"
            join-style="ansi-no-parenthesis"
            alias-view-columns="false"
            drop-fk-use-foreign-key-keyword="true"
            table-type="InnoDB"
            character-set="utf8"
            collate="utf8_general_ci"
            offset-style="limit">
        <read-data reader-name="tenant"/>
        <read-data reader-name="seed"/>
        <read-data reader-name="seed-initial"/>
        <read-data reader-name="demo"/>
        <read-data reader-name="ext"/>
        <read-data reader-name="ext-test"/>
        <read-data reader-name="ext-demo"/>
        <inline-jdbc
                jdbc-driver="com.mysql.jdbc.Driver"
                jdbc-uri="jdbc:mysql://127.0.0.1/ofbiz?autoReconnect=true&amp;characterEncoding=UTF-8"
                jdbc-username="ofbiz"
                jdbc-password="ofbiz"
                isolation-level="ReadCommitted"
                pool-minsize="2"
                pool-maxsize="250"
                time-between-eviction-runs-millis="600000"/><!-- Please note that at least one person has experienced a problem with this value with MySQL
                and had to set it to -1 in order to avoid this issue.
                For more look at http://markmail.org/thread/5sivpykv7xkl66px and http://commons.apache.org/dbcp/configuration.html-->
        <!-- <jndi-jdbc jndi-server-name="localjndi" jndi-name="java:/MySqlDataSource" isolation-level="Serializable"/> -->
    </datasource>
    <datasource name="localmysqlolap"
            helper-class="org.apache.ofbiz.entity.datasource.GenericHelperDAO"
            field-type-name="mysql"
            check-on-start="true"
            add-missing-on-start="true"
            check-pks-on-start="false"
            use-foreign-keys="true"
            join-style="ansi-no-parenthesis"
            alias-view-columns="false"
            drop-fk-use-foreign-key-keyword="true"
            table-type="InnoDB"
            character-set="utf8"
            collate="utf8_general_ci"
            offset-style="limit">
        <read-data reader-name="tenant"/>
        <read-data reader-name="seed"/>
        <read-data reader-name="seed-initial"/>
        <read-data reader-name="demo"/>
        <read-data reader-name="ext"/>
        <read-data reader-name="ext-test"/>
        <read-data reader-name="ext-demo"/>
        <inline-jdbc
                jdbc-driver="com.mysql.jdbc.Driver"
                jdbc-uri="jdbc:mysql://127.0.0.1/ofbizolap?autoReconnect=true&amp;characterEncoding=UTF-8"
                jdbc-username="ofbiz"
                jdbc-password="ofbiz"
                isolation-level="ReadCommitted"
                pool-minsize="2"
                pool-maxsize="250"
                time-between-eviction-runs-millis="600000"/><!-- Please note that at least one person has experienced a problem with this value with MySQL
                and had to set it to -1 in order to avoid this issue.
                For more look at http://markmail.org/thread/5sivpykv7xkl66px and http://commons.apache.org/dbcp/configuration.html-->
        <!-- <jndi-jdbc jndi-server-name="localjndi" jndi-name="java:/MySqlDataSource" isolation-level="Serializable"/> -->
    </datasource>
    <datasource name="localmysqltenant"
            helper-class="org.apache.ofbiz.entity.datasource.GenericHelperDAO"
            field-type-name="mysql"
            check-on-start="true"
            add-missing-on-start="true"
            check-pks-on-start="false"
            use-foreign-keys="true"
            join-style="ansi-no-parenthesis"
            alias-view-columns="false"
            drop-fk-use-foreign-key-keyword="true"
            table-type="InnoDB"
            character-set="utf8"
            collate="utf8_general_ci"
            offset-style="limit">
        <read-data reader-name="tenant"/>
        <read-data reader-name="seed"/>
        <read-data reader-name="seed-initial"/>
        <read-data reader-name="demo"/>
        <read-data reader-name="ext"/>
        <read-data reader-name="ext-test"/>
        <read-data reader-name="ext-demo"/>
        <inline-jdbc
                jdbc-driver="com.mysql.jdbc.Driver"
                jdbc-uri="jdbc:mysql://127.0.0.1/ofbiztenant?autoReconnect=true&amp;characterEncoding=UTF-8"
                jdbc-username="ofbiz"
                jdbc-password="ofbiz"
                isolation-level="ReadCommitted"
                pool-minsize="2"
                pool-maxsize="250"
                time-between-eviction-runs-millis="600000"/><!-- Please note that at least one person has experienced a problem with this value with MySQL
                and had to set it to -1 in order to avoid this issue.
                For more look at http://markmail.org/thread/5sivpykv7xkl66px and http://commons.apache.org/dbcp/configuration.html-->
        <!-- <jndi-jdbc jndi-server-name="localjndi" jndi-name="java:/MySqlDataSource" isolation-level="Serializable"/> -->
    </datasource>
    <datasource name="odbcmysql"
            helper-class="org.apache.ofbiz.entity.datasource.GenericHelperDAO"
            field-type-name="mysql"
            check-on-start="true"
            add-missing-on-start="true"
            check-pks-on-start="false"
            use-foreign-keys="true"
            join-style="ansi-no-parenthesis"
            alias-view-columns="false"
            drop-fk-use-foreign-key-keyword="true"
            table-type="InnoDB"
            character-set="utf8"
            collate="utf8_general_ci"
            offset-style="limit">
        <read-data reader-name="tenant"/>
        <read-data reader-name="seed"/>
        <inline-jdbc
                jdbc-driver="com.mysql.jdbc.Driver"
                jdbc-uri="jdbc:mysql://127.0.0.1/ofbiz_odbc?autoReconnect=true&amp;characterEncoding=UTF-8"
                jdbc-username="ofbiz"
                jdbc-password="ofbiz"
                isolation-level="ReadCommitted"
                pool-minsize="2"
                pool-maxsize="250"
                time-between-eviction-runs-millis="600000"/>
        <!-- <jndi-jdbc jndi-server-name="localjndi" jndi-name="java:/MySqlDataSource" isolation-level="Serializable"/> -->
    </datasource>
```

