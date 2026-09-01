## 常见命令
```
# 查看docker状态
docker ps
# 拉取镜像
docker pull mysql:8.0
# 查看镜像
docker images
# 运行并挂载目录
docker run -d -p 80:80 -v /f/software/docker/nginx/html:/usr/share/nginx/html nginx
```