# 🧠 Ansible Cluster with Docker & Kubernetes

This project demonstrates how to build an **Ansible-based infrastructure** using both **Docker containers** and **Kubernetes pods**. It includes the setup of a master and multiple nodes, passwordless SSH communication, and executing Ansible playbooks across both environments.

---

## 📁 Project Structure

```bash
ansible-docker-cluster/
│
├── Dockerfile
├── install_pkg.yml            
├── inventory                  
├── README.md
│
└── k8s/
    ├── master-pod.yaml
    ├── node1-pod.yaml
    ├── node2-pod.yaml
    ├── ansible-service.yaml   
    └── namespace.yaml
```

---

## ⚙️ Technologies Used

- 🐳 Docker
- ☸️ Kubernetes (Minikube or local cluster)
- 🔧 Ansible
- 🔐 OpenSSH (passwordless setup)
- 📦 Custom Ansible Docker Image: `tpathak21/ansible-image:latest`

---

## 🔧 Phase 1: Docker-Based Ansible Cluster

### 🧱 Step 1: Build Docker Image

```bash
docker build -t rhel9-ansible .
docker tag rhel9-ansible tpathak21/ansible-image:latest
docker push tpathak21/ansible-image:latest
```

### 🪵 Step 2: Create Docker Network

```bash
docker network create ansible-net
```

### 🚀 Step 3: Run Containers

```bash
docker run -d --name ansible-master --network ansible-net -p 2222:22 rhel9-ansible
docker run -d --name ansible-node1  --network ansible-net -p 2223:22 rhel9-ansible
docker run -d --name ansible-node2  --network ansible-net -p 2224:22 rhel9-ansible
```

### 🔑 Step 4: Setup Passwordless SSH

Inside the master container:

```bash
ssh-keygen
ssh-copy-id root@ansible-node1
ssh-copy-id root@ansible-node2
```

### 📄 Step 5: Add Ansible Inventory

Create an `inventory` file:

```ini
[all]
ansible-node1 ansible_host=172.18.0.3 ansible_user=root
ansible-node2 ansible_host=172.18.0.4 ansible_user=root
```

### 📦 Step 6: Run Ansible Playbook

```bash
ansible-playbook -i inventory install_pkg.yml
```

---

## ☸️ Phase 2: Kubernetes-Based Ansible Cluster

### 🌐 Step 1: Create Namespace

```bash
kubectl create namespace ansible-project
```

### 📄 Step 2: Deploy Pods

```bash
kubectl apply -f k8s/namespace.yaml
kubectl apply -f k8s/master-pod.yaml
kubectl apply -f k8s/node1-pod.yaml
kubectl apply -f k8s/node2-pod.yaml
```

### 🔍 Step 3: Exec into Master Pod

```bash
kubectl exec -it ansible-master -n ansible-project -- /bin/bash
```

### 🔑 Step 4: SSH Key + Inventory + Playbook (Same as Docker)

Set up SSH key-based auth and run the same playbook.

---

## ✅ Output Verification

- ✔️ SSH works from master to all nodes
- ✔️ `ansible -m ping all` returns successful responses
- ✔️ Ansible installs `tree` package (or any other you define)
- ✔️ Pods and containers remain isolated

---

## 📌 Improvements (Optional)

- Add Jenkins Pipeline to automate playbook execution
- Use Kubernetes ConfigMaps for inventory/playbooks
- Expose Ansible master via LoadBalancer
- Use Ansible roles

---

## 👨‍💻 Author

- **Tejaswi Pathak**  
- 🔗 DockerHub: [tpathak21](https://hub.docker.com/u/tpathak21)
- LinkedIn: 
---


"# TejPATHAK" 
