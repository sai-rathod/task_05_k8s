# Task-05:
# React App Kubernetes Deployment

This repository contains Kubernetes manifests to deploy a React application with 3 replicas and expose it via NodePort service.

## Prerequisites

- Kubernetes cluster (cloud-based or local)
- `kubectl` CLI installed and configured
- For cloud deployments: Access to instance/node IPs
- For local deployments: Minikube, Kind, or Docker Desktop with Kubernetes enabled

## Files

- `deployment.yml` - Deployment configuration for the React app
- `service.yml` - NodePort service to expose the application

## Deployment Steps

### 1. Create Namespace

```bash
kubectl create namespace task-05
```

### 2. Apply Kubernetes Manifests

Apply both the deployment and service files:

```bash
kubectl apply -f deployment.yml
kubectl apply -f service.yml
```

Or apply all files in the directory:

```bash
kubectl apply -f .
```

### 3. Verify Deployment

Check if pods are running:

```bash
kubectl get pods -n task-05
```

Check service details:

```bash
kubectl get svc -n task-05
```

## Accessing the Application

### For Cloud-Based Kubernetes (AWS, GCP, Azure, etc.)

1. Get the NodePort assigned to the service:
   ```bash
   kubectl get svc myapp -n task-05
   ```
   Look for the port mapping (e.g., `3000:30XXX/TCP`)

2. Get your node/instance IP address:
   ```bash
   kubectl get nodes -o wide
   ```

3. Access the application:
   ```
   http://<NODE_IP>:<NODE_PORT>
   ```
   Example: `http://18.123.45.67:30123`

### For Local Kubernetes (Minikube, Kind, Docker Desktop)

#### Minikube

```bash
minikube service myapp -n task-05
```

This command will automatically open the application in your browser.

Or manually get the URL:
```bash
minikube service myapp -n task-05 --url
```

#### Docker Desktop

```bash
kubectl get svc myapp -n task-05
```

Access via `http://localhost:<NODE_PORT>`

#### Kind (Kubernetes in Docker)

Since Kind runs in containers, you need to port-forward:

```bash
kubectl port-forward -n task-05 svc/myapp 3000:3000
```

Then access via `http://localhost:3000`

## Application Details

- **Image**: `sairathod/new-app`
- **Container Port**: 3000
- **Replicas**: 3
- **Service Type**: NodePort
- **Resource Limits**:
  - Memory: 128Mi (request) / 256Mi (limit)
  - CPU: 100m (request) / 250m (limit)

## Troubleshooting

### Pods not running

```bash
kubectl describe pods -n task-05
kubectl logs <pod-name> -n task-05
```

### Service not accessible

1. Verify service endpoints:
   ```bash
   kubectl get endpoints myapp -n task-05
   ```

2. Check if pods are healthy:
   ```bash
   kubectl get pods -n task-05 -o wide
   ```

3. For cloud deployments, ensure security groups/firewall rules allow traffic on the NodePort

## Cleanup

To remove all resources:

```bash
kubectl delete namespace task-05
```

Or delete individual resources:

```bash
kubectl delete -f deployment.yml
kubectl delete -f service.yml
```

## Notes

- The NodePort will be automatically assigned in the range 30000-32767
- For production environments, consider using LoadBalancer or Ingress instead of NodePort
- Ensure your container image is publicly accessible or configure image pull secrets
