#### 启动docker

```xml
systemctl start docker
```

#### 重启docker

```shell
systemctl restart  docker
```

#### 关闭docker

```shell
systemctl stop docker
```

#### 查看所有容器

```shell
docker ps -a
```

#### 运行容器

```xml
docker start 容器id
```

#### 移除容器

```shell
docker rm 容器id
```

#### 停止运行的容器

```shell
docker stop containerId
```

#### 启动容器

```shell
docker run ...
```

- -itd: -i 让容器的标准输入保持打开状态, -t 为容器分配一个伪终端, -d: 在后台运行容器
- --name: 为容器指定名称
- -p: 端口映射, 格式: 宿主机端口:容器端口
- -v: 数据卷挂载: 宿主机目录:容器目录

#### 查看容器是否运行

```shell
docker ps | grep name
```

