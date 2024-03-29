# Jenkins

The leading open source automation server, Jenkins provides hundreds of plugins to support building, deploying and automating any project. 集成GitLab, Jenkins, Docker and Slack

* 对项目进行进程守护：私自覆盖文件会进还原

## install

war包自带Jetty服务器 `java -jar jenkins.war`访问<http://localhost:8080>，

将jenkins.war放到tomcat/webapps目录下，重启tomcat服务，<http://localhost:8080/jenkins>

### 安装

```sh
# ubuntu
wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins

systemctl status|start jenkins

ufw status
ufw allow 8080

sudo ufw allow OpenSSH
sudo ufw enable

# centos
yum install -y java
# 安装jenkins
# 添加Jenkins库到yum库，Jenkins将从这里下载安装。
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
yum install -y jenkins
# 如果不能安装就到官网下载jenkis的rmp包，官网地址（http://pkg.jenkins-ci.org/redhat-stable/）
wget http://pkg.jenkins-ci.org/redhat-stable/jenkins-2.7.3-1.1.noarch.rpm
wget https://mirrors.tuna.tsinghua.edu.cn/jenkins/redhat/jenkins-2.173-1.1.noarch.rpm
rpm -ivh jenkins-2.7.3-1.1.noarch.rpm

# 配置jenkis的端口
vi /etc/sysconfig/jenkins
JENKINS_PORT="8080"

service jenkins start/stop/restart

# 访问ip：8080，进行安装以及配置用户
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

安装包自动完成的工作包含：

* 设置Jenkins开机自启动，设置内容见/etc/init.d/Jenkins 文件
* 创建了jenkins用户用来运行服务
* 输出安装日志文件到  /var/log/jenkins/jenkins.log，检查日志文件可定位安装过程遇到的问题
* 输出配置文件到/etc/default/Jenkins可修改相关配置
* 设置Jenkins监听的端口为8080，通过浏览器可用该端口进行访问

### docker安装

```
docker pull jenkins/Jenkins
docker run -d -p 49001:8080 -v$PWD/jenkins:/var/jenkins_home -t jenkins/jenkins
```

## usage

* <https://www.centos.bz/2017/07/jenkins-ansible-docker-swarm/>
- https://www.digitalocean.com/community/tutorials/how-to-set-up-continuous-integration-pipelines-in-jenkins-on-ubuntu-16-04：构建出错，docker权限不足

## 参考

- [官网](https://jenkins.io/) 中文网 <http://www.jenkins.org.cn>
- [插件](https://plugins.jenkins.io/)
- [Jenkins文档](https://jenkins.io/doc/book/pipeline/)
