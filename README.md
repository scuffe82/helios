# helios
The start of a new argo powered k8s cluster

# k8s setup
sudo swapoff -a
## disable swap

sudo modprobe overlay
sudo modprobe br_netfilter

sudo tee /etc/modules-load.d/k8s.conf <<EOF
overlay
br_netfilter
EOF

sudo tee /etc/sysctl.d/kubernetes.conf <<EOT
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOT

sudo sysctl --system

sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates

## containerd 2 setup

wget https://github.com/containerd/containerd/releases/download/v2.1.3/containerd-2.1.3-linux-amd64.tar.gz
tar Cxzvf /usr/local containerd-2.1.3-linux-amd64.tar.gz
wget  -P /usr/lib/systemd/system https://raw.githubusercontent.com/containerd/containerd/main/containerd.service
systemctl daemon-reload
systemctl enable --now containerd

wget https://github.com/opencontainers/runc/releases/download/v1.3.0/runc.amd64
install -m 755 runc.amd64 /usr/local/sbin/runc

wget https://github.com/containernetworking/plugins/releases/download/v1.7.1/cni-plugins-linux-amd64-v1.7.1.tgz
mkdir -p /opt/cni/bin
tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.7.1.tgz

containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1

## k8s setup

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/k8s.gpg
echo 'deb [signed-by=/etc/apt/keyrings/k8s.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | sudo tee /etc/apt/sources.list.d/k8s.list
sudo apt update
sudo apt install kubelet kubeadm kubectl -y
sudo apt mark hold kubelet kubeadm kubectl

kubeadm init --pod-network-cidr 192.168.0.0/16 --upload-certs --apiserver-cert-extra-sans helios,helios.spenceops.com,10.0.1.30,hermes,hermes.spenceops.com,10.0.1.20,spenceops.com,*.spenceops.com