# cloud-native-workshop
workshop to get started with cloud native

### local Mutli Node Kubernetes Cluster

multipass launch -c 6 -m 16G --disk 35G -n local-cluster

multipass shell local-cluster

sudo apt-get update && sudo apt-get upgrade -y


sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release 


curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=arm64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null


sudo apt-get update && sudo apt-get install -y docker-ce docker-ce-cli containerd.io

sudo usermod -aG docker $USER

//logout or restart or sudo docker ps sudo chown $USER /var/run/docker.sock or sudo chmod 777 /var/run/docker.sock
sudo docker run hello-world


sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update && sudo apt-get install -y kubectl

kubectl version --client

sudo nano ~/.bashrc
alias k="kubectl"
source ~/.bashrc


curl -s https://raw.githubusercontent.com/rancher/k3d/main/install.sh | bash


wget https://raw.githubusercontent.com/rancher/k3d/main/docs/usage/guides/calico.yaml


k3d cluster create local-k8s --servers 1 --agents 1 --registry-create \
  --k3s-server-arg "--no-deploy=traefik" \
  --k3s-server-arg "--flannel-backend=none"\
  --volume "$(pwd)/calico.yaml:/var/lib/rancher/k3s/server/manifests/calico.yaml"
  
  
 sudo apt install jq -y

#### determine loadbalancer ingress range
cidr_block=$(docker network inspect k3d-local-k8s | jq '.[0].IPAM.Config[0].Subnet' | tr -d '"')
cidr_base_addr=${cidr_block%???}
ingress_first_addr=$(echo $cidr_base_addr | awk -F'.' '{print $1,$2,255,0}' OFS='.')
ingress_last_addr=$(echo $cidr_base_addr | awk -F'.' '{print $1,$2,255,255}' OFS='.')
ingress_range=$ingress_first_addr-$ingress_last_addr

echo $ingress_range

#### deploy metallb 
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/metallb.yaml

çconfigure metallb ingress address range
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - $ingress_range
EOF

sudo apt install jq -y

#### determine loadbalancer ingress range
cidr_block=$(docker network inspect k3d-local-k8s | jq '.[0].IPAM.Config[0].Subnet' | tr -d '"')
cidr_base_addr=${cidr_block%???}
ingress_first_addr=$(echo $cidr_base_addr | awk -F'.' '{print $1,$2,255,0}' OFS='.')
ingress_last_addr=$(echo $cidr_base_addr | awk -F'.' '{print $1,$2,255,255}' OFS='.')
ingress_range=$ingress_first_addr-$ingress_last_addr

echo $ingress_range

#### deploy metallb 
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/metallb.yaml

#### configure metallb ingress address range
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - $ingress_range
EOF

#### create a deployment (i.e. nginx)
kubectl create deployment nginx --image=nginx

#### expose the deployments using a LoadBalancer
kubectl expose deployment nginx --port=80 --type=LoadBalancer

#### obtain the ingress external ip
external_ip=$(kubectl get svc nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

#### test the loadbalancer external ip
curl $external_ipcreate a deployment (i.e. nginx)
kubectl create deployment nginx --image=nginx

#### expose the deployments using a LoadBalancer
kubectl expose deployment nginx --port=80 --type=LoadBalancer

#### obtain the ingress external ip
external_ip=$(kubectl get svc nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

sudo apt install jq -y

#### determine loadbalancer ingress range
cidr_block=$(docker network inspect k3d-local-k8s | jq '.[0].IPAM.Config[0].Subnet' | tr -d '"')
cidr_base_addr=${cidr_block%???}
ingress_first_addr=$(echo $cidr_base_addr | awk -F'.' '{print $1,$2,255,0}' OFS='.')
ingress_last_addr=$(echo $cidr_base_addr | awk -F'.' '{print $1,$2,255,255}' OFS='.')
ingress_range=$ingress_first_addr-$ingress_last_addr

echo $ingress_range

#### deploy metallb 
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/namespace.yaml
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.10.2/manifests/metallb.yaml

#### configure metallb ingress address range
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: default
      protocol: layer2
      addresses:
      - $ingress_range
EOF

#### create a deployment (i.e. nginx)
kubectl create deployment nginx --image=nginx

#### expose the deployments using a LoadBalancer
kubectl expose deployment nginx --port=80 --type=LoadBalancer

#### obtain the ingress external ip
external_ip=$(kubectl get svc nginx -o jsonpath='{.status.loadBalancer.ingress[0].ip}')

#### test the loadbalancer external ip
curl $external_ip test the loadbalancer external ip
curl $external_ip


