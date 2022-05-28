# harbor_note

## 链接

git : [https://github.com/goharbor/harbor.git](https://github.com/goharbor/harbor.git)

文档 : [https://goharbor.io/docs/2.5.0/](https://goharbor.io/docs/2.5.0/)

## 安装

### 前提条件

#### docker环境

安装

可参考 [https://www.cnblogs.com/smallbo/p/12378727.html](https://www.cnblogs.com/smallbo/p/12378727.html)

#### docker-compose

安装

下载地址
[https://github.com/docker/compose/releases](https://github.com/docker/compose/releases)

下载

```shell
wget https://github.com/docker/compose/releases/download/v2.5.1/docker-compose-linux-x86_64
```

加入到环境变量

```shell
mv docker-compose-linux-x86_64 /usr/bin/docker-compose

chmod +x /usr/bin/docker-compose
```

### 在线安装

下载在线安装包

```shell
wget https://github.com/goharbor/harbor/releases/download/v2.5.0/harbor-online-installer-v2.5.0.tgz
```

解压

```shell
tar -zvxf harbor-online-installer-v2.5.0.tgz 
```

进入项目

```shell
cd harbor
```

修改配置

```shell
mv harbor.yml.tmpl harbor.yml

vim harbor.yml
```

设置hostname 为外网ip，或者为域名

```shell
hostname: [:hostname]
```

如果使用http则注释掉https部分

```shell
#https:
  # https port for harbor, default is 443
  #  port: 443
  # The path of cert and key files for nginx
  #certificate: /your/certificate/path
  #private_key: /your/private/key/path
```

设置整个harbor项目数据的映射目录

```shell
data_volume: /data
```

> 其余数据库的账户密码等可自行设置

运行

```shell
./install.sh 
```

### 推送本地镜像到harbor&拉取harbor镜像

> 假设当前harbor地址为 115.159.195.1:8080

#### 设置信任的私有库

**linux**

```shell
sudo vi /etc/docker/daemon.json
```

新增

```shell
{
"insecure-registries":["115.159.195.1:8080"]
}
```

重启 docker

```shell
systemctl daemon-reload //重载配置文件
systemctl restart  docker //重启docker
```

**mac**

docker=> Preferences => Deamon => insecure registries => 新增 115.159.195.1:8080 => Apply&Restart

#### 登录私有仓库

```shell
docker login -u [:账户] -p [:密码] 115.159.195.1:8080
```

#### 标记镜像打tag

```shell
docker tag SOURCE_IMAGE[:TAG] 115.159.195.1:8080/[:harborproject]/REPOSITORY[:TAG]
```

例：本地有一个portainer/portainer的镜像，harbor中设置了一个test的项目

```shell
docker tag portainer/portainer:latest 115.159.195.1:8080/test/portainer/portainer:latest
```

#### 推送镜像

```shell
docker push 115.159.195.1:8080/test/portainer/portainer:latest
```

#### 拉取镜像

```shell
docker pull 115.159.195.1:8080/test/portainer/portainer:latest
```