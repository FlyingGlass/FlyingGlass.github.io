---
title: jenkins部署springboot应用
date: 2018-04-08 16:24:50
categories: 
- [Devops]
---

*最近在项目中使用持续交付(CI)工具jenkins部署spring-boot应用，碰到不少问题，特以此文记录*

#### jenkins配置

- 插件配置
- `java`,`maven`,`git`配置

#### 部署脚本

```bash
#!/usr/bin/env bash

BUILD_ID=dontKillMe

PROJ_NAME="backend-web-service"
LOG_PATH="/mnt/data01/logs"
MODULE_JAR_PATH="${WORKSPACE}/backend-web-service/target/${PROJ_NAME}.war"

###### Stop service
pid=`ps -ef | grep $PROJ_NAME | grep -v grep | awk '{print $2}'`
if [ -n "$pid" ]
then
    sudo kill -9 $pid
fi
    
###### Maven build package
cd ${WORKSPACE} && mvn clean package -Dmaven.test.skip=true

###### Start service
# Check Log Path
if [ ! -d ${LOG_PATH} ]; then
    sudo mkdir -p ${LOG_PATH}
fi

sudo nohup java -jar ${MODULE_JAR_PATH} --spring.profiles.active=dev --logback.logdir=${LOG_PATH} 1>/dev/null 2>/dev/null &

echo "Log Path : ${LOG_PATH}"
```

#### 碰到的问题

- 由于`jenkins`构建完毕后会杀掉所有启动的进程，可以通过改变`BUILD_ID`的值来防止后台进程被杀死
- 如果采用`maven`工程进行构建，修改`BUILD_ID`无效，后台进程随着`jenkins`构建完成后仍被杀死