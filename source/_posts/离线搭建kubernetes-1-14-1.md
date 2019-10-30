---
title: 离线搭建kubernetes 1.14.1
date: 2019-05-14 13:06:43
tags: [kubernetes,docker]
---

对于私有云来讲，如何离线安装kubernetes是个关键性的问题。
<!-- more -->

## linux 安装 kubernetes

### linux 服务器连接（这里显示实际情况的服务器）

```bash
192.1.80.39
root/xxxxxxx
```

### linux 服务器状态

```bash
# 操作系统版本
cat /etc/redhat-release
CentOS Linux release 7.5.1804 (Core)

# 内核版本
cat /proc/version
Linux version 3.10.0-862.3.3.el7.x86_64 (builder@kbuilder.dev.centos.org) (gcc version 4.8.5 20150623 (Red Hat 4.8.5-28) (GCC) ) #1 SMP Fri Jun 15 04:15:27 UTC 2018

# 内存情况
free -m
              total        used        free      shared  buff/cache   available
Mem:          64148        1981       59611         116        2556       61404
Swap:         32191           0       32191

# 磁盘情况
df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   50G  4.3G   46G   9% /
devtmpfs                  32G     0   32G   0% /dev
tmpfs                     32G     0   32G   0% /dev/shm
tmpfs                     32G  114M   32G   1% /run
tmpfs                     32G     0   32G   0% /sys/fs/cgroup
/dev/sda5               1014M  176M  839M  18% /boot
/dev/sda2                 95M   34M   62M  36% /boot/efi
/dev/mapper/centos-home  2.6T   34M  2.6T   1% /home
tmpfs                    6.3G     0  6.3G   0% /run/user/0

# 进程使用情况
top
top - 09:34:06 up 164 days, 17:31,  1 user,  load average: 2.28, 0.65, 0.25
Tasks: 385 total,   2 running, 380 sleeping,   3 stopped,   0 zombie
%Cpu(s):  0.2 us,  0.2 sy,  0.0 ni, 99.6 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem : 65688368 total, 61034436 free,  2036496 used,  2617436 buff/cache
KiB Swap: 32964604 total, 32964604 free,        0 used. 62870972 avail Mem

12135 rabbitmq  20   0   20.6g 223892   3860 S   8.6  0.3   2656:48 beam.smp
12135 rabbitmq  20   0   20.6g 224100   3860 S  11.6  0.3   2656:48 beam.smp
17431 root      20   0  162244   2596   1588 R   0.7  0.0   0:00.11 top
38690 root      20   0 2479544  41292  12032 S   0.7  0.1 277:04.70 docker-containe
38673 root      20   0 2924172  85804  34072 S   0.3  0.1  63:05.48 dockerd
    3 root      20   0       0      0      0 S   0.0  0.0   0:03.24 ksoftirqd/0
    ...  
```

### linux 防火墙配置(firewall)(如关闭firewall防火墙时可忽略该配置项)

```bash
# 查看firewall
firewall-cmd --list-all

# 开放端口
firewall-cmd --permanent --add-port=6443/tcp
firewall-cmd --permanent --add-port=2379-2380/tcp
firewall-cmd --permanent --add-port=10250/tcp
firewall-cmd --permanent --add-port=10251/tcp
firewall-cmd --permanent --add-port=10252/tcp
firewall-cmd --permanent --add-port=10255/tcp
firewall-cmd --permanent --add-port=9868/tcp
firewall-cmd --permanent --add-port=9868/udp
firewall-cmd --reload
modprobe br_netfilter
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
```

### 安装docker-ce

[安装docker-ce文档](https://docs.docker.com/install/linux/docker-ce/centos/)

### kubernetes 工具

- kubeadm 集群部署工具
- kubelet 集群节点docker管理服务
- kubectl 集群管理工具，通过command来管理集群

### kubernetes 集群

- master 主节点是集群里运行控制面的机器，包括etcd（集群的数据库）和API服务(kubectl CLI与之交互)
- slave

### 本地安装kubeadm, kubectl, kubelet 工具

```bash
# 文件路径
~/k8s
# 本地运行
cd ~/k8s
# 安装本地rpm文件
yum -y localinstall *.rpm
# 运行结果
Loaded plugins: fastestmirror
Examining 53edc739a0e51a4c17794de26b13ee5df939bd3161b37f503fe2af8980b41a89-cri-tools-1.12.0-0.x86_64.rpm: cri-tools-1.12.0-0.x86_64
Marking 53edc739a0e51a4c17794de26b13ee5df939bd3161b37f503fe2af8980b41a89-cri-tools-1.12.0-0.x86_64.rpm to be installed
Examining 548a0dcd865c16a50980420ddfa5fbccb8b59621179798e6dc905c9bf8af3b34-kubernetes-cni-0.7.5-0.x86_64.rpm: kubernetes-cni-0.7.5-0.x86_64
Marking 548a0dcd865c16a50980420ddfa5fbccb8b59621179798e6dc905c9bf8af3b34-kubernetes-cni-0.7.5-0.x86_64.rpm to be installed
Examining 5c6cb3beb5142fa21020e2116824ba77a2d1389a3321601ea53af5ceefe70ad1-kubectl-1.14.1-0.x86_64.rpm: kubectl-1.14.1-0.x86_64
Marking 5c6cb3beb5142fa21020e2116824ba77a2d1389a3321601ea53af5ceefe70ad1-kubectl-1.14.1-0.x86_64.rpm to be installed
Examining 9e1af74c18311f2f6f8460dbd1aa3e02911105bfd455416381e995d8172a0a01-kubeadm-1.14.1-0.x86_64.rpm: kubeadm-1.14.1-0.x86_64
Marking 9e1af74c18311f2f6f8460dbd1aa3e02911105bfd455416381e995d8172a0a01-kubeadm-1.14.1-0.x86_64.rpm to be installed
Examining conntrack-tools-1.4.4-4.el7.x86_64.rpm: conntrack-tools-1.4.4-4.el7.x86_64
Marking conntrack-tools-1.4.4-4.el7.x86_64.rpm to be installed
Examining e1e8f430609698d7ec87642179ab57605925cb9aa48d406da97dedfb629bebf2-kubelet-1.14.1-0.x86_64.rpm: kubelet-1.14.1-0.x86_64
Marking e1e8f430609698d7ec87642179ab57605925cb9aa48d406da97dedfb629bebf2-kubelet-1.14.1-0.x86_64.rpm to be installed
Examining libnetfilter_cthelper-1.0.0-9.el7.x86_64.rpm: libnetfilter_cthelper-1.0.0-9.el7.x86_64
Marking libnetfilter_cthelper-1.0.0-9.el7.x86_64.rpm to be installed
Examining libnetfilter_cttimeout-1.0.0-6.el7.x86_64.rpm: libnetfilter_cttimeout-1.0.0-6.el7.x86_64
Marking libnetfilter_cttimeout-1.0.0-6.el7.x86_64.rpm to be installed
Examining libnetfilter_queue-1.0.2-2.el7_2.x86_64.rpm: libnetfilter_queue-1.0.2-2.el7_2.x86_64
Marking libnetfilter_queue-1.0.2-2.el7_2.x86_64.rpm to be installed
Resolving Dependencies
--> Running transaction check
---> Package conntrack-tools.x86_64 0:1.4.4-4.el7 will be installed
---> Package cri-tools.x86_64 0:1.12.0-0 will be installed
---> Package kubeadm.x86_64 0:1.14.1-0 will be installed
---> Package kubectl.x86_64 0:1.14.1-0 will be installed
---> Package kubelet.x86_64 0:1.14.1-0 will be installed
---> Package kubernetes-cni.x86_64 0:0.7.5-0 will be installed
---> Package libnetfilter_cthelper.x86_64 0:1.0.0-9.el7 will be installed
---> Package libnetfilter_cttimeout.x86_64 0:1.0.0-6.el7 will be installed
---> Package libnetfilter_queue.x86_64 0:1.0.2-2.el7_2 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

==============================================================================================================================
 Package                Arch   Version       Repository                                                                  Size
==============================================================================================================================
Installing:
 conntrack-tools        x86_64 1.4.4-4.el7   /conntrack-tools-1.4.4-4.el7.x86_64                                        550 k
 cri-tools              x86_64 1.12.0-0      /53edc739a0e51a4c17794de26b13ee5df939bd3161b37f503fe2af8980b41a89-cri-tools-1.12.0-0.x86_64
                                                                                                                         18 M
 kubeadm                x86_64 1.14.1-0      /9e1af74c18311f2f6f8460dbd1aa3e02911105bfd455416381e995d8172a0a01-kubeadm-1.14.1-0.x86_64
                                                                                                                         38 M
 kubectl                x86_64 1.14.1-0      /5c6cb3beb5142fa21020e2116824ba77a2d1389a3321601ea53af5ceefe70ad1-kubectl-1.14.1-0.x86_64
                                                                                                                         41 M
 kubelet                x86_64 1.14.1-0      /e1e8f430609698d7ec87642179ab57605925cb9aa48d406da97dedfb629bebf2-kubelet-1.14.1-0.x86_64
                                                                                                                        122 M
 kubernetes-cni         x86_64 0.7.5-0       /548a0dcd865c16a50980420ddfa5fbccb8b59621179798e6dc905c9bf8af3b34-kubernetes-cni-0.7.5-0.x86_64
                                                                                                                         35 M
 libnetfilter_cthelper  x86_64 1.0.0-9.el7   /libnetfilter_cthelper-1.0.0-9.el7.x86_64                                   35 k
 libnetfilter_cttimeout x86_64 1.0.0-6.el7   /libnetfilter_cttimeout-1.0.0-6.el7.x86_64                                  39 k
 libnetfilter_queue     x86_64 1.0.2-2.el7_2 /libnetfilter_queue-1.0.2-2.el7_2.x86_64                                    45 k

Transaction Summary
==============================================================================================================================
Install  9 Packages

Total size: 255 M
Installed size: 255 M
Downloading packages:
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : libnetfilter_queue-1.0.2-2.el7_2.x86_64                                                                    1/9
  Installing : libnetfilter_cttimeout-1.0.0-6.el7.x86_64                                                                  2/9
  Installing : kubectl-1.14.1-0.x86_64                                                                                    3/9
  Installing : libnetfilter_cthelper-1.0.0-9.el7.x86_64                                                                   4/9
  Installing : conntrack-tools-1.4.4-4.el7.x86_64                                                                         5/9
  Installing : kubernetes-cni-0.7.5-0.x86_64                                                                                    6/9
  Installing : kubelet-1.14.1-0.x86_64                                                                                          7/9
  Installing : cri-tools-1.12.0-0.x86_64                                                                                        8/9
  Installing : kubeadm-1.14.1-0.x86_64                                                                                          9/9
  Verifying  : cri-tools-1.12.0-0.x86_64                                                                                        1/9
  Verifying  : libnetfilter_cthelper-1.0.0-9.el7.x86_64                                                                         2/9
  Verifying  : kubectl-1.14.1-0.x86_64                                                                                          3/9
  Verifying  : libnetfilter_cttimeout-1.0.0-6.el7.x86_64                                                                        4/9
  Verifying  : libnetfilter_queue-1.0.2-2.el7_2.x86_64                                                                          5/9
  Verifying  : kubeadm-1.14.1-0.x86_64                                                                                          6/9
  Verifying  : kubelet-1.14.1-0.x86_64                                                                                          7/9
  Verifying  : kubernetes-cni-0.7.5-0.x86_64                                                                                    8/9
  Verifying  : conntrack-tools-1.4.4-4.el7.x86_64                                                                               9/9

Installed:
  conntrack-tools.x86_64 0:1.4.4-4.el7       cri-tools.x86_64 0:1.12.0-0                 kubeadm.x86_64 0:1.14.1-0
  kubectl.x86_64 0:1.14.1-0                  kubelet.x86_64 0:1.14.1-0                   kubernetes-cni.x86_64 0:0.7.5-0
  libnetfilter_cthelper.x86_64 0:1.0.0-9.el7 libnetfilter_cttimeout.x86_64 0:1.0.0-6.el7 libnetfilter_queue.x86_64 0:1.0.2-2.el7_2

Complete!

# 启动kubernetes
sudo systemctl enable kubelet && sudo systemctl start kubelet
# 查看所需的docker images
kubeadm config images list
k8s.gcr.io/kube-apiserver:v1.14.1
k8s.gcr.io/kube-controller-manager:v1.14.1
k8s.gcr.io/kube-scheduler:v1.14.1
k8s.gcr.io/kube-proxy:v1.14.1
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd:3.3.10
k8s.gcr.io/coredns:1.3.1

# 将本地镜像load到docker（）
cd ~/export
# 解压zip文件
unzip export.zip
unzip kube-proxy.zip
# 加载镜像到本地docker仓库
sudo docker load < kube-apiserver:v1.14.1
sudo docker load < kube-controller-manager:v1.14.1
sudo docker load < kube-scheduler:v1.14.1
sudo docker load < kube-proxy:v1.14.1
sudo docker load < pause:3.1
sudo docker load < etcd:3.3.10
sudo docker load < coredns:1.3.1
```

### 初始化配置信息

```bash
# 关闭Selinux/firewalld
systemctl stop firewalld
systemctl disable firewalld
setenforce 0
sed -i "s/SELINUX=enforcing/SELINUX=disabled/g" /etc/selinux/config

# 关闭交换分区
swapoff -a
yes | cp /etc/fstab /etc/fstab_bak
cat /etc/fstab_bak |grep -v swap > /etc/fstab

# 设置网桥包经IPTables，core文件生成路径
echo """
vm.swappiness = 0
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.conf.all.rp_filter = 1
net.ipv4.ip_forward = 1
net.bridge.bridge-nf-call-ip6tables = 1
""" > /etc/sysctl.conf
sysctl -p

# 同步时间
yum install -y ntpdate
ntpdate -u ntp.api.bz

# 检查默认内核版本是否大于4.14，否则请调整默认启动参数
grub2-editenv list

# 清除message日志
cat /dev/null > /var/log/message
```

### 加载内核模块

```bash
cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack_ipv4

# 安装指定模块
yum install ipset
yum install ipvsadm
```

### kubeadm init 部署 master 节点（指定版本，POD范围）

```bash
# 开始安装
# --apiserver-advertise-address=192.1.80.39
kubeadm init --kubernetes-version=v1.14.1 --pod-network-cidr=10.244.0.0/16
# 重置kubelet设置
kubeadm reset -f
# 运行成功后记录 join 命令后续会用到
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.1.80.39:6443 --token 3hhj7y.4gwog5jefl7q9ij5 \
    --discovery-token-ca-cert-hash sha256:42cb7bbff4754625711d92bc1a1da61dbdb02abcb20f79e215630e2d83c31c95
```

## 参考资料

- [安装kubeadm](https://kubernetes.io/zh/docs/setup/independent/install-kubeadm/)
- [使用 kubeadm 创建一个单主集群](https://kubernetes.io/zh/docs/setup/independent/create-cluster-kubeadm/)
