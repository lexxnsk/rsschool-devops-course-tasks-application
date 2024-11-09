
# The RS School - AWS DevOps Course. Project Documentation. Application part.

This repository contains all necessary resources and configurations to deploy and manage applications on a Kubernetes cluster, specifically using K3s. It includes Helm charts, Kubernetes manifests, and scripts for setting up and configuring applications on the cluster.

## File Structure
```
├── .github/workflows/
├── PR_descriptions/
├── screenshots/
├── .gitignore
├── README.md
├── jenkins-sa.yaml
├── jenkins-values.yaml
├── jenkins-volume.yaml
```

### Directory & File Overview

- **```.github/workflows/```**:  
  This directory is a special folder in a GitHub repository that contains YAML files defining GitHub Actions workflows. 
- **```PR_descriptions/```**:  
  This directory contains the descriptions for Pull Request.
- **```screenshots/```**:  
  This directory contains necessary screenshots and texts.
- **```.gitignore```**:  
  This file specifies which files or directories should be ignored by Git when tracking changes in a repository.
- **```README.md```**:  
  This file in GitHub serves as the primary documentation for a repository (you're reading it right now).
- **```jenkins-sa.yaml```**:  
  This file contains the configuration for a Service Account in a K3S cluster that is specifically used for Jenkins.
- **```jenkins-values.yaml```**:  
  This file contains configuration values that customize the deployment of Jenkins within a K3S cluster.
- **```jenkins-volume.yaml```**:  
  This file contains configuration to define persistent storage for Jenkins in a K3S environment.

### GitHub variables and GitHub Secrets variables
3. **K3S token variable** ```K3S_TOKEN``` is stored in GitHub Secrets. It was created using the following command:  
```gh secret set K3S_TOKEN --body "<K3S_TOKEN>" --repo lexxnsk/rsschool-devops-course-tasks```  

You can list variables and secrets using the following commands:  
```gh variable list --repo lexxnsk/rsschool-devops-course-tasks```  
```gh secret list --repo lexxnsk/rsschool-devops-course-tasks```  

---

## How to Use it automatically:
1. **GitHub Actions:**  
   Before committing, check the ```.github/workflows/task5-deployment.yml``` file and update the branch name to trigger the GitHub workflow automatically.​⬤

---
## Task 4 clarifications:
**K3S preparation:**
- You can change default K3S namespace by:
```kubectl config set-context --current --namespace=jenkins```
- You can install Jenkins using next commands:
```
helm repo add jenkins https://charts.jenkins.io
helm repo update
kubectl apply -f jenkins-volume.yaml
kubectl create namespace jenkins
kubectl apply -f jenkins-sa.yaml    
chart=jenkinsci/jenkins
helm install jenkins -n jenkins -f jenkins-values.yaml $chart
```
- You can describe pod and then check init container logs for debug:
```kubectl logs jenkins-0 -c init``` 
```kubectl describe pod jenkins-0```
- You can check current pods status and check persistency by:
```
kubectl get pods
kubectl delete pod jenkins-0
kubectl get pods
```
- You can check service by:
```
kubectl get svc jenkins -n jenkins
NAME      TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
jenkins   NodePort   10.43.125.237   <none>        8080:32000/TCP   27m
```
- You can check current K3s storage class using this command:  
```
kubectl get storageclass
NAME                   PROVISIONER                    RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
jenkins-pv             kubernetes.io/no-provisioner   Delete          WaitForFirstConsumer   false                  56m
local-path (default)   rancher.io/local-path          Delete          WaitForFirstConsumer   false                  62m
```
- Here is a simple NGINX reverse proxy config to be installed on Bastion Host:
```
sudo vi /etc/nginx/conf.d/jenkins.conf

server {
    listen 80;
    server_name jenkins.myslivets.ru;

    location / {
        proxy_pass http://10.0.2.10:32000;       # Forward requests to Jenkins
        proxy_http_version 1.1;                  # Use HTTP/1.1 for proxying
        proxy_set_header Upgrade $http_upgrade;  # Handle WebSocket connections
        proxy_set_header Connection 'upgrade';   # Handle WebSocket connections
        proxy_set_header Host $host;             # Preserve original Host header
        proxy_set_header X-Real-IP $remote_addr; # Pass the client IP address
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; # Preserve client IP
        proxy_set_header X-Forwarded-Proto $scheme; # Preserve protocol (http or https)
    }
}

sudo nginx -t
sudo systemctl restart nginx
``` 
- You can simulate Jenkins on Server Node by running a simple Python Server:
```
sudo python3 -m http.server 32000
```

---
## Task 5 clarifications:
**XXXXXXXXXXXXXXXXXXXXXXx:**
