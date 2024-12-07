# Kubernetes Simple Web App

This project demonstrates the deployment of a simple static web application in a AWS EKS environment. The solution includes Infrastructure as Code (IaC), Kubernetes manifests, and monitoring with Prometheus and Grafana.

## Prerequisites
- AWS account
- Terraform installed
- AWS CLI installed
- Kubectl installed
- Helm installed

## Deployment Steps

### Step 1: Set up the Infrastructure
- Navigate to the `terraform/` directory:
```
 cd terraform
```
- Create EKS terraform files referring to official terraform AWS documentation.
- Update variables.tf with your desired AWS region.
- Initialize and deploy Terraform:
```
terraform init

terraform apply
 
```
### Step 2: Build and Push Docker Image
- Navigate to the docker/ directory:
```
cd docker
```
- Build the Docker image:
```
docker build -t simple-web-app .
```
- Push the image to Docker Hub or ECR.

### Step 3: Deploy to Kubernetes

- Update your kubeconfig to connect to the EKS cluster:
```
aws eks --region <region> update-kubeconfig --name <cluster-name>
```
- Create a Kubernetes deployment file and services file in the kubernetes directory:
- Apply the Kubernetes manifests:
```
kubectl apply -f kubernetes/
```
- Access the application via the EXTERNAL-IP of the LoadBalancer:
```
kubectl get svc web-app-service
```
### Step 4: Set up Monitoring

- Deploy Prometheus and Grafana using Helm:
```
helm install prometheus-stack prometheus-community/kube-prometheus-stack --namespace monitoring
helm install grafana grafana/grafana --set adminPassword=admin --namespace monitoring

```
- Access Prometheus:
```
kubectl port-forward -n monitoring svc/prometheus-stack-kube-prometheus-prometheus 9090:9090
```
Open your browser and visit: http://localhost:9090
- Access Grafana:
```
kubectl port-forward -n monitoring svc/prometheus-stack-grafana 3000:80
```
Open your browser and visit: http://localhost:3000
### Step 5: Configure Prometheus and Grafana Dashboards

- Log in to Grafana and click Configuration → Data Sources → Add data source. <br>
Select Prometheus and configure it: <br>
URL: http://prometheus-stack-kube-prometheus-prometheus.monitoring.svc.cluster.local:9090 <br>
Save & Test.

- Import Prebuilt Dashboards: <br>
Go to Dashboards → Import.<br>
Use the following IDs for Kubernetes monitoring dashboards: <br>
Node Exporter Dashboard: 1860 <br>
Kubernetes Cluster Monitoring: 315
## Cleanup
- To avoid incurring costs, delete the infrastructure:
```
terraform destroy

