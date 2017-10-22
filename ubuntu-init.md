# Init
## [wine](https://wiki.winehq.org/Ubuntu)
1. sudo dpkg --add-architecture i386
2. sudo add-apt-repository ppa:wine/wine-builds
3. sudo apt-get update
4. sudo apt-get install --install-recommends winehq-devel

## [sougou](http://pinyin.sogou.com/linux/)
1. sudo add-apt-repository ppa:fcitx-team/nightly
2. install fcitx from software center
3. install [sougou input method](http://pinyin.sogou.com/linux/download.php?f=linux&bit=64)
4. setting - language support, change ibus to fcitx(if necessary)
5. run fcitx and add sougou input method
6. log off and login

## java and maven
1. download jdk from oracle(tar.gz format)
2. tar -vxf jdk.tar.gz
3. mv jdk /usr/jvm/java
4. download maven from apache
5. tar -vxf maven.tar.gz
6. mv maven /usr/maven
7. gedit /etc/profile and apply environment setting for java and maven
<br> export JAVA_HOME=/usr/jvm/jdk1.8.0_91
<br> export M2_HOME=/usr/maven
<br> export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib
<br> export PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$M2_HOME/bin

## google and lantern
1. download [google chrome](https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb)
2. go to [lantern github](https://github.com/getlantern/lantern/releases) and check the new version to download

## ssh to other linuxes without password
1. ssh-keygen -t rsa
2. scp ~/.ssh/id_rsa.pub root@remote:/root/.ssh/authorized_keys
3. 修改/etc/ssh/sshd_config文件，将ClientAliveInterval 0和ClientAliveCountMax 3的注释符号去掉,将ClientAliveInterval对应的0改成60,ClientAliveInterval指定了服务器端向客户端请求消息的时间间隔, 默认是0, 不发送.而ClientAliveInterval 60表示每分钟发送一次, 然后客户端响应, 这样就保持长连接了.ClientAliveCountMax, 使用默认值3即可.ClientAliveCountMax表示服务器发出请求后客户端没有响应的次数达到一定值, 就自动断开. 正常情况下, 客户端不会不响应.

## gedit with markdown plugin
1. change permission to install plugin: sudo chmod -R 777 ~/.config/gedit
2. install as superuser: sudo ./gedit-markdown.sh install
3. for more visit [reference](http://www.cnblogs.com/mo-wang/p/5118144.html) for help
4. more official plugins: sudo apt-get install gedit-plugins

## [rabbitcvs](http://wiki.rabbitvcs.org/wiki/install/ubuntu)
1. sudo add-apt-repository ppa:rabbitvcs/ppa
2. sudo apt-get update
3. install dependency: sudo apt-get install python-nautilus python-configobj python-gtk2 python-glade2 python-svn python-dbus python-dulwich subversion meld
4. install rabbitcvs: sudo apt-get install rabbitvcs-cli rabbitvcs-core rabbitvcs-gedit rabbitvcs-nautilus3
5. for more information visit [reference](http://www.linuxidc.com/Linux/2013-08/89117.htm)

## [docker](https://www.docker.com/)
### Installation
1. sudo apt-get install apt-transport-https
2. sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
3. sudo bash -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
4. sudo apt-get update
5. sudo apt-get install lxc-docker
### Common commands and usage [Reference](http://blog.csdn.net/xundh/article/details/46441403)
1. docker images：列出所有镜像(images) 
2. docker ps：列出正在运行的(容器)containers 
3. docker pull ubuntu：下载镜像 
4. docker run -i -t ubuntu /bin/bash：运行ubuntu镜像 
5. docker commit 3a09b2588478 ubuntu:mynewimage：提交你的变更，并且把容器保存成Tag为mynewimage的新的ubuntu镜像.(注意，这里提交只是提交到本地仓库，类似git)
6. Reference document [镜像不能下载问题](https://groups.google.com/forum/#!topic/dockercn/Jmi1jGzC8IY)
7. Reference document [Docker体验 Ubuntu下安装](http://blog.csdn.net/xundh/article/details/46441403)
### Daily tips
1. create a private registry but cannot pull from it on other machine
> on other machine, change /etc/default/docker, add: DOCKER_OPTS="--insecure-registry registryIPGoes:5000"
### Containerized apps
1. mysql
> docker run -d --name mysql -e MYSQL_ROOT_PASSWORD=mypass -p 3306:3306 -v /root/yimym/mysql/conf:/etc/mysql/conf.d -v /root/yimym/mysql/data:/var/lib/mysql mysql
2. mongo
> docker run -d --name mongo -p 27017:27017 -v /my-data-dir:/data/db mongo --auth
3. redis
> docker run -d --name redis -p 6379:6379 -v /my-data-dir:/data redis redis-server --appendonly yes
4. jenkins(deprydicated)
> docker run -d --name jenkins -p 81:8080 -p 50000:50000 -u root -v $JENKINS_HOME:/var/jenkins_home jenkins
5. rancher
> docker run -d --name rancher --restart=unless-stopped -p 8080:8080 rancher/server

### run a new container as a server
1. docker run -it --name remote.test -p 5000:4000 -p 3307:3306 -p 27018:27017 -p 6380:6379 -v /root/remote.test:/root/data -v /root/docker:/root/docker -u root localhost:5000/yimym/strawberry
2. apt-get update
3. apt-get install apt-transport-https ca-certificates
4. apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
5. touch /etc/apt/sources.list.d/docker.list
6. add in docker.list file: deb https://apt.dockerproject.org/repo ubuntu-trusty main
7. apt-get update
8. apt-get install docker-engine

## install [fail2ban](https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-centos-7) to protect ssh
1. fail2ban is not listed in offical CentOS packages, but in epel, so first install epel repos:
```sh
$ wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
$ rpm -ivh epel-release-latest-7.noarch.rpm
```
or more easier(not tested)
```sh
$ sudo yum install epel-release
```

2. install fail2ban and config
```sh
$ yum install fail2ban -y
$ systemctl enable fail2ban.service
```
configurations:
```sh
$ cat /etc/fail2ban/jail.local
[DEFAULT]
# Ban hosts for one hour:
bantime = 3600

# Override /etc/fail2ban/jail.d/00-firewalld.conf:
banaction = iptables-multiport

[sshd]
enabled = true
```

# Tips
## vim
### copy to system clipboard
1. check if current vim has this feature by following command returns none 0
```sh
:echo has('clipboard') 
```

2. if feature not available, try to install vim-gtk by
```sh
sudo apt-get install vim-gtk
```

3. copy to system clipboard by
```sh
gg"+yG #copy entire buffer
"+nyy #copy n lines
```

4. reference to [Accessing system clipboard](http://vim.wikia.com/wiki/Accessing_the_system_clipboard) and [How to copy to system clipboard](http://stackoverflow.com/questions/11489428/how-to-make-vim-paste-from-and-copy-to-systems-clipboard)

5. register help by
```sh
:help registers
```

### toggle line number
1. :set nu #show line number
2. :set nonu #hide line number

## docker
### inspect
1. docker inspect -f "{{.Mounts}}" $container # show mounted volumns
2. docker inspect -f "{{json .Mounts}}" $container # show mounted volumns in JSON pattern
3. docker inspect -f "{{json .Mounts}}" $container | python -mjson.tool #show in pretty JSON view with python tool
