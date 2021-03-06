####server.xml配置文件解说
```
配置文件结构：
<Server>
    <Service>
	<Engine>
	    <Host>
	    </Host>
	</Engine>
    </Service>
</Server>
```
#####配置文件样例
```
规划： 
    网站网页目录：/web/www      域名：www.test.com 
    论坛网页目录：/web/bbs     URL：www.test.com/bbs 
    网站管理程序：$CATALINA_HOME/wabapps   URL：manager.test.com/manager    允许访问地址：172.23.136.* 
```     
```
配置文件内容和说明
<Server port="8005" shutdown="SHUTDOWN"> 
   <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" /> 
   <Listener className="org.apache.catalina.core.JasperListener" /> 
   <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" /> 
   <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" /> 
   <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" /> 
      <GlobalNamingResources> 
#全局命名资源，来定义一些外部访问资源，其作用是为所有引擎应用程序所引用的外部资源的定义 

        <Resource name="UserDatabase" auth="Container" 
              type="org.apache.catalina.UserDatabase" 
              description="User database that can be updated and saved" 
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory" 
              pathname="conf/tomcat-users.xml" /> 
      </GlobalNamingResources> 
#定义的一个名叫“UserDatabase”的认证资源，将conf/tomcat-users.xml加载至内存中，在需要认证的时候到内存中进行认证  

      <Service name="Catalina"> 
# 定义Service组件，同来关联Connector和Engine，一个Engine可以对应多个Connector，每个Service中只能一个Engine 

        <Connector port="80" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" /> 
# 修改HTTP/1.1的Connector监听端口为80.客户端通过浏览器访问的请求，只能通过HTTP传递给tomcat。 

        <Connector port="8009" protocol="AJP/1.3" redirectPort="8443" /> 
        <Engine name="Catalina" defaultHost="test.com"> 
# 修改当前Engine，默认主机是，www.test.com  --> 

        <Realm className="org.apache.catalina.realm.LockOutRealm"> 
            <Realm className="org.apache.catalina.realm.UserDatabaseRealm" 
                   resourceName="UserDatabase"/> 
        </Realm> 
# Realm组件，定义对当前容器内的应用程序访问的认证，通过外部资源UserDatabase进行认证 

          <Host name="test.com"  appBase="/web" unpackWARs="true" autoDeploy="true"> 
# 定义一个主机，域名为：test.com，应用程序的目录是/web，设置自动部署，自动解压   

            <Alias>www.test.com</Alias> 
#  定义一个别名www.test.com，类似apache的ServerAlias --> 

            <Context path="" docBase="www/" reloadable="true" /> 
# 定义该应用程序，访问路径""，即访问www.test.com即可访问，网页目录为：相对于appBase下的www/，即/web/www，
# 并且当该应用程序下web.xml或者类等有相关变化时，自动重载当前配置，即不用重启tomcat使部署的新应用程序生效 

            <Context path="/bbs" docBase="/web/bbs" reloadable="true" /> 
# 定义另外一个独立的应用程序，访问路径为：www.test.com/bbs，该应用程序网页目录为/web/bbs  

            <Valve className="org.apache.catalina.valves.AccessLogValve" directory="/web/www/logs" 
                   prefix="www_access." suffix=".log" 
                   pattern="%h %l %u %t &quot;%r&quot; %s %b" /> 
#  定义一个Valve组件，用来记录tomcat的访问日志，日志存放目录为：/web/www/logs如果定义为相对路径则是相当于$CATALINA_HOME，
#  并非相对于appBase，这个要注意。定义日志文件前缀为www_access.并以.log结尾，pattern定义日志内容格式，
#  具体字段表示可以查看tomcat官方文档 

          </Host> 
          <Host name="manager.test.com" appBase="webapps" unpackWARs="true" autoDeploy="true"> 
#  定义一个主机名为manager.test.com，应用程序目录是$CATALINA_HOME/webapps,自动解压，自动部署  

            <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="172.23.136.*" /> 
#  定义远程地址访问策略，仅允许172.23.136.*网段访问该主机，其他的将被拒绝访问 

            <Valve className="org.apache.catalina.valves.AccessLogValve" directory="/web/bbs/logs" 
                   prefix="bbs_access." suffix=".log" 
                   pattern="%h %l %u %t &quot;%r&quot; %s %b" /> 
#  定义该主机的访问日志     

          </Host> 
        </Engine> 
      </Service> 
    </Server> 
```
