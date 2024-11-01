# prometheus-config.yaml

apiVersion: v1
kind: ConfigMap
metadata:
name: prometheus-config
data:
prometheus.yml: |
global:
scrape_interval: 15s
evaluation_interval: 15s

    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']

      - job_name: 'node-exporter'
        static_configs:
          - targets: ['node-exporter:9100']

      - job_name: 'sample-app'
        static_configs:
          - targets: ['sample-app:8080']

---

# prometheus-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
name: prometheus
spec:
replicas: 1
selector:
matchLabels:
app: prometheus
template:
metadata:
labels:
app: prometheus
spec:
containers: - name: prometheus
image: prom/prometheus:v2.45.0
ports: - containerPort: 9090
volumeMounts: - name: config
mountPath: /etc/prometheus/ - name: storage
mountPath: /prometheus/
volumes: - name: config
configMap:
name: prometheus-config - name: storage
emptyDir: {}

---

# prometheus-service.yaml

apiVersion: v1
kind: Service
metadata:
name: prometheus
spec:
selector:
app: prometheus
ports:

- port: 9090
  targetPort: 9090
  type: ClusterIP

---

# node-exporter-daemonset.yaml

apiVersion: apps/v1
kind: DaemonSet
metadata:
name: node-exporter
spec:
selector:
matchLabels:
app: node-exporter
template:
metadata:
labels:
app: node-exporter
spec:
containers: - name: node-exporter
image: prom/node-exporter:v1.6.1
ports: - containerPort: 9100
volumeMounts: - name: proc
mountPath: /host/proc
readOnly: true - name: sys
mountPath: /host/sys
readOnly: true
volumes: - name: proc
hostPath:
path: /proc - name: sys
hostPath:
path: /sys

---

# node-exporter-service.yaml

apiVersion: v1
kind: Service
metadata:
name: node-exporter
spec:
selector:
app: node-exporter
ports:

- port: 9100
  targetPort: 9100
  type: ClusterIP

---

# grafana-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
name: grafana
spec:
replicas: 1
selector:
matchLabels:
app: grafana
template:
metadata:
labels:
app: grafana
spec:
containers: - name: grafana
image: grafana/grafana:10.0.0
ports: - containerPort: 3000
env: - name: GF_SECURITY_ADMIN_PASSWORD
value: "admin123"
volumeMounts: - name: storage
mountPath: /var/lib/grafana
volumes: - name: storage
emptyDir: {}

---

# grafana-service.yaml

apiVersion: v1
kind: Service
metadata:
name: grafana
spec:
selector:
app: grafana
ports:

- port: 3000
  targetPort: 3000
  type: LoadBalancer

---

# sample-app.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
name: sample-app
spec:
replicas: 1
selector:
matchLabels:
app: sample-app
template:
metadata:
labels:
app: sample-app
spec:
containers: - name: sample-app
image: nginx
ports: - containerPort: 80
volumeMounts: - name: config
mountPath: /etc/nginx/conf.d
volumes: - name: config
configMap:
name: nginx-config

---

# nginx-config.yaml

apiVersion: v1
kind: ConfigMap
metadata:
name: nginx-config
data:
default.conf: |
server {
listen 80;
location /metrics {
stub_status on;
}
}

I'll help you create a comprehensive tutorial for setting up Prometheus and Grafana monitoring stack.

```yaml
# prometheus-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-config
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
      evaluation_interval: 15s

    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']

      - job_name: 'node-exporter'
        static_configs:
          - targets: ['node-exporter:9100']

      - job_name: 'sample-app'
        static_configs:
          - targets: ['sample-app:8080']

---
# prometheus-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: prometheus
spec:
  replicas: 1
  selector:
    matchLabels:
      app: prometheus
  template:
    metadata:
      labels:
        app: prometheus
    spec:
      containers:
        - name: prometheus
          image: prom/prometheus:v2.45.0
          ports:
            - containerPort: 9090
          volumeMounts:
            - name: config
              mountPath: /etc/prometheus/
            - name: storage
              mountPath: /prometheus/
      volumes:
        - name: config
          configMap:
            name: prometheus-config
        - name: storage
          emptyDir: {}

---
# prometheus-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: prometheus
spec:
  selector:
    app: prometheus
  ports:
    - port: 9090
      targetPort: 9090
  type: ClusterIP

---
# node-exporter-daemonset.yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
spec:
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      containers:
        - name: node-exporter
          image: prom/node-exporter:v1.6.1
          ports:
            - containerPort: 9100
          volumeMounts:
            - name: proc
              mountPath: /host/proc
              readOnly: true
            - name: sys
              mountPath: /host/sys
              readOnly: true
      volumes:
        - name: proc
          hostPath:
            path: /proc
        - name: sys
          hostPath:
            path: /sys

---
# node-exporter-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: node-exporter
spec:
  selector:
    app: node-exporter
  ports:
    - port: 9100
      targetPort: 9100
  type: ClusterIP

---
# grafana-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: grafana
spec:
  replicas: 1
  selector:
    matchLabels:
      app: grafana
  template:
    metadata:
      labels:
        app: grafana
    spec:
      containers:
        - name: grafana
          image: grafana/grafana:10.0.0
          ports:
            - containerPort: 3000
          env:
            - name: GF_SECURITY_ADMIN_PASSWORD
              value: "admin123"
          volumeMounts:
            - name: storage
              mountPath: /var/lib/grafana
      volumes:
        - name: storage
          emptyDir: {}

---
# grafana-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: grafana
spec:
  selector:
    app: grafana
  ports:
    - port: 3000
      targetPort: 3000
  type: LoadBalancer

---
# sample-app.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sample-app
  template:
    metadata:
      labels:
        app: sample-app
    spec:
      containers:
        - name: sample-app
          image: nginx
          ports:
            - containerPort: 80
          volumeMounts:
            - name: config
              mountPath: /etc/nginx/conf.d
      volumes:
        - name: config
          configMap:
            name: nginx-config

---
# nginx-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config
data:
  default.conf: |
    server {
        listen 80;
        location /metrics {
            stub_status on;
        }
    }
```

Here's the step-by-step tutorial to set up this monitoring stack:

1. **Prerequisites:**

   ```bash
   # Create a monitoring namespace
   kubectl create namespace monitoring

   # Set context to monitoring namespace
   kubectl config set-context --current --namespace=monitoring
   ```

2. **Deploy Prometheus:**

   ```bash
   # Create Prometheus configuration
   kubectl apply -f prometheus-config.yaml

   # Deploy Prometheus
   kubectl apply -f prometheus-deployment.yaml
   kubectl apply -f prometheus-service.yaml
   ```

3. **Deploy Node Exporter:**

   ```bash
   # Deploy Node Exporter as DaemonSet
   kubectl apply -f node-exporter-daemonset.yaml
   kubectl apply -f node-exporter-service.yaml
   ```

4. **Deploy Grafana:**

   ```bash
   # Deploy Grafana
   kubectl apply -f grafana-deployment.yaml
   kubectl apply -f grafana-service.yaml
   ```

5. **Deploy Sample App:**

   ```bash
   # Deploy sample application
   kubectl apply -f sample-app.yaml
   kubectl apply -f nginx-config.yaml
   ```

6. **Access the Services:**

   ```bash
   # Get service URLs
   kubectl get svc

   # Access Prometheus UI
   kubectl port-forward svc/prometheus 9090:9090

   # Access Grafana UI
   kubectl port-forward svc/grafana 3000:3000
   ```

7. **Configure Grafana:**

   - Login to Grafana (default credentials: admin/admin123)
   - Add Prometheus data source:
     - URL: http://prometheus:9090
     - Access: Server (default)

8. **Import Dashboards:**

   ```bash
   # Node Exporter Dashboard ID: 1860
   # Navigate to:
   # Dashboards -> Import -> Enter 1860
   ```

9. **Basic Monitoring Metrics:**

   - CPU Usage
   - Memory Usage
   - Disk I/O
   - Network Traffic
   - HTTP Request Rate
   - Response Times
   - Error Rates

10. **Create Alert Rules:**

    ```yaml
    # In Grafana UI:
    # Alerting -> Notification channels
    # Add email/slack channels

    # Example Alert Rule:
    # CPU Usage > 80%
    # Memory Usage > 90%
    # HTTP 5xx Errors > 5
    ```
