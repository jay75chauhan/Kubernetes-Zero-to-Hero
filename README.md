# Kubernetes-Zero-to-Hero
Creating this repo with an intent to make Kubernetes easy for begineers. This is a work-in-progress repo.


## --- KUBERNETES Install Steps (kubeadm)---
```markdown


## Step1:

### On Master and slave


apt-get update -y
apt-get install docker.io -y
service docker restart
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" >/etc/apt/sources.list.d/kubernetes.list
apt-get update
apt install kubeadm=1.20.0-00 kubectl=1.20.0-00 kubelet=1.20.0-00 -y
```

## Step2:

### On Master node:

```bash
kubeadm init --pod-network-cidr=192.168.0.0/16
```

## Step3:

### On Master node:

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Step4:

### On Master node:

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.1/manifests/calico.yaml
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.49.0/deploy/static/provider/baremetal/deploy.yaml
```


### Kubectl is a command-line tool used to interact with Kubernetes clusters. It allows you to deploy and manage applications, inspect and modify cluster resources, and perform various administrative tasks. Here are some basic kubectl commands:

1. **kubectl get**: Retrieve information about resources in the cluster.
   - `kubectl get pods`: List all pods in the cluster.
   - `kubectl get deployments`: List all deployments in the cluster.
   - `kubectl get services`: List all services in the cluster.
   - `kubectl get nodes`: List all nodes in the cluster.

2. **kubectl describe**: Get detailed information about a specific resource.
   - `kubectl describe pod <pod-name>`: Show detailed information about a specific pod.
   - `kubectl describe deployment <deployment-name>`: Show detailed information about a specific deployment.
   - `kubectl describe service <service-name>`: Show detailed information about a specific service.

3. **kubectl create**: Create a resource from a file or inline definition.
   - `kubectl create -f <file.yaml>`: Create a resource defined in a YAML file.
   - `kubectl create deployment <deployment-name> --image=<image-name>`: Create a deployment from a specified container image.

4. **kubectl delete**: Delete a resource.
   - `kubectl delete pod <pod-name>`: Delete a specific pod.
   - `kubectl delete deployment <deployment-name>`: Delete a specific deployment.
   - `kubectl delete service <service-name>`: Delete a specific service.

5. **kubectl apply**: Apply changes to a resource.
   - `kubectl apply -f <file.yaml>`: Apply changes to a resource defined in a YAML file.
   - `kubectl apply -f <dir>`: Apply changes to all resources defined in a directory.

6. **kubectl logs**: View the logs of a pod.
   - `kubectl logs <pod-name>`: Print the logs of a specific pod.

7. **kubectl exec**: Execute a command inside a container of a pod.
   - `kubectl exec -it <pod-name> -- <command>`: Run an interactive shell in a specific pod.

8. **kubectl port-forward**: Forward local ports to a pod.
   - `kubectl port-forward <pod-name> <local-port>:<pod-port>`: Forward a local port to a specific pod.

## Kubernetes Installation Using KOPS on EC2

### Create an EC2 instance or use your personal laptop.

Dependencies required 

1. Python3
2. AWS CLI
3. kubectl

###  Install dependencies

```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

```
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
```

```
sudo apt-get update
sudo apt-get install -y python3-pip apt-transport-https kubectl
```

```
pip3 install awscli --upgrade
```

```
export PATH="$PATH:/home/ubuntu/.local/bin/"
```

### Install KOPS (our hero for today)

```
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64

chmod +x kops-linux-amd64

sudo mv kops-linux-amd64 /usr/local/bin/kops
```

### Provide the below permissions to your IAM user. If you are using the admin user, the below permissions are available by default

1. AmazonEC2FullAccess
2. AmazonS3FullAccess
3. IAMFullAccess
4. AmazonVPCFullAccess

### Set up AWS CLI configuration on your EC2 Instance or Laptop.

Run `aws configure`

## Kubernetes Cluster Installation 

Please follow the steps carefully and read each command before executing.

### Create S3 bucket for storing the KOPS objects.

```
aws s3api create-bucket --bucket kops-abhi-storage --region us-east-1
```

### Create the cluster 

```
kops create cluster --name=demok8scluster.k8s.local --state=s3://kops-abhi-storage --zones=us-east-1a --node-count=1 --node-size=t2.micro --master-size=t2.micro  --master-volume-size=8 --node-volume-size=8
```

### Important: Edit the configuration as there are multiple resources created which won't fall into the free tier.

```
kops edit cluster myfirstcluster.k8s.local
```

Step 12: Build the cluster

```
kops update cluster demok8scluster.k8s.local --yes --state=s3://kops-abhi-storage
```

This will take a few minutes to create............

After a few mins, run the below command to verify the cluster installation.

```
kops validate cluster demok8scluster.k8s.local
```

