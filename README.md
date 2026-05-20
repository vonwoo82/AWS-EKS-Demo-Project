# AWS-EKS-Demo-Project
Deploying a Scalable Application on Amazon EKS Using Terraform, Amazon ECR, and GitHub Actions

-------------------------------------------------------------------------------------------------------------------------------------------------------------

**Overview**

This project demonstrates how to deploy a containerized web application to an Amazon EKS (Elastic Kubernetes Service) cluster using a fully automated workflow.

It is designed as a cloud engineering project that showcases foundational skills:

• AWS infrastructure provisioning with Terraform

• Containerization with Docker

• Image storage using Amazon Elastic Container Registry (ECR)

• Kubernetes deployments (Deployment, Service, Namespace, Ingress)

• CI/CD pipeline using GitHub Actions

• Logging/monitoring with CloudWatch

----------------------------------------------------------------------------------------------------------------------------------------------------------------
**Architecture**

Incoming traffic flows through an AWS Application Load Balancer → routed to the EKS cluster → pods running the application → logs sent to CloudWatch.

Core AWS services used:

• VPC

• EKS + Managed Node Groups

• ECR

• ALB Ingress Controller

• CloudWatch

----------------------------------------------------------------------------------------------------------------------------------------------------------------

**Repository Structure**

```
aws-eks-project-demo/
├── app/
│   ├── Dockerfile
│   └── app.py
│
├── terraform/
│   ├── main.tf
│   ├── eks.tf
│   ├── vpc.tf
│   ├── variables.tf
│   └── outputs.tf
│
├── kubernetes/
│   ├── namespace.yaml
│   ├── deployment.yaml
│   ├── service.yaml
│   └── ingress.yaml
│
├── .github/workflows/
│   └── ci-cd.yaml
│
└── README.md
```

---------------------------------------------------------------------------------------------------------------------------------------------------------------

**Application Code**

This demo uses a simple Python Flask application.

app.py

```
from flask import Flask
app = Flask(__name__)

@app.route("/")
def home():
    return "Hello TW3!"

if __name__ == "__main__":
    app.run(host="0.0.0.0")
```
Dockerfile

```
FROM python:3.10-slim
WORKDIR /app
COPY . .
RUN pip install flask
CMD ["python", "app.py"]
```

-------------------------------------------------------------------------------------------------------------------------------------------------------------

**Terraform: Deploy AWS Infrastructure**

Terraform provisions:

• VPC + subnets

• EKS cluster

• IAM roles

• Managed node groups

To deploy:

```
cd terraform
```

```
terraform init
```

```
terraform plan
```

```
terraform apply
```

After apply finishes, configure kubectl:

```
aws eks update-kubeconfig --name <TW3_cluster> --region <us-east-1>
```

```
kubectl get nodes
```

-----------------------------------------------------------------------------------------------------------------------------------------------------------------
**Build and Push Docker Image to ECR**

Authenticate:

```
aws ecr get-login-password --region <us-east-1>
```

````
docker login --username AWS --password-stdin <ECR_URI>
```

Build and push:
```
docker build -t demo-app .
```

```
docker tag demo-app:latest <ECR_URI>:latest
```

```
docker push <ECR_URI>:latest
```

Update your deployment.yaml with your image:

```
image: <ECR_URI>:latest
```

----------------------------------------------------------------------------------------------------------------------------------------------------------------

**Deploy Kubernetes Resources**

Apply all manifests:
```
kubectl apply -f kubernetes/
```
Validate:

```
kubectl get pods -n demo
```

```
kubectl get svc -n demo
```

```
kubectl get ingress -n demo
```

-------------------------------------------------------------------------------------------------------------------------------------------------------------------
