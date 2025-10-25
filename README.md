# Near-RT-RIC_installation_ubuntu22.04_OAI

In this Kube config error occurs , goes to localhost:8080, and pod will not be up

**Follow this:**
https://docs.o-ran-sc.org/projects/o-ran-sc-ric-plt-ric-dep/en/latest/installation-guides.html#ric-applications

**Clone this;**
git clone "https://gerrit.o-ran-sc.org/r/ric-plt/ric-dep"


# install kubernetes, kubernetes-CNI, helm and docker
cd ric-dep/bin
./install_k8s_and_helm.sh

**Pods doenot come up, then , in other terminal**

sudo apt update
sudo apt install -y containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null
sudo sed -i 's/^\(\s*SystemdCgroup = \)false/\1true/' /etc/containerd/config.toml
sudo systemctl enable --now containerd

sudo kubeadm reset -f || true
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 \
  --cri-socket=unix:///run/containerd/containerd.sock

**Give kubectl a kubeconfig (user + root)**

mkdir -p ~/.kube
sudo cp /etc/kubernetes/admin.conf ~/.kube/config
sudo chown "$USER:$USER" ~/.kube/config

sudo mkdir -p /root/.kube
sudo cp /etc/kubernetes/admin.conf /root/.kube/config
sudo chown root:root /root/.kube/config

**Install a CNI & allow scheduling on the control-plane**

kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/v0.24.2/Documentation/kube-flannel.yml
kubectl taint nodes --all node-role.kubernetes.io/control-plane:NoSchedule- || true



