# Jenkins配置说明
## 后端程序
### 代码托管
后端程序托管在SVN上，服务器地址http://192.168.0.20/svn/
### 触发机制
每次提交SVN后触发进行打包编译，生成新的程序镜像并发布。
### shell
```shell
#!/bin/bash
set -e

echo
echo "start to build with $@"
echo

APPNAME="$1"
APPPORT="$2"

### check input
if [ -z $APPNAME ] || [ -z $APPPORT ]; then
  echo "Both appname and appport is necessary!"
  echo "Usage: deploy APPNAME APPPORT"
  exit 0
fi

echo "App $APPNAME use port $APPPORT"

### maven compile
/opt/maven/bin/mvn -DskipTests clean compile

### stop and remove old containers
docker ps -a |grep "$APPNAME" |awk '{print $1}' |while read CID
do
  echo 'stop and remove old container by id=' $CID
  docker stop $CID
  docker rm -f $CID
done

### stop and remove old image
docker images |grep "$APPNAME" |awk '{print $3}' |while read OLDIMGID
do
  echo 'remove old images by id=' $OLDIMGID
  docker rmi -f $OLDIMGID
done

### maven package and build new image
/opt/maven/bin/mvn -DskipTests package docker:build

### get new image ID if exists
IMGID=$(docker images |grep "$APPNAME" |awk '{print $3}')
echo '>>> image id is ' $IMGID
if [ -z $IMGID ]; then
  echo "App $APPNAME does not exist!"
  exit 1
fi

### get vol if necessary
VOL=""
if [ "$APPNAME" == "s-file" ]; then
  VOL="-v /root/yimym:/yimym"
fi
if [ "$APPNAME" == "gateway" ]; then
  VOL="-v /root/yimym/public:/public"
fi
if [ "$APPNAME" == "auth" ]; then
  VOL="-v /root/yimym/pfile:/yimym"
fi
if [ "$APPNAME" == "config" ]; then
  VOL="-v /root/yimym/config:/config"
fi
### build new container
docker run $VOL -p $APPPORT:$APPPORT -d --name $APPNAME --env-file /root/yimym/dev.env --add-host CONFIG_SERVER:192.168.0.11 --add-host REGISTRY-SERVER:192.168.0.11 --add-host S_MYSQL_SERVER:192.168.0.11 --add-host S_MONGODB_SERVER:192.168.0.11 --add-host P_REDIS_SERVER:192.168.0.11 --add-host P_MONGODB_SERVER:192.168.0.11 --add-host GATEWAY_SERVER:192.168.0.11 --add-host PLATFORM_SERVER:192.168.0.11 $IMGID
```

## 前端程序
前端程序托管在GIT，服务器地址http://192.168.0.102
### 触发机制
合并test分支后，代码打包并推送到测试服务器
### shell
```shell
#!/bin/bash
set -e

V_PREFIX="V2.1."
V_POSTFIX="i" # i means internaltest while e means externaltest

if [ -z $1 ]; then
  echo "ERROR: environment need to be specified, such as development-n:"
  echo "Usage: $0 environment"
  exit 1
fi

DIGITEXP="^[0-9]{1,}$"
version=`cat /root/sh/v.cnt`
if [[ ! "$version" =~ $DIGITEXP ]]; then 
  echo "ERROR: $version is not a number, please check v.cnt file"
  exit 2
fi
version=`expr $version + 1`
if [ $version -lt 100 ]; then
  version="${V_PREFIX}0${version}${V_POSTFIX}"
else
  version="${V_PREFIX}${version}${V_POSTFIX}"
fi
echo "===== New version is $version ====="

export NOMES_VERNAME=$1
export NOMES_VERNO=$version
export NODE_HOME=/usr/node
export PATH=$PATH:$NODE_HOME/bin
webpack
```
