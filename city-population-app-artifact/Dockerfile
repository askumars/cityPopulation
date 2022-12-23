FROM  nimmis/java-centos:latest
WORKDIR /
ADD city-population-app-0.0.1-SNAPSHOT.jar city-population-app-0.0.1-SNAPSHOT.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/city-population-app-0.0.1-SNAPSHOT.jar", "--server.servlet.context-path=/city"]
