##
### master
1. kube-apiserver
2. kube-controller-manager
3. kube-scheduler.

### node 
1. kubelet: <-> master：当　Scheduler 确定在某个 Node 上运行 Pod 后，　会将Pod的具体信息（image, volume）发送给该节点的kubectl，　kubectl根据这些信息创建和运行容器，并向Master报告运行状态
2. kube-proxy： service 在逻辑上代表了后端的多个Pod，外界通过service访问pod,　kube-proxy将访问service的tcp/upd数据流转发到后端容器．如果有多个副本，kube-proxy会实现负载均衡
3. A container runtime(like Docker, rkt)

a virtual or a physical machine
* Master-----node: one to many
* Node --- pod: one to many
* pod --- container: one to many


## Controller
1. Deployment
2. ReplicaSet
3. 

## Object
1. Pod: 最小工作单元，包含一个或多个容器，作为一个整体被 Master 调度到一个 Node 上运行
* 运行多个容器，必须容器联系非常紧密，需要直接共享资源(直接共享存储放到一个pod中), tomcat 和 mysql 并不是直接共享存储，而且没必要一起启动停止，放到各自的pod中合适
* 使用同一个网络namespace,相同的ip和port
* pod 会被频繁的销毁和重启，他们的ip会发生变化。
2. Service
定义了外界访问一组特定pod的方式，service 有自己的ip和端口，service 为pod提供了负载均衡
运行pod(controller)
访问pod(service)
3. Volume
4. Namespace
可以将一个物理的cluster划分多个虚拟cluster.（多个cluster）
不同的namespace资源是完全隔离的。


## 命令
1. 部署应用: kubectl run 
2. 访问应用: kubectl expose
3. Scale 应用:

apt-get update && apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl


## 安装

###
```
apt-get update && apt-get install docker.io
```
### 所有节点安装
```
https://kubernetes.io/docs/setup/independent/install-kubeadm/

apt-get update && apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl


apt-get update && apt-get install -y apt-transport-https curl
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubectl


k8s.gcr.io/kube-apiserver:v1.14.1
k8s.gcr.io/kube-controller-manager:v1.14.1
k8s.gcr.io/kube-scheduler:v1.14.1
k8s.gcr.io/kube-proxy:v1.14.1
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd:3.3.10
k8s.gcr.io/coredns:1.3.1

# pull.sh
for i in `kubeadm config images list`; do
	imageName=${i#k8s.gcr.io/}
        docker pull registry.aliyuncs.com/google_containers/$imageName
        docker tag registry.aliyuncs.com/google_containers/$imageName k8s.gcr.io/$imageName
	docker rmi registry.aliyuncs.com/google_containers/$imageName
done;




root@gx1:/home/guanxine# kubeadm init --apiserver-advertise-address 192.168.38.11 --pod-network-cidr=10.244.0.0/16
I0509 03:31:50.447661    7155 version.go:96] could not fetch a Kubernetes version from the internet: unable to get URL "https://dl.k8s.io/release/stable-1.txt": Get https://dl.k8s.io/release/stable-1.txt: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
I0509 03:31:50.447753    7155 version.go:97] falling back to the local client version: v1.14.1
[init] Using Kubernetes version: v1.14.1
[preflight] Running pre-flight checks
	[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Activating the kubelet service
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [gx1 kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.38.11]
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [gx1 localhost] and IPs [192.168.38.11 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [gx1 localhost] and IPs [192.168.38.11 127.0.0.1 ::1]
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 17.502879 seconds
[upload-config] storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.14" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --experimental-upload-certs
[mark-control-plane] Marking the node gx1 as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node gx1 as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: 1wmht0.5lcjfyts9gzdnu7h
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.38.11:6443 --token 1wmht0.5lcjfyts9gzdnu7h \
    --discovery-token-ca-cert-hash sha256:7af30e970d0b6614e68cec8de3322776d27fc294d95026d6fd2b425976c17793 




```

安装网络
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

kubeadm token list

 sudo swapoff -a

 kubeadm join --token 1wmht0.5lcjfyts9gzdnu7h 192.168.38.11:6443 --discovery-token-unsafe-skip-ca-verification
 ```
 [preflight] Running pre-flight checks
	[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[kubelet-start] Downloading configuration for the kubelet from the "kubelet-config-1.14" ConfigMap in the kube-system namespace
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Activating the kubelet service
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.
```

https://blog.csdn.net/lfm940624/article/details/84706118

```
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
export KUBECONFIG=/etc/kubernetes/admin.conf
```

提示：echo "source <(kubectl completion bash)" >> ~/.bashrc
查看详情
sudo kubectl describe pod kube-apiserver-gx1 --namespace=kube-system

sudo kubectl get deployment

guanxine@gx1:~$ sudo kubectl get pod -o wide
NAME                         READY   STATUS    RESTARTS   AGE   IP       NODE     NOMINATED NODE   READINESS GATES
httpd-app-6df58645c6-7xfcv   0/1     Pending   0          79s   <none>   <none>   <none>           <none>
httpd-app-6df58645c6-wr562   0/1     Pending   0          79s   <none>   <none>   <none>           <none>



kubectl apply -f nginx.yml


##
Service cluster ip 是一个虚拟IP. 由　Kubenetes节点上的iptables规则管理

Namespace：创建了容器
Cgroups: 为进程设置资源：cpu,内存，磁盘，网络
容器本质是一种特殊的进程


常用命令

http://192.168.131.1/auth-service/v1/users;jsessionid=67A3B51C925E8C866352DDE4508ECA39
```
  980  cd /home/k2data/zbh/nginx-ingress
  981  ls
  982  vim values.yaml 
  983  cd /root/yjw/
  984  ls
  985  ls ingress/
  986  cat ingress/test.ingress.yaml 
  987  kubectl get pods,svc | grep auth
  988  kubectl get pods -o wide | grep ingress
  989  kubectl get ingress
  990  kubectl get pods | grep mysql
  991  kubectl exec -it mysql-64bb655d7b-bmvll bash
  992  kubectl get pods | grep cas
  993  cat auth-api/auth-api-svc.yaml 
  994  kubectl exec -it cas-server-1-7c468f4c79-6pmbg bash
  995  cat auth-api/auth-api.yaml 

```


尽快熟悉：
首先，在本地通过 Docker 测试代码，制作镜像； 
然后，选择合适的 Kubernetes API 对象，编写对应 YAML 文件（比如，Pod，Deployment)
最后，在 Kubernetes 上部署这个 YAML 文件

## pod 容器设计模式
namespace 隔离，cgroup 限制，rootfs 文件系统
容器本质是进程，单进程模型，并不是说只能运行一个进程，而是值容器没有管理多个进程的能力，k8s 操作系统
只是一个逻辑概念，是一组共享了某些资源的容器，共享的是同一个 network namespace, 并且可以声明共享同一个 volume. 之间靠（Infra（k8s.gcr.io/pause） 容器关联，它最先启动 ）

同一个pod的容器
1. 可以通过 localhost 进行通信
2. 看到的网络设备和 Infra 容器一样
3. 一个 pod 只有一个 ip 地址，也就是这个 pod 的 network namespace 对应的 ip 地址

进出流量都是通过 Infra 容器完成的。

pod 而不是容器才是 k8s 的最小编排单位。
容器（Container）就成了 pod 属性里的一个普通字段。
pod 当作是传统环境中的“机器”，把容器看作是运行在这个“机器”里的“用户程序”
调度（运行在哪个服务器），网络（网卡），存储（磁盘），安全（防火墙）相关的属性，基本都是 pod 级别

凡是跟 linux namespace 相关的属性，一定是 pod 级别的。
pod 重要字段
1. NodeSelector: pod 和 node 绑定
2. NodeName
3. HostAliases: 定义了 pod 的 host 文件(/etc/hosts)，设置 host 文件里的内容一定要通过这样的方式，否则在 pod 被删除重建之后，kubelet 会自动覆盖被修改的内容
4. Container
  + Image : 镜像
  + Command: 命令
  + workingDir: 容器的工作目录
  + Ports: 端口
  + volumeMounts: 挂在的 volume 
  + ImagePullPolicy: 镜像拉去策略，默认是 Always, 每次创建 pod 都重新拉去一次镜像，Never 不主动拉去镜像，IfNotPresent 在主机上不存在时这个镜像时才拉去。
  + Lifecycle: 
    - postStart: Docker 容器 ENTRYPOINT 执行之后开始执行，是异步的。postStart 开始执行可能 ENTRYPOINT 还没有结束
    - preStop: 同步的，优雅退出。先执行 preStop 后 kill 容器

pod 生命周期主要体现在 pod api 对象的 Status 部分， 是除了 Metadata 和 Spec 之外第三个重要字段
pod.status.phase 为当前状态
1. pending ：  pod 里有些容器因为某种原因不能被顺利创建，比如调度不成功
2. Running: 调度成功
3. Succeeded: 所有容器都正常运行完毕，并且已经退出。运行一次任务常见
4. Failed: 至少有一个容器不正常退出
5. Unknown: 异常状态，pod 的状态不能持续的被 kubelet 汇报给 kube-apiserver. 可能主从节点（Master 和 kubelet）间通信出现了问题。

Project Volume
有几个特殊的 Volume 不是为了存容器里的数据，也不是用来进行容器和宿主机之间的数据交换。是为容器预先定义好数据
1. Secret: 把 pod 想要访问的加密数据存放到 Etcd 中。然后便可以通过pod的容器里挂在 Volume 的方式，访问到这些 Secret 里保存的信息了，场景：存放数据库密码
2. ConfigMap
3. Downward API
4. ServiceAccountToken


控制器模式
1. Deployment


pod 对容器的进一步抽象和封装。k8s 操作 pod 的逻辑，都是由 Controller 完成 -> Deployment
Deployment
1. 控制器定义
2. 被控制对象 -> template 叫做 PodTemplate

可以水平扩展/收缩， 由 ReplicaSet(对象) 实现。 Deployment -> ReplicaSet -> 多个副本 pod 

StatefulSet
多个实例之间有依赖关系，主从，主备关系 以及 实例对外部数据有依赖关系的应用，被成为 “有状态应用” Stateful Application
1. 拓扑状态， 多个实例关系不对等。
2. 存储状态，多个实例分别绑定了不同的存储数据

StatefulSet 的核心功能，就是通过某种方式记录这些状态，然后 pod 被重新创建时，能够为新 pod 恢复这些状态。

Headless Service
用户只要访问 Service , 就能访问到某个 Pod, Service 如何被访问
1. 以 Service 的 VIP 方式（Virtual IP, 即：虚拟IP）。 比如访问 10.0.23.1 这个 Service 的 IP 地址时，10.0.23.1 其实就是一个 VIP。它会把请求转发到该
Service 所代理的某个 pod 上。
2. 以 Service 的 DNS 方式。
  + Normal Service: 解析出来是 VIP
  + Headless Service: 解析出来是代理的某一个 pod 的IP， 不需要 VIP，直接以 DNS 记录的方式解析出被代理的 pod 的ip地址： clusterIP: None， 会以 DNS 记录的方式暴露出它所代理的 pod. 所有 pod 的 IP 会绑定一个这样的DNS 记录 ```<pod-name>.<svc-name>.<namespace>.svc.cluster.local``` 知道了Service名字和pod名字就可以访问到 pod 的 ip.
  

  1. StatefulSet 的控制器直接管理的是 pod , 有编号
  2. k8s 通过 Headless Service 为这些有编号的 pod 生成有编号的 DNS 
  3. StatefulSet 为有编号的 pod 分配有编号的 pvc 


## 网路
一个容器可看到的网络栈，被隔离在自己的 Network 
网络栈： 网卡（Network Interface）, 回环设备(Loopback Device), 路由表（Routing table） 和 iptables.

大多数下我们都希望容器进程能使用自己的 Network namespace 里的网络栈，即：拥有自己的ip和端口

被隔离的容器如何跟其他 Network namespace 里的容器通信呢。
两台：连线
多台：交换机
Linux 能够起到虚拟交换机作用的网络设备是网桥(Bridge), 工作在数据链路层的设备，根据 mac 地址将数据包转发到网桥的不同端口

docker 会默认在宿主机上创建一个 docker0 的网桥，凡是链接在 docker0 网桥上的容器，就可以通过它来通信。
如何链接？需要 veth pair 的虚拟设备，可以看作是链接不同 Network namespace 的网线。

网络插件：通过某种方法，把不同宿主机上的特殊设备连通，从而达到容器跨主机通信的目的。

k8s: cni 网桥， 宿主机上默认名称：cni0


## Service
1. ClusterIP 

Service 由 kube-proxy 组件，加上 iptables 来共同完成的。

2. 有 0x4000 标记的会被 SNAT

## 外界联通 Service
所谓 Service 的访问入口，其实就是每台主机上由 kube-proxy 生成的 iptables 规则，以及 kube-dns 生成的 dns 记录，而一旦离开了这个集群，这些信息对用户来说，也就自然没有作用了。
如何从外部 k8s 集群之外，访问到 k8s 里创建的 Service.
1. NodePort


## Ingress 
Service 的 Service
IngressRule




1. 弄一个 service ， 多个副本，查看一下iptables 


