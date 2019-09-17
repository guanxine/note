##
```
docker build -t helloworld .
```

docker image ls

## docker run 启动
```
docker run -p 4000:80 helloworld
```

## 修改
可以对正在运行的镜像直接提交为一个对象
```
shaonian:note gx$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                  NAMES
c5245c94035c        helloworld          "python app.py"     3 minutes ago       Up 3 minutes        0.0.0.0:4000->80/tcp   practical_franklin

shaonian:note gx$ docker commit c52 gxshaonian/helloworld:v1
sha256:6d1961fbf9cd4ce33d5be9f69ceaaaf5bc3f473dbde8e44b277d74d5dc813156
```
## volume
允许将宿主机上指定的目录或者文件，挂载到容器里进行读取和修改操作

1. docker run -v /test ...
2. docker run -v /home:/test ...

1. 默认在宿主句创建一个临时目录 /var/lib/docker/volumes/[volume_id]/_data, 挂在到容器的 /test 目录
2. 把宿主机 /home 挂载到容器 /test 目录