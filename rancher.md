# Rancher Installation
1. create mysql table: CREATE DATABASE IF NOT EXISTS cattle COLLATE = 'utf8_general_ci' CHARACTER SET = 'utf8';
2. run rancher: docker run -d --restart=always -p 88:8080 -e CATTLE_DB_CATTLE_MYSQL_HOST=120.76.115.230 -e CATTLE_DB_CATTLE_MYSQL_PORT=3307 -e CATTLE_DB_CATTLE_MYSQL_NAME=cattle -e CATTLE_DB_CATTLE_USERNAME=root -e CATTLE_DB_CATTLE_PASSWORD=root rancher/server

# Rancher API
## 两种API，一种是环境API，一种是账户API，
1. 环境API可以在当前登陆环境UI的API菜单中获取。
2. 账户API可以在UI中的API页面，在advanced options中获取。

# [Rancher Compose CLI](http://docs.rancher.com/rancher/v1.1/zh/cattle/rancher-compose/)
1. 从右下角CLI中可以直接下载对应操作系统版本的CLI
2. rancher-compose --url http://server_ip:8080 --access-key <username_of_environment_api_key> --secret-key <password_of_environment_api_key> up

## [Rancher Compose](https://docs.rancher.com/rancher/v1.1/en/cattle/rancher-compose/commands/)

### [Upgrade specified service](https://docs.rancher.com/rancher/v1.1/en/cattle/rancher-compose/upgrading/)
1. update a service in stack
``` shell
rancher-compose --url $RANCHER_URL --access-key $RANCHER_ACCESS_KEY --secret-key $RANCHER_SECRET_KEY --project-name stackname --file docker-compose.yml --rancher-file rancher-compose.yml --project-name stackname --file docker-compose.yml up --upgrade --pull -d servicename
```
2. finish upgrade(--confirm-upgrade) or rollback(--roleback)
```shell
rancher-compose --url $RANCHER_URL --access-key $RANCHER_ACCESS_KEY --secret-key $RANCHER_SECRET_KEY --project-name stackname --file docker-compose.yml --rancher-file rancher-compose.yml --project-name stackname --file docker-compose.yml up --upgrade --rollback -d
rancher-compose --url $RANCHER_URL --access-key $RANCHER_ACCESS_KEY --secret-key $RANCHER_SECRET_KEY --project-name stackname --file docker-compose.yml --rancher-file rancher-compose.yml --project-name stackname --file docker-compose.yml up --upgrade --confirm-upgrade -d
```
2. compose rely on yaml file, check [here](https://docs.docker.com/compose/compose-file/) for yml details specification.

# FAQ
1. can NOT be added again after rancher agent is deleted, remove folder
```shell
rm -rf /var/lib/rancher/state
```
