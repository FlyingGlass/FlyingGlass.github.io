---
title: aws配置eb-cli
date: 2018-06-02 12:25:02
categories: 
- [AWS]
---

*EB CLI 是 Elastic Beanstalk 的命令行界面，它提供了可简化从本地存储库创建、更新和监控环境的交互式命令。将 EB CLI 用作每日开发和测试周期的一部分，取代 AWS 管理控制台。*

#### Prerequisites

- `Larger than Python 2.7 or Python3.4` 

#### 安装EB CLI

```bash
# 可选参数 --upgrade更新依赖组件 --user安装到用户子目录
pip install awsebcli 
# 设置Env, eg: LOCAL_PATH=~/.local/bin
export PATH=LOCAL_PATH:$PATH
source ~/.bashrc
# Check eb version
eb --version
```

#### 配置 EB CLI

```bash
# Maven package 激活pro的profile打入ebextensions的nginx配置
cd my-app && mvnw clean package -Dmaven.test.skip=true
# EB init
eb init
# EB create env profile
eb create
# EB deploy
eb deploy
# EB logs
eb logs
# 登录 EB Instance 检查详细日志
# eb ssh 
# tailf /var/log/nginx/access.log
```

#### 配置示例(`cat .elasticbeanstalk/config.yml`)

```yaml
branch-defaults:
  master:
    environment: my-app-dev
    group_suffix: null
  release:
    environment: my-app-dev
    group_suffix: null
deploy:
  # 需要上传java包的路径
  artifact: target/my-app.war
global:
  application_name: my-app
  branch: null
  default_ec2_keyname: mypem
  default_platform: Java 8
  default_region: us-east-1
  include_git_submodules: true
  instance_profile: null
  platform_name: null
  platform_version: null
  profile: eb-cli
  repository: null
  sc: git
  workspace_type: Application
```

#### 反向代理配置

```markdown
#### Nginx配置如何生效
- 目录结构如下：
	my-app.war
	|-- .ebextensions
	|   `-- nginx
	|       `nginx.conf
	|       `agent_deny
	`-- org
	`--META-INF
	`--WEB-INF
```

#### Pom.xml

```xml
<!--maven package springboot war-->
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <finalName>${project.artifactId}</finalName>
            </configuration>
        </plugin>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <configuration>
                <failOnMissingWebXml>false</failOnMissingWebXml>
                <webResources>
                    <resource>
                        <directory>src/main/ebextensions</directory>
                        <targetPath>.ebextensions</targetPath>
                        <filtering>true</filtering>
                    </resource>
                </webResources>
            </configuration>
        </plugin>
    </plugins>
</build>
```

#### 参考链接

- [安装 Elastic Beanstalk 命令行界面 (EB CLI)](https://docs.aws.amazon.com/zh_cn/elasticbeanstalk/latest/dg/eb-cli3-install.html)
- [配置 EB CLI](https://docs.aws.amazon.com/zh_cn/elasticbeanstalk/latest/dg/eb-cli3-configuration.html)
- [配置反向代理](https://docs.aws.amazon.com/zh_cn/elasticbeanstalk/latest/dg/java-se-nginx.html)