# tomcat-docker
The tomcat docker that built with JDK 8

## 1. Build eclipse-temurin

Run command to build image

#### For Ubuntu Focal
```
cd ./eclipse-temurin/focal
docker build -t tatdat171/eclipse-temurin:8-jdk-focal
```

#### For Ubuntu Jammy

```
cd ./eclipse-temurin/focal
docker build -t tatdat171/eclipse-temurin:8-jdk-jammp
```


## 2. Build Tomcat image from eclipse-temurin

Run command to build image

#### For Ubuntu Focal
```
cd ./tomcat/9/focal
docker build -t tatdat171/tomcat:9.0.73-jdk8-temurin-focal
```

#### For Ubuntu Jammy

```
cd ./tomcat/9/jammy
docker build -t tatdat171/tomcat:9.0.73-jdk8-temurin-jammy
```


