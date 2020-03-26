# docker 操作

## 基本操作
- 拉取最新版本的Ｕbuntu<br>
`docker pull <image_name:tag>`

- 查看本地镜像<br>
`docker images`

- 运行容器<br>
`docker run -itd --name <container_name> <image_name>`
<br>

- 进入容器
`docker exec -it <container_name> bash`<br>