# Automating Kubernetes Installation on Debian

This script automates the installation of Kubernetes on Debian and Debian-based distributions. It also installs Docker, disables swap, and configures the necessary Kubernetes repositories.

## Prerequisites

- Debian 10 or newer
- sudo access

## Steps

1. **Update Package Lists and Install Dependencies:**

   The script will update your package lists and install the required dependencies.

2. **Install Docker:**

   Docker is required to run Kubernetes containers. The script will add the Docker repository and install Docker.

3. **Disable Swap:**

   Kubernetes requires swap to be disabled. The script will disable swap and update the fstab file to ensure swap remains off after a reboot.

4. **Install Kubernetes:**

   The script will add the Kubernetes repository and install `kubelet`, `kubeadm`, and `kubectl`.

5. **Initialize Kubernetes:**

   After installing Kubernetes, the script will run `kubeadm init` to initialize the Kubernetes cluster.

6. **Configure Kubeconfig:**

   The script will set up the kubeconfig file so you can access your Kubernetes cluster.

## Script

Copy and run the following script to automate the installation process:

```sh
#!/bin/bash

# Update package lists
sudo apt update

# Install dependencies
sudo apt install -y apt-transport-https ca-certificates gnupg2 curl software-properties-common libssl-dev

# Add Docker repository
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"

# Update package lists
sudo apt update

# Install Docker
sudo apt install -y docker-ce
sudo systemctl status docker

# Disable swap
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab

# Add Kubernetes repository
sudo mkdir -p -m 755 /etc/apt/keyrings
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list

# Update package lists
sudo apt update

# Install Kubernetes
sudo apt install -y kubelet kubeadm kubectl

# Remove containerd configuration
sudo rm -rf /etc/containerd/config.toml
sudo systemctl restart containerd

# Initialize Kubernetes cluster
sudo kubeadm init

# Set up kubeconfig
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Notes

- Ensure to check the latest versions of Kubernetes and Docker to use versions that fit your requirements.
- After initializing Kubernetes, you may need to install a CNI network plugin and configure worker nodes.

If you have any questions or issues, feel free to contact us.
```

Feel free to make any additional changes as needed!
