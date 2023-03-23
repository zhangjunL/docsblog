
## 手动部署 
 前端：
1.本地构建  npm run build

1.复制docker 文件夹到 dist发布的文件夹（包含nginx配置文件，如需修改，应在生成镜像前进行修改）

2.复制 Dockerfile 文件到 发布文件夹根目录（该文件中可以指定nginx镜像版本）

3.将发布文件夹上传到服务器（可以使用xftp工具或者scp命令）

4.通过ssh 连接到linux服务器

5.进入发布文件夹中

6.运行 docker build -t name:tag .   (.不能省略) 生成镜像

7.docker run -restart=away -d -p 宿主机端口:docker端口 --name 容器名称  镜像名称：tag(tag应递增)

后端:
1.在visual studio 2022及以上版本中发布，发布在文件夹中。

2.将发布好的文件上传到服务器

3.通过ssh连接到linux服务器

4.进入发布文件夹中

5.运行 docker build -t name:tag .   (.不能省略) 生成镜像

6.docker run -restart=away -d -p 宿主机端口:docker端口 --name 容器名称  镜像名称：tag(tag应递增)