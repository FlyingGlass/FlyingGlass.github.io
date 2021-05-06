---
title: nexus的maven配置
date: 2018-04-26 14:44:44
categories: 
- [Web开发]
---

*nexus是可以搭建maven，npm，docker，pypi私有仓库的工具，本文记录maven的安装配置*

#### 配置nexus的maven

- maven-aliyun-proxy

  ```
  Remote storage:http://maven.aliyun.com/nexus/content/groups/public
  Blob store:maven
  ```

- maven-cloudera-proxy

  ```
  Remote storage:https://repository.cloudera.com/artifactory/cloudera-repos/
  Blob store:maven
  ```

- maven-hosted-snapshots

  ```
  Version policy: Snapshot
  Blob store:maven
  Deployment policy: Allow redeploy
  ```

- maven-hosted-releases

  ```
  Version policy: Release
  Blob store:maven
  Deployment policy: Allow redeploy
  ```

- maven-times-group(注意Members顺序)

  ```
  Blob store:maven
  Members:
  - maven-aliyun-proxy
  - maven-cloudera-proxy
  - maven-hosted-releases
  - maven-hosted-snapshots
  - maven-central
  - maven-releases
  - maven-snapshots
  ```

#### 配置本地maven

- 全局配置

  - mac下brew的配置：`/usr/local/Cellar/maven/3.5.2/libexec/conf/settings.xml`
  - linux下的配置：`$MAVEN_HOME/conf/settings.xml`

- 用户级别配置

  - `~/.m2/settings.xml(若没有需要新建)`

- settings.xml配置如下：

  ```xml
  <settings>
    <mirrors>
      <mirror>
        <!--This sends everything else to /public -->
        <id>nexus</id>
        <mirrorOf>*</mirrorOf>
        <url>http://ip:port/nexus/repository/maven-times-group/</url>
      </mirror>
    </mirrors>
    <profiles>
      <profile>
        <id>nexus</id>
        <!--Enable snapshots for the built in central repo to direct -->
        <!--all requests to nexus via the mirror -->
        <repositories>
          <repository>
            <id>central</id>
            <url>http://central</url>
            <releases><enabled>true</enabled></releases>
            <snapshots><enabled>true</enabled></snapshots>
          </repository>
        </repositories>
       <pluginRepositories>
          <pluginRepository>
            <id>central</id>
            <url>http://central</url>
            <releases><enabled>true</enabled></releases>
            <snapshots><enabled>true</enabled></snapshots>
          </pluginRepository>
        </pluginRepositories>
      </profile>
    </profiles>
    <activeProfiles>
      <!--make the profile active all the time -->
      <activeProfile>nexus</activeProfile>
    </activeProfiles>

    <servers>
      <!--server username and password -->
      <server>
        <id>nexus-releases</id>
        <username>admin</username>
        <password>admin123</password>
      </server>
      <server>
        <id>nexus-snapshot</id>
        <username>admin</username>
        <password>admin123</password>
      </server>
    </servers>

  </settings>
  ```

- 发布项目到nexus仓库配置

  在项目的`pom.xml`如下配置：

  ```xml
  	<!--nexus 设置-->
  	<distributionManagement>
          <repository>
  			<id>nexus-releases</id>
  			<name>Nexus Release Repository</name>
  			<url>http://ip:port/nexus/repository/maven-hosted-releases/</url>
  		</repository>
  		<snapshotRepository>
  			<id>nexus-snapshot</id>
  			<name>Nexus Snapshot Repository</name>
  			<url>http://ip:port/nexus/repository/maven-hosted-snapshots/</url>
  		</snapshotRepository>
  	</distributionManagement>
  ```