# RTX5090D NGC Environment Setup Guide

**Author:** Chenyang Li  
**Date:** 2025-08-31

---

## 0. Boot Into Ubuntu

### 0.0  Reboot or power on the system and keep pressing `Delete` until it enters the BIOS menu.

### 0.1  Press `F7` to switch into **Advanced Mode**.

### 0.2  Navigate to the **Boot** menu and choose the option that boots into **Ubuntu**.

---

## 1. Install Nvidia Driver and CUDA Toolkit

### 1.0  Once booted into Ubuntu, log in as your default user.

### 1.1 Install necessary dependencies:
```bash
sudo apt update
sudo apt install build-essential
```

### 1.2  Install CUDA and Nvidia drivers:
```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cuda-toolkit-13-0
sudo apt-get install -y nvidia-open
```

> 💡 **Tips**:  
> - You can follow the official guide here: [NVIDIA CUDA Downloads](https://developer.nvidia.com/cuda-downloads)  
> - If the system hangs, simply reboot it manually.

### 1.3  Reboot the system:
```bash
sudo reboot
```

> 💡 If the `reboot` command doesn't work, manually restart the machine.

### 1.4  After rebooting, log in again as your default username.

### 1.5  Check if the installation is successful:
```bash
sudo systemctl start nvidia-persistenced
sudo systemctl enable nvidia-persistenced
sudo nvidia-smi
```

---

## 2. Install Nvidia Container Toolkit and Docker

### 2.0  Add the production repository:
```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

### 2.1  Install the NVIDIA Container Toolkit:
```bash
sudo apt-get update
export NVIDIA_CONTAINER_TOOLKIT_VERSION=1.17.8-1
sudo apt-get install -y     nvidia-container-toolkit=${NVIDIA_CONTAINER_TOOLKIT_VERSION}     nvidia-container-toolkit-base=${NVIDIA_CONTAINER_TOOLKIT_VERSION}     libnvidia-container-tools=${NVIDIA_CONTAINER_TOOLKIT_VERSION}     libnvidia-container1=${NVIDIA_CONTAINER_TOOLKIT_VERSION}
```

### 2.2  Remove old Docker packages and install Docker:
```bash
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done

sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu   $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 2.3  Verify Docker installation:
```bash
sudo docker run hello-world
```
> This command downloads a test image and runs it in a container. If successful, it prints a confirmation message and exits.

### 2.4  Configure the NVIDIA container runtime:
```bash
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

### 2.5  Test the GPU container environment:
```bash
sudo docker run --rm --runtime=nvidia --gpus all ubuntu nvidia-smi
```
> It should show output similar to running `nvidia-smi` directly on the host.

---

## 3. Try the NGC Container

### 3.0  Register an NGC account at:  
[https://catalog.ngc.nvidia.com/containers](https://catalog.ngc.nvidia.com/containers)

### 3.1  Once registered, pull the desired container, e.g.:  
[deepseek-r1 Container](https://catalog.ngc.nvidia.com/orgs/nim/teams/deepseek-ai/containers/deepseek-r1)

---
