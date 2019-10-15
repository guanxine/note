# etcd

- distributed reliable key-value store
- raft[https://raft.github.io]
- a single-member cluster of etcd
    使用 docker 的方式来运行 etcd 
    - primary gcr.io/etcd-development/etcd
    - secondary quay.io/coreos/etcd
```
rm -rf /tmp/etcd-data.tmp && mkdir -p /tmp/etcd-data.tmp && \
  docker rmi quay.io/coreos/etcd:v3.3.17 || true && \
  docker run \
  -p 2379:2379 \
  -p 2380:2380 \
  --mount type=bind,source=/tmp/etcd-data.tmp,destination=/etcd-data \
  --name etcd-v3.3.17 \
  quay.io/coreos/etcd:v3.3.17 \
  /usr/local/bin/etcd \
  --name s1 \
  --data-dir /etcd-data \
  --listen-client-urls http://0.0.0.0:2379 \
  --advertise-client-urls http://0.0.0.0:2379 \
  --listen-peer-urls http://0.0.0.0:2380 \
  --initial-advertise-peer-urls http://0.0.0.0:2380 \
  --initial-cluster s1=http://0.0.0.0:2380 \
  --initial-cluster-token tkn \
  --initial-cluster-state new
```
- 两个端口号: 
    - 2379: client 
    - 2380: server-to-server

- https://github.com/etcd-io/etcd/blob/master/Documentation/docs.md
- 使用
```
docker exec -ti xxx sh
/ # etcdctl cluster-health
member 59a9c584ea2c3f35 is healthy: got healthy result from http://0.0.0.0:2379
cluster is healthy
/ # etcdctl set foo bar
bar
/ # etcdctl get foo
bar

```