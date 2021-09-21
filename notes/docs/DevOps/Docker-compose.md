# Docker-Compose

有些任务需要多个容器一起提供服务，这时候一个个管理就比较麻烦了，可以用Docker Compose统一管理

在PC和Mac下的Docker Desktop默认会带Docker-Compose，无需额外安装

Linux下需要单独安装，访问：<https://github.com/docker/compose/releases>，找到对应linux系统的安装包右键复制链接

```bash
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-Linux-x86_64" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
docker-compose --version
```
