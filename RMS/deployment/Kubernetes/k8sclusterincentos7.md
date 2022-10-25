This wiki has all the steps to install docker, kubernetes and form a cluste between different centos machines.

Before you start the cluster, you must know the IpaAdress of each machine.

For example:

kubemaster: 192.168.1.99
kubenode1: 192.168.1.109
kubenode2: 192.168.1.167 

** Step 1: **  Configure your /etc/hosts file, so that each machine can ping one another via hostname.

*sudo vim /etc/hosts*

192.168.1.99 kubemaster
192.168.1.109 kube2
192.168.1.167 kube3

** Step 2 ** Disable SELinux and swap

Now disable both SELinux and swap. On all three machines, issue the following commands:

*sudo setenforce 0*
*sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux*

Next, disable swap (on all three machines) with the following command:
swapoff -a

To ensure that swap isn't re-enabled during a reboot on each server. Open up the /etc/fstab and comment out the swap entry like this:

# /dev/mapper/centos-swap swap swap defaults 0 0

** Step 3:** Enable br_netfilter

For enabling the br_netfilter kernel module on all three servers. This is done with the following commands:

modprobe br_netfilter
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables

** Step 3 ** Install Docker-ce

To install the necessary Docker tool. On all three machines, install the Docker-ce dependencies with the following command:
yum install -y yum-utils device-mapper-persistent-data lvm2

Next, add the Docker-ce repository with the command:
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

Install Docker-ce with the command:
yum install -y docker-ce

** Step 4 ** Install Kubernetes

This is also done on all three servers. First need to create a repository entry for yum. To do this, issue the command vim /etc/yum.repos.d/kubernetes.repo and then add the following contents:

*[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg
        https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg*

Save and close that file. Install Kubernetes with the command:
yum install -y kubelet kubeadm kubectl

Once the installation completes, reboot all three machines. As soon as each machine has rebooted, log back in and su to the root user.

** Step 5 ** Cgroup changes
To ensure that both Docker-ce and Kubernetes belong to the same control group (cgroup). By default, Docker should already belong to cgroupfs (you can check this with the command docker info | grep -i cgroup). To add Kubernetes to this, issue the command:

sed -i 's/cgroup-driver=systemd/cgroup-driver=cgroupfs/g' /etc/systemd/system/kubelet.service.d/10-kubeadm.conf

Restart the systemd daemon and the kubelet service with the commands:
systemctl daemon-reload
systemctl restart kubelet

** Step 6 ** Initialize the Kubernetes cluster only in Masternode
To initialize the Kubernetes cluster. This is done on kubemaster (and only on that machine). On kubemaster, issue the command 

kubeadm init --apiserver-advertise-address=192.168.1.99 --pod-network-cidr=192.168.1.0/16

Note:
--apiserver-advertise-address = determines which IP address Kubernetes should advertise its API server on.
--pod-network-cidr = specify the range of IP addresses for the pod network. We're using the 'flannel' virtual network. If you want to use another pod network such as weave-net or calico, change the range IP address.


Once that completes, head over to kube2 and issue the command (adjusting the IP address to fit your needs):

kubeadm join 192.168.1.99:6443 --token TOKEN --discovery-token-ca-cert-hash DISCOVERY_TOKEN
Where TOKEN and DISCOVERY_TOKEN are the tokens displayed after the initialization command completes.

Configuring Kubernetes
Before Kubernetes can be used, we must take care of a bit of configuration. Issue the following three commands (to create a new .kube configuration directory, copy the necessary configuration file, and give the file the proper ownership):
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
Deploy flannel network

Now we must deploy the flannel network to the cluster with the command:
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml