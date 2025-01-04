# OpenTelemetry Demo Project with AWS EKS

This project demonstrates the deployment and monitoring of a containerized application using OpenTelemetry, Docker, Kubernetes, and Amazon Web Services (AWS). The project was developed to showcase observability, modular deployment, and cloud-native best practices.

## Introduction

The OpenTelemetry Demo Project highlights the deployment of a cloud-native application using AWS services. The project includes monitoring and distributed tracing using OpenTelemetry and visualizations with Grafana and Jaeger. The application transitions from a Dockerized environment to a Kubernetes setup using Amazon EKS (Elastic Kubernetes Service).

## Project Features

- **Containerization**: Docker-based deployment of a multi-service application.
- **Observability**: Integrated OpenTelemetry for monitoring and distributed tracing.
- **Kubernetes Deployment**: Modular deployment with YAML configurations, optimized for Amazon EKS.
- **Scalability**: Supports horizontal scaling via Kubernetes node groups.
- **Access Methods**: Applications accessible via LoadBalancer, Port Forwarding, and Ingress.

## Phase 1: Kubernetes Deployment on Amazon EKS

### EKS Cluster Setup

- Configure an EC2 Instance as EKS Client and add necessary IAM role to EC2.

### Install Necessary Tools

Install `kubectl`, `eksctl`, and configure AWS CLI using the installation steps provided in the following script:
[Installation Steps](https://github.com/divya0702/opentelemetry/blob/5e768682273bb31b770da84033dcdea677c8ea94/phase_1/kubernetes/installation-steps.sh)

### Deploy EKS Cluster

Use the configuration file `cluster.yaml` to deploy the cluster:
[Cluster Configuration File](https://github.com/divya0702/opentelemetry/blob/5e768682273bb31b770da84033dcdea677c8ea94/phase_1/kubernetes/eks-cluster.yaml)

```bash
eksctl create cluster -f cluster.yaml
```

### Deploy OpenTelemetry Demo Application

1. Create namespace and deploy resources:
   ```bash
   kubectl create namespace otel-demo
   kubectl apply -f https://raw.githubusercontent.com/open-telemetry/opentelemetry-demo/main/kubernetes/opentelemetry-demo.yaml -n otel-demo
   ```

2. Accessing the Application:

#### Method 1: Port Forwarding
```bash
kubectl port-forward svc/my-otel-demo-frontendproxy 8080:8080
```

#### Method 2: Load Balancer
Modify the frontend service in `opentelemetry-demo.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend
  namespace: otel-demo
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 80
    targetPort: 8080
  selector:
    app: frontend
```

Apply the changes:
```bash
kubectl apply -f opentelemetry-demo.yaml -n otel-demo
```

Retrieve the LoadBalancer URL.

#### Method 3: Ingress
Set up an Ingress Controller (e.g., NGINX Ingress) to route traffic:

1. Create an `ingress.yaml` configuration file: [Ingress Configuration File](https://github.com/divya0702/opentelemetry/blob/5e768682273bb31b770da84033dcdea677c8ea94/phase_1/kubernetes/otel-demo-ingress.yaml)
2. Apply the Ingress configuration:
   ```bash
   kubectl apply -f ingress.yaml -n otel-demo
   ```
3. Retrieve the Ingress address:
   ```bash
   kubectl get ingress -n otel-demo
   ```

Using an Ingress Controller allows traffic routing from a single LoadBalancer to multiple services, reducing the overhead of managing multiple LoadBalancers.

## Phase 2: YAML Splitting and Modular Deployment

The monolithic `opentelemetry-demo.yaml` file was split into smaller files based on resource types:

- Example folders: `Deployments/`, `Services/`, `ConfigMaps/`

[Split YAML Files](https://github.com/divya0702/opentelemetry/tree/5e768682273bb31b770da84033dcdea677c8ea94/phase_2/split_yaml_files)

These files were uploaded to an S3 bucket as a ZIP file for modular deployment.

## Phase 4: Customizing Grafana Dashboards for EKS Monitoring

Using the Grafana instance provided by OpenTelemetry, detailed dashboards were created for monitoring pods, clusters, and nodes in the EKS environment. Notification alerts were generated using AWS SNS.

[Grafana Dashboard Configurations](https://github.com/divya0702/opentelemetry/tree/5e768682273bb31b770da84033dcdea677c8ea94/phase_4)

## Future Recommendations

- Use **Amazon Elastic Container Registry (ECR)** for managing Docker images.
- Explore **AWS Fargate** and **ECS** for a fully managed container orchestration setup.

## Conclusion

The project successfully demonstrated the use of OpenTelemetry for deploying and monitoring applications using Docker and Kubernetes on AWS. The phased approach ensured a clear understanding of containerization, orchestration, and observability practices. The project highlights the critical role of modular deployment strategies and cloud-native technologies in modern application development.

