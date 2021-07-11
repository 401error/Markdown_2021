# K8S安装



## 1.docker安装

1.移除旧版本
apt-get remove docker docker-engine docker-ce docker.io

2.安装依赖
sudo apt install apt-transport-https ca-certificates software-properties-common curl

3.添加密钥
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -

4.添加docker源
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu bionic stable"


5.更新 
apt-get update

6.安装docker
 apt install docker-ce

7.测试
docker version

下载阿里云镜像（此步骤是为了能够在docker里面下载tomcat等等）
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://ghl7q4ui.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker





## 安装kubernetes

* 配置k8s的更新源，推荐阿里云

```
echo "deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
sudo gpg --keyserver keyserver.ubuntu.com --recv-keys BA07F4FB #对安装包进行签名
sudo gpg --export --armor BA07F4FB | sudo apt-key add -
sudo apt-get update
```

* 关闭虚拟内存（这步很重要）

```
sudo swapoff -a #暂时关闭
nano /etc/fstab #永久关闭，注释掉swap那一行，推荐永久关闭
```

* 安装最新版的k8s：

```
apt-get install kubelet kubeadm kubectl kubernetes-cni
```

* 其中`kubeadm`用于初始化环境，`kubectl`用于操作`kubelet`。 设置开机启动：

```
sudo systemctl enable kubelet && systemctl start kubelet
```



## 2.配置网络

* 配置每台机器的`/etc/netplan/50-cloud-init.yaml`，

```
network:
    ethernets:
        ens33:
            addresses: [192.168.32.132/24] //该节点IP
            dhcp4: false
            gateway4: 192.168.32.2 //网关
            nameservers:
                addresses: [192.168.32.2] //DNS的IP
            optional: true
    version: 2
```





## 3 .master节点配置

* 使用`kubeadm init`进行初始化操作：

```shell
#修改IP地址为master节点的IP地址并配置pod地址
kubeadm init \
--apiserver-advertise-address=192.168.32.132 \
--image-repository registry.aliyuncs.com/google_containers  \
--pod-network-cidr=10.244.0.0/16 
```

* apiserver-advertise-address 为master节点IP
* image-repository ：配置国内镜像源
* pod-network-cidr ：配置集群内部子网 网段

* 拉不到的镜像手动拉



## 4.flannel网络配置