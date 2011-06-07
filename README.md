Lift on Jetty 6 with H2 configured via JNDI
===========================================

Basic setup for JNDI and SBT
----------------------------

* src/main/scala/bootstrap/liftweb/Boot.scala: DefaultConnectionIdentifier.jndiName = "jdbc/lift_proto"
* src/main/webapp/WEB-INF/web.xml: resource-ref
* src/main/jetty/jetty-env.xml: org.mortbay.jetty.plus.naming.Resource configuration
* project/build/LiftProject.scala: jettyEnvXml and jetty-plus, jetty-naming

Note that org.h2.jdbcx.JdbcDataSource requires username/password, and that it uses URL and not Url.

This project should now work using sbt and then *jetty-run*.

Deploying to Jetty on Ubuntu
----------------------------

For deploying on Jetty 6 on Ubuntu 11.04, I've used something like the following with MySQL:

/etc/jetty/contexts/lift-h2-jndi.xml:

    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE Configure PUBLIC "-//Mort Bay Consulting//DTD Configure//EN"
     "http://jetty.mortbay.org/configure.dtd">
    <Configure id="magentoprocessor" class="org.mortbay.jetty.webapp.WebAppContext">
      <Array id="plusConfig" type="java.lang.String">
        <Item>org.mortbay.jetty.webapp.WebInfConfiguration</Item>
        <Item>org.mortbay.jetty.plus.webapp.EnvConfiguration</Item>
        <Item>org.mortbay.jetty.plus.webapp.Configuration</Item>
        <Item>org.mortbay.jetty.webapp.JettyWebXmlConfiguration</Item>
        <Item>org.mortbay.jetty.webapp.TagLibConfiguration</Item>
      </Array>
    <Set name="configurationClasses"><Ref id="plusConfig"/></Set>
    <Set name="contextPath">/mycontext</Set>
    <Set name="war">/opt/.../....war</Set>
    <Set name="extractWAR">false</Set>
    <Set name="copyWebDir">false</Set>
    <Call class="java.lang.System" name="setProperty">
      <Arg>run.mode</Arg>
      <Arg>test</Arg>
    </Call>
    <New id="magento" class="org.mortbay.jetty.plus.naming.Resource">
      <Arg></Arg>
      <Arg>jdbc/lift_proto</Arg>
      <Arg>
        <New class="com.mysql.jdbc.jdbc2.optional.MysqlConnectionPoolDataSource">
          <Set name="Url">jdbc:mysql://localhost:3306/lift_proto</Set>
          <Set name="User">sa</Set>
          <Set name="Password">sa</Set>
        </New>
      </Arg>
    </New>
    </Configure>

and check contextPath and war parameters.
