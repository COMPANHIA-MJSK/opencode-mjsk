# Docker & Kubernetes Skill

## Purpose
Expert containerization with Docker and orchestration with Kubernetes. Covers development, deployment, and production-ready container strategies.

## Docker

### Dockerfile Best Practices
```dockerfile
# Multi-stage build
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM node:18-alpine AS runtime
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
USER node
CMD ["node", "dist/main.js"]
```

### Docker Compose
```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgres://user:pass@db:5432/mydb
    depends_on:
      db:
        condition: service_healthy
  
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 5s
      timeout: 5s
      retries: 5

volumes:
  postgres_data:
```

### Docker Commands
```bash
# Build
docker build -t myapp:latest .
docker build --no-cache -t myapp:v2 .

# Run
docker run -d -p 3000:3000 --name myapp myapp:latest
docker run -it --rm alpine sh

# Compose
docker compose up -d
docker compose down -v
docker compose logs -f app

# Clean
docker system prune -a
docker volume prune
```

## Kubernetes

### Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp:latest
        ports:
        - containerPort: 3000
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
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

### Service & Ingress
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 3000
  type: ClusterIP

---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myapp-service
            port:
              number: 80
```

### ConfigMap & Secret
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
data:
  DATABASE_HOST: "db-service"
  LOG_LEVEL: "info"

---
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secrets
type: Opaque
data:
  DATABASE_PASSWORD: cGFzc3dvcmQxMjM=
  API_KEY: c2VjcmV0a2V5
```

### Useful Commands
```bash
# Pods
kubectl get pods -o wide
kubectl describe pod <pod-name>
kubectl logs -f <pod-name>
kubectl exec -it <pod-name> -- sh

# Deployments
kubectl rollout status deployment/myapp
kubectl rollout undo deployment/myapp
kubectl scale deployment/myapp --replicas=5

# Debugging
kubectl get events --sort-by='.lastTimestamp'
kubectl top pods
kubectl port-forward svc/myapp-service 3000:80
```

## Best Practices
- Use multi-stage builds to reduce image size
- Never run containers as root in production
- Use Kubernetes namespaces for isolation
- Implement proper resource limits
- Use Helm charts for complex deployments
- Monitor with Prometheus and Grafana
- Implement GitOps with ArgoCD or Flux
