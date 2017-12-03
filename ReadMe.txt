sRestcommConnect Dao Service Project
======================


To build this project use

    mvn install

To run this project use the following Maven goal

    mvn camel:run


Build and Deploy Container manage connections “DataSource” by using Pax-JDBC features
-------------------------
 
this components will register all data sources using configuration files and same DS can be used in persistence.xml

1- install PAX-JDBC
Pax Jdbc module creating a features xml file to deploy Pax Jdbc components as features in Apache Karaf. 

To use this feature enter the following in your Karaf shell -

* feature:repo-add mvn:org.ops4j.pax.jdbc/pax-jdbc-features/1.1.0/xml/features
* feature:install transaction jndi pax-jdbc-oracle pax-jdbc-pool-dbcp2 pax-jdbc-config -- For Orale
* feature:install transaction jndi pax-jdbc-mysql pax-jdbc-pool-dbcp2 pax-jdbc-config  -- For MySql

2- Create configuration file with name restrictions “org.ops4j.datasource-*.cfg” under /etc

* touch org.ops4j.datasource-MySqlDS.cfg
	
	osgi.jdbc.driver.class=com.mysql.jdbc.Driver
	osgi.jdbc.driver.name=mysql
	osgi.jndi.service.name=java:/MySqlDS
	dataSourceName=MySqlDS
 
	url=jdbc:mysql://localhost:3306/restcomm

	# Connection URL for load balancing:
	#jdbc:mysql:loadbalance://localhost:3306,localhost:3310/restcomm

	# Connection URL for server replication:
	#jdbc:mysql:replication://master,slave1,slave2,slave3/restcomm
	user=userA
	password=passA
 
3- Check data source connection, locate bundle id of “OPS4J Pax JDBC Config”

JBossFuse:karaf@root> ls 359

OPS4J Pax JDBC Config (359) provides:
-------------------------------------
objectClass = [org.osgi.service.cm.ManagedServiceFactory]
service.id = 662
service.pid = org.ops4j.datasource
----
dataSourceName = MySqlDS
felix.fileinstall.filename = file:/Users/imran/jboss-fuse-6.3.0.redhat-187/etc/org.ops4j.datasource-MySqlDS.cfg
objectClass = [javax.sql.DataSource]
osgi.jdbc.driver.class = com.mysql.jdbc.Driver
osgi.jdbc.driver.name = mysql
osgi.jndi.service.name = java:/MySqlDS
password = *****
service.factoryPid = org.ops4j.datasource
service.id = 663
service.pid = org.ops4j.datasource.cac7dc48-9450-4866-9a83-67ca9b508cbd
url = jdbc:mysql://localhost:3306/restcomm
user = root

5- Configure application to use OSGI data source
    <bean id="mySqlDataSource"
          class="org.springframework.jndi.JndiObjectFactoryBean">
        <property name="jndiName">
            <value>osgi:service/javax.sql.DataSource/(osgi.jndi.service.name=jdbc/MySqlDS)</value>
        </property>
    </bean>

