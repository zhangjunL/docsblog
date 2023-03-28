# nginx 简介
nginx是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。nginx是由俄罗斯的程序员Igor Sysoev为俄国访问量第二的Rambler.ru站点（俄文：Рамблер）开发的，第一个公开版本0.1.0发布于2004年10月4日。nginx是一款轻量级的Web服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，其特点是占有内存少，并发能力强，事实上nginx的并发能力在同类型的网页服务器中表现较好，中国大陆使用nginx网站用户超过50%。
## nginx安装
### 1.下载
```bash
 apt-get install nginx
```
### 2.查看nginx版本
```bash
nginx -v
```
### 3.启动nginx
```bash
service nginx start
```
### 4.查看nginx运行状态
```bash
service nginx status
```
### 5.停止nginx
```bash
service nginx stop
```
### 6.重启nginx
```bash
service nginx restart
```
## nginx配置
### 1.配置文件
nginx的配置文件位于/etc/nginx/nginx.conf

### 2.加载配置文件
```bash
service nginx reload
```