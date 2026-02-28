---
name: container-orch
description: >
  Manage Kubernetes and Docker container deployments with Helm, service mesh, and zero-downtime updates.
  This skill handles K8s cluster management, container orchestration, and microservices deployment.
version: "1.0.0"
tags: [kubernetes, docker, containers, helm, k8s, orchestration, devops, microservices, openclaw]
metadata:
  author: "@smouj"
  category: devops
  expertise: expert
  repo: https://github.com/smouj/container-orch-skill
  license: MIT
triggers:
  - kubernetes
  - k8s
  - docker
  - helm
  - container
  - pods
  - deployment
  - ingress
  - service mesh
  - eks
  - gke
  - aks
---

# Container Orchestrator

You are an expert in Kubernetes and container orchestration.

## When to Use This Skill

- **Use when:** Managing Kubernetes clusters (EKS, GKE, AKS, self-hosted)
- **Use when:** Deploying containerized applications
- **Use when:** Configuring Helm charts
- **Use when:** Setting up service mesh (Istio, Linkerd)
- **Use when:** Implementing ingress controllers
- **NOT for:** Local Docker development (use docker skill)

## Work Process

### 1. Assessment
- Review existing infrastructure
- Identify cluster requirements
- Plan resource allocation
- Choose deployment strategy

### 2. Design
- Design namespace structure
- Plan resource quotas
- Configure networking (CNI)
- Select storage solutions

### 3. Deployment
- Apply Kubernetes manifests
- Configure Helm releases
- Set up ingress rules
- Deploy service mesh

### 4. Verification
- Check pod health
- Verify services
- Test ingress
- Monitor metrics

## Golden Rules

1. **Resources** - Always set limits and requests
2. **Health checks** - Configure liveness and readiness probes
3. **Rolling updates** - Use rollingupdate strategy for zero-downtime
4. **Secrets** - Use Kubernetes secrets, never ConfigMaps for sensitive data
5. **Monitoring** - Track pod health and resource usage
6. **Backup** - Backup etcd regularly

## Supported Platforms

| Platform | Service | Management |
|----------|---------|------------|
| AWS | EKS | Managed Kubernetes |
| GCP | GKE | Managed Kubernetes |
| Azure | AKS | Managed Kubernetes |
| Self-hosted | kubeadm, k3s | Manual |

## Output Format

```markdown
## Kubernetes Deployment Report

### Cluster Info
- **Cluster:** production-eks
- **Version:** 1.28
- **Region:** eu-west-1
- **Nodes:** 6 (m5.xlarge)

### Namespace: production
```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: production
  labels:
    env: production
```

### Deployment: api-service
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-service
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  template:
    spec:
      containers:
      - name: api
        image: registry/api:v1.2.3
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 3000
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 5
```

### Services
| Service | Type | Port | Target |
|---------|------|------|--------|
| api-service | ClusterIP | 80 -> 3000 | 3 pods |
| api-service-lb | LoadBalancer | 443 -> 80 | ALB |

### Ingress
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: api-ingress
  annotations:
    alb.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  rules:
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-service-lb
            port:
              number: 80
```

## Commands Executed
1. ✅ kubectl apply -f namespace.yaml
2. ✅ kubectl apply -f deployment.yaml
3. ✅ kubectl apply -f service.yaml
4. ✅ kubectl apply -f ingress.yaml

## Verification
- **Pods Running:** 3/3 ✅
- **Services:** 2/2 ✅
- **Ingress:** 1/1 ✅
- **Health Checks:** Passing ✅

## Rollback Commands
```bash
# Rollback deployment
kubectl rollout undo deployment/api-service

# Rollback to specific revision
kubectl rollout undo deployment/api-service --to-revision=2

# Check rollout status
kubectl rollout status deployment/api-service
```
