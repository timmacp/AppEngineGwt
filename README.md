# AppEngineGwt
Modular gwt with embedded Jetty

This derives from:
### artifact 'modular-webapp'
https://github.com/tbroyer/gwt-maven-archetypes

### App Engine samples:
https://github.com/GoogleCloudPlatform/java-docs-samples/tree/main/appengine-java11/appengine-simple-jetty-main

https://github.com/GoogleCloudPlatform/java-docs-samples/tree/master/appengine-java11/helloworld-servlet

## USAGE

### development mode

mvn gwt:codeserver -pl *-client -am  
  
in another terminal window:  
mvn jetty:run -pl *-server -am -Denv=dev  
  
http://localhost:8080/

### Embedded Jetty local or App Engine standard

mvn package -P WAR  
mvn exec:java -pl *-jettyMain -P call-main  
mvn appengine:deploy -pl *-server -P gae-deploy

### Jetty Maven plugin without codeserver
mvn package -pl *-client -am  
mvn jetty:run -pl *-server -am  

### Eclipse debugging
mvn gwt:codeserver -pl *-client -am 

In Eclipse launch:
1) mvn jetty:run -pl *-server -am -Denv=dev
  
with this in the VM arguments box under the JRE tab:  -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:8000 

2) a remote java application at project *-server for localhost port 8000.

###  Jetty hot restart
For hot restart in Eclipse select 'build automatically'.  
Hot restart does not require a browser refresh.

## cleaning
### all
mvn clean
### all except jettyMain
mvn clean -pl *-server -am         
### server only
mvn clean -pl *-server   

## NOTES


### development mode
In profile 'env-dev' there's no extraClasspath for sample-shared under jetty-maven-plugin 
as it causes multiple scanning warnings.

### 'WAR' profile
1) This is to remove for jetty:run profiles: sample-client  & sample-jettyMain dependencies.  
The latter requires 'WAR' profile to be not active by default.  

2) The gwt BOM artifactId in the root of artifact 'modular-webapp' is omitted, as it draws in unwanted dependencies in the WAR.
It could be included in -client.

### Jetty Maven plugin without codeserver:  
1) mvn package -pl *-client -am  is required to to create resourceBase sample-client-1.0-SNAPSHOT  
The alternative is a reactor build with sample-client as a dependency in -Denv=prod.
Problem is then Jetty to expect a jetty_overlays directory,
related to maven-war-plugin expecting to overlay gwt-app's content in the resulting WAR because -client is type war.

2) mvn jetty:run -pl *-server -am :  
If on 1st run: \sample-server\target\tmp directory fails to build, the 2nd attempt is ok. 
  
### sample-jettyMain
The appengine-simple-jetty-main sample has unused declared embedded Jetty dependencies in sample-jettyMain:  
jetty-util & jetty-annotations, they're removed here.

### App Engine
For the App Engine runtime to resolve com.example.Main: classpath is unspecified:  
app.yaml:  entrypoint: 'java -cp "*"  

### Reactor builds
-pl,--projects <arg>  
lists reactor projects to build instead  of all projects. 
 
-am,--also-make       
to also build projects required by -pl projects  
alternatively in an Eclipse launch select  'resolve workspace artifacts'.

### mvn install
No artifacts need to be installed but this might be advisable as it involves additional chaecks.

