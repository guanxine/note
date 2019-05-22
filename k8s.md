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
只是一个逻辑概念，是一组共享了某些资源的容器，共享的是同一个 network namespace, 并且可以声明共享同一个 volume. 之间靠（Infra（k8s.gcr.io/pause） 容器关联，它最先启动 ）

同一个pod的容器
1. 可以通过 localhost 进行通信
2. 看到的网络设备和 Infra 容器一样
3. 一个 pod 只有一个 ip 地址，也就是这个 pod 的 network namespace 对应的 ip 地址