# Docker
## 重启
```
systemctl daemon-reload 
systemctl restart docker
```
## 配置
```
cat > /etc/docker/daemon.json <<EOF
{
 "insecure-registries": ["192.168.205.209:5000"],
 "iptables":false
} 
```
## 私有仓库
```
启动私有仓库
docker run -d -p 5000:5000 --restart always -v /root/registry:/var/lib/registry --name registry registry:2

查看私有仓库内容
curl 127.0.0.1:5000/v2/_catalog

配置私有仓库的web服务
cat > /home/lzp/04.docker/registry_web/web-config.yml <<EOF
registry:
  # Docker registry url
  url: http://172.19.50.2:5000/v2
  # Docker registry fqdn
  name: 172.19.50.2:5000
  # To allow image delete, should be false
  readonly: false
  auth:
    # Disable authentication
    enabled: false
EOF

启动私有仓库的web服务
docker run -d --name registry-web -v /etc/localtime:/etc/localtime --restart=always -p 8080:8080 -v /root/04.docker/registry_web/web-config.yml:/conf/config.yml -e REGISTRY_READONLY=false hyper/docker-registry-web
```
## 把容器导出成镜像

```
docker commit abc123456789 image-new:latest
```
## 配置代理
```
vim /etc/systemd/system/multi-user.target.wants/docker.service
```
# 集群
## 1.主机名
```
hostnamectl hostname node182

cat >> /etc/hosts <<EOF
70.170.65.182 node182
70.170.65.184 node184
70.170.65.186 node186
70.170.65.188 node188
EOF
```
## 1.互信
```
ssh-keygen -t rsa -C "node182"
ssh-copy-id -i  ~/.ssh/id_rsa.pub root@70.170.65.184
```

## 2.同步时间
```
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
timedatectl set-ntp no
timedatectl set-time "2024-04-16 17:03:30"
hwclock -w

pool 70.176.67.55 iburst

systemctl stop ntp
apt install ntpdate
ntpdate 70.176.67.55
```

## 3.关闭swap
```
swapoff -a
```

## 4.禁用SELinux
```
apt install selinux-utils
setenforce 0
```

## 5.网络
```
cat <<EOF | tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables=1
net.bridge.bridge-nf-call-iptables=1
EOF

sysctl --system
```

## 6.k8s安装
```
apt-key add trusted.gpg
deb http://mirrors.aliyun.com/kubernetes/apt kubernetes-xenial main

apt install kubelet=1.23.0-00
apt install kubeadm=1.23.0-00
apt install kubectl=1.23.0-00
```

# master节点
## 1.初始化
```
kubeadm init \
--apiserver-advertise-address=70.170.65.184 \
--control-plane-endpoint=node1 \
--image-repository registry.aliyuncs.com/google_containers \
--kubernetes-version v1.23.0 \
--service-cidr=10.96.0.0/16 \
--pod-network-cidr=192.168.0.0/16
```

## 2.join
```
kubeadm token create --print-join-command
```
# 解决问题
## 删除terminating状态的pod
```
kubectl get namespace cert-manager -o json >tmp.json
kubectl proxy
curl -k -H "Content-Type: application/json" -X PUT --data-binary @tmp.json http://127.0.0.1:8001/api/v1/namespaces/cert-manager/finalize
```
## kubeadm reset
```
rm -rf /etc/cni/*
ifconfig vxlan.calico down
ip link delete vxlan.calico
```
## 设置节点
```
污点
kubectl taint nodes master node-role.kubernetes.io/master:NoSchedule
kubectl taint nodes master node-role.kubernetes.io/master:NoSchedule-

调度
kubectl cordon node188
kubectl uncordon node188

驱逐
kubectl drain node188
--force
--ignore-daemonsets
--delete-local-data

删除
kubectl delete node node188

重启kubelet
systemctl restart kubelet
```
