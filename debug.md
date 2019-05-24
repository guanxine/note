
1.
```
[root@k8s-master01 gx]# kubectl get pods -l app=test-k8s
NAME                        READY   STATUS    RESTARTS   AGE
test-k8s-55d65cc5cd-rwdhr   1/1     Running   0          84m
test-k8s-55d65cc5cd-z5ncj   1/1     Running   0          84m
```

2. 
```
[root@k8s-master01 gx]# wget -O- test-k8s
--2019-05-24 16:23:20--  http://test-k8s/
Resolving test-k8s (test-k8s)... failed: Name or service not known.
wget: unable to resolve host address ‘test-k8s
```