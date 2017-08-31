# Spring PetClinic Sample Application

This repo is a fork of the [spring-petclinic/spring-framework-petclinic](https://github.com/spring-petclinic/spring-framework-petclinic).
The intent of the repository is to demonstrate, how to use JRebel to hot reload Java classes in a web application that is started in a Docker container.

The extra artifacts that are added compared to the original repositories:
* jrebel/ -- JRebel standalone distribution
* docker-compose.yml -- Docker Compose configuration that describes all the dependencies and configuration to start the application in the Docker container
* src/main/resources/rebel-remote.xml -- A configuration file for JRebel Remoting to recognize the module as "reloadable"

## docker-compose.yml

The configuration file describes the environment and dependencies that are required for this example.

```
petclinic:
  image: tomcat:8.0
  ports:
    - "8080:8080"
  volumes:
    - "./jrebel/jrebel.jar:/jrebel.jar"
    - "./jrebel/lib/libjrebel64.so:/libjrebel64.so"
    - "./target/petclinic.war:/usr/local/tomcat/webapps/petclinic.war"
    - "~/.jrebel:/.jrebel"
 environment:
    - "JAVA_OPTS=-agentpath:/libjrebel64.so -Drebel.remoting_plugin=true"
 ```
The configuration uses [Tomcat Docker image](https://hub.docker.com/_/tomcat/), hence the web archive of the application should be mapped to */usr/local/tomcat/webapps/* folder. The binaries from JRebel distribution -- jrebel.jar and libjrebel64.so -- are required for the agent to work. The local *.jrebel* is optional, but suggested to persist JRebel caches in order to make the application startup faster in case of restarts.

For more imformation about JRebel Remoting see the official documentation: [https://manuals.zeroturnaround.com/jrebel/remoteserver/index.html](https://manuals.zeroturnaround.com/jrebel/remoteserver/index.html)

## Running the application

!!!Before you start - make sure that Docker is installed on your machine!!!

```
      git clone https://github.com/antonarhipov/petclinic-docker-jrebel.git
      cd petclinic-docker-jrebel
      ./mvnw -DskipTests clean package
      docker-compose up
```
To start updating the application:
* Import the Maven project into your IDE of choice
* Generate rebel.xml configuration file
* Configure the remote server URL in JRebel settings. In this case, the URL is http://localhost:8080. (The IDE plugin will show a warning -- safely ignore)
* Make changes to the source code and synchronize the project via JRebel IDE plugin









