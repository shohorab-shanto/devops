# 📊 Complete Guide to Prometheus & Grafana Monitoring
## All-in-One Resource: From Zero to Production

> **Master monitoring in a single document** - Concepts, Installation, Configuration, Visualization, and Troubleshooting

**Version:** 1.0 | **Last Updated:** October 2025 | **Level:** Beginner to Advanced

---

## 📖 Table of Contents

### Part 1: Foundations
1. [Introduction & Why Monitoring Matters](#1-introduction--why-monitoring-matters)
2. [Core Concepts Explained Simply](#2-core-concepts-explained-simply)
3. [Architecture & How It All Works](#3-architecture--how-it-all-works)

### Part 2: Installation & Setup
4. [Installing on Virtual Machines (Ubuntu/Linux)](#4-installing-on-virtual-machines)
5. [Installing on Kubernetes](#5-installing-on-kubernetes)
6. [Quick Start Examples](#6-quick-start-examples)

### Part 3: Collecting Metrics
7. [Understanding Metrics & Metric Types](#7-understanding-metrics--metric-types)
8. [Instrumenting Applications](#8-instrumenting-applications)
9. [Exporters & Data Collection](#9-exporters--data-collection)
10. [PromQL Query Language](#10-promql-query-language)

### Part 4: Configuration
11. [VM Configuration (Alert Rules, AlertManager)](#11-vm-configuration)
12. [Kubernetes Configuration (ServiceMonitor, PodMonitor)](#12-kubernetes-configuration)
13. [Alert Rules & Recording Rules](#13-alert-rules--recording-rules)
14. [AlertManager Configuration](#14-alertmanager-configuration)

### Part 5: Visualization
15. [Grafana Dashboards](#15-grafana-dashboards)
16. [Dashboard Design Best Practices](#16-dashboard-design-best-practices)
17. [Pre-built Dashboards](#17-pre-built-dashboards)

### Part 6: Real-World Examples
18. [Complete Application Monitoring](#18-complete-application-monitoring)
19. [Database Monitoring](#19-database-monitoring)
20. [Kubernetes Cluster Monitoring](#20-kubernetes-cluster-monitoring)

### Part 7: Production & Operations
21. [Troubleshooting Guide](#21-troubleshooting-guide)
22. [Performance Tuning](#22-performance-tuning)
23. [Security Best Practices](#23-security-best-practices)
24. [Backup & High Availability](#24-backup--high-availability)

### Part 8: Quick Reference
25. [Command Cheat Sheet](#25-command-cheat-sheet)
26. [PromQL Query Reference](#26-promql-query-reference)
27. [Common Configuration Templates](#27-common-configuration-templates)
28. [Emergency Procedures](#28-emergency-procedures)

---

# Part 1: Foundations

## 1. Introduction & Why Monitoring Matters

### What is Monitoring? 🔍

**Simple Explanation:** Monitoring is like having a smart fitness tracker for your applications and servers. Just as a fitness tracker monitors your heart rate, steps, and sleep to keep you healthy, Prometheus monitors your application's CPU, memory, response times, and error rates to keep it running smoothly.

**Why You Need It:**

1. **Detect Problems Early** 🚨
   - Know about issues before your users complain
   - Example: High memory usage warning before server crashes

2. **Understand Performance** 📈
   - See how fast your application responds
   - Identify bottlenecks and slow endpoints

3. **Plan for Growth** 📊
   - Track usage trends over time
   - Know when to scale up resources

4. **Meet SLAs** ✅
   - Prove your uptime and performance
   - Generate reports for stakeholders

### The Dynamic Duo: Prometheus & Grafana

**Prometheus** = The Data Collector (The Detective 🕵️)
- Actively collects metrics from applications
- Stores time-series data efficiently
- Evaluates rules and triggers alerts
- Pull-based architecture (it asks for data)

**Grafana** = The Data Visualizer (The Artist 🎨)
- Creates beautiful, interactive dashboards
- Queries Prometheus for data
- Makes complex data understandable at a glance
- Customizable visualizations

**Together They Are:**
```mermaid
graph LR
    App[Your Application] -->|exposes metrics| Prom[Prometheus<br/>Collects & Stores]
    Prom -->|provides data| Graf[Grafana<br/>Visualizes]
    Graf -->|shows insights| User[You!<br/>Take Action]
    Prom -->|fires alerts| Alert[AlertManager<br/>Notifies]
    Alert -->|notify| User
    
    style Prom fill:#ff6b6b,color:#fff
    style Graf fill:#4dabf7,color:#fff
    style Alert fill:#ffd43b,color:#000
```

**Real-World Analogy:**
- **Prometheus** = Security camera recording everything
- **Grafana** = Monitoring room with screens showing live footage
- **AlertManager** = Security guard who calls you when something's wrong

---

## 2. Core Concepts Explained Simply

### Key Terminology

| Term | Simple Explanation | Real-World Analogy |
|------|-------------------|-------------------|
| **Metrics** | Numerical measurements (CPU, memory, requests) | Your car's speedometer showing 65 mph |
| **Time Series** | Metrics collected over time with timestamps | Your electricity bill showing daily usage |
| **Scraping** | Prometheus collecting metrics from targets | Mail carrier collecting letters from mailboxes |
| **Target** | Application/server that exposes metrics | House with an address that mail carriers visit |
| **Exporter** | Translates metrics to Prometheus format | Translator converting languages |
| **Labels** | Key-value pairs identifying metrics | Tags on photos (location, date, people) |
| **PromQL** | Query language for Prometheus | Like SQL for databases, but for metrics |
| **Endpoint** | URL where metrics are exposed | `/metrics` - like a vending machine slot |
| **Scrape Interval** | How often Prometheus collects metrics | Every 15 seconds (default) |
| **Retention** | How long data is kept | 15 days (default) |

### Understanding Metrics with Examples

**Counter** (Always goes up):
```
Website Visits:
Monday: 100 → Tuesday: 250 → Wednesday: 380
(Never decreases, only increases)
```

**Gauge** (Goes up and down):
```
Server Temperature:
10am: 45°C → 2pm: 72°C → 8pm: 51°C
(Changes based on current state)
```

**Histogram** (Distribution):
```
Response Times:
Fast (<100ms): 80 requests
Medium (100-500ms): 15 requests  
Slow (>500ms): 5 requests
(Groups values into buckets)
```

---

## 3. Architecture & How It All Works

### The Big Picture

```mermaid
graph TB
    subgraph "Your Applications & Services"
        App1[Application 1<br/>Port: 8080/metrics]
        App2[Application 2<br/>Port: 9090/metrics]
        Node[Node Exporter<br/>Port: 9100/metrics]
    end
    
    subgraph "Prometheus Ecosystem"
        Prom[Prometheus Server<br/>Scrapes & Stores Metrics]
        AM[AlertManager<br/>Handles Alerts]
        PG[Pushgateway<br/>For Short-lived Jobs]
    end
    
    subgraph "Visualization & Queries"
        Grafana[Grafana<br/>Dashboards & Graphs]
        API[Prometheus API<br/>PromQL Queries]
    end
    
    subgraph "Service Discovery"
        SD[Service Discovery<br/>Kubernetes/Consul/DNS]
    end
    
    App1 -->|HTTP GET /metrics| Prom
    App2 -->|HTTP GET /metrics| Prom
    Node -->|System Metrics| Prom
    PG -->|Push Metrics| Prom
    SD -->|Discover Targets| Prom
    Prom -->|Fire Alerts| AM
    AM -->|Email/Slack/PagerDuty| Users[Users]
    Prom -->|Query Data| Grafana
    Prom -->|PromQL| API
    Grafana -->|Display| Dashboard[📊 Dashboards]
    
    style Prom fill:#ff6b6b,stroke:#c92a2a,color:#fff
    style Grafana fill:#4dabf7,stroke:#1971c2,color:#fff
    style AM fill:#ffd43b,stroke:#f08c00,color:#000
```

### How It All Works: The Complete Story

**Step 1: Applications Expose Metrics** 📤
```
Your app runs a web server on /metrics endpoint:
http://myapp:8080/metrics

Returns:
http_requests_total{method="GET",path="/api/users"} 1523
cpu_usage_percent 45.2
```

**Step 2: Prometheus Discovers Targets** 🔍
```
Prometheus checks its configuration:
- Static targets (manually configured)
- Service discovery (automatic from Kubernetes, Consul, etc.)
```

**Step 3: Prometheus Scrapes Metrics** 📊
```
Every 15 seconds (default):
1. Prometheus sends HTTP GET request
2. Target responds with current metrics
3. Prometheus adds timestamp and stores data
```

**Step 4: Data Storage in TSDB** 💾
```
Time-Series Database stores:
Timestamp | Metric Name | Value | Labels
10:00:00  | cpu_usage   | 45.2  | {instance="server1"}
10:00:15  | cpu_usage   | 47.1  | {instance="server1"}
10:00:30  | cpu_usage   | 46.8  | {instance="server1"}
```

**Step 5: Rules Evaluation** ⚠️
```
Prometheus checks alert rules:
IF cpu_usage > 80 FOR 5 minutes
THEN send alert to AlertManager
```

**Step 6: AlertManager Routes Notifications** 📧
```
AlertManager receives alert:
1. Groups similar alerts together
2. Checks routing rules
3. Sends to Slack/Email/PagerDuty
4. Handles silences and inhibitions
```

**Step 7: Grafana Visualizes** 🎨
```
1. User opens Grafana dashboard
2. Grafana queries Prometheus with PromQL
3. Prometheus returns time-series data
4. Grafana renders beautiful graphs
```

### Complete Data Flow

```mermaid
sequenceDiagram
    participant App as Application
    participant Prom as Prometheus
    participant TSDB as Time-Series DB
    participant Rules as Rules Engine
    participant AM as AlertManager
    participant Graf as Grafana
    participant User as User
    
    Note over App: 1. Expose /metrics
    App->>App: Start metrics endpoint
    
    loop Every 15 seconds
        Note over Prom: 2. Scrape metrics
        Prom->>App: GET /metrics
        App-->>Prom: Return metrics data
        Note over Prom,TSDB: 3. Store in TSDB
        Prom->>TSDB: Write time-series data
    end
    
    loop Every 15 seconds
        Note over Rules: 4. Evaluate rules
        TSDB->>Rules: Query current values
        Rules->>Rules: Check alert conditions
        Rules-->>AM: Send alerts if triggered
    end
    
    Note over Graf: 5. User opens dashboard
    User->>Graf: View dashboard
    Graf->>Prom: PromQL Query
    Prom->>TSDB: Fetch data
    TSDB-->>Prom: Time-series data
    Prom-->>Graf: Query results
    Graf-->>User: Display graphs
```

### Prometheus Server Internals

```mermaid
graph TB
    subgraph PS["PROMETHEUS SERVER"]
        SD[Service Discovery<br/>Find targets]
        R[Retrieval<br/>Scraper]
        TSDB[(Time Series<br/>Database<br/>TSDB)]
        HTTP[HTTP Server<br/>PromQL API]
        RE[Rules<br/>Evaluation]
        
        SD --> R
        R --> TSDB
        TSDB --> HTTP
        TSDB --> RE
    end
    
    Targets[Targets<br/>Applications] -.->|scrape| R
    HTTP --> Users[Users/Grafana]
    RE --> AM[AlertManager]
    
    style TSDB fill:#ff6b6b,color:#fff
    style R fill:#51cf66,color:#fff
    style HTTP fill:#4dabf7,color:#fff
    style RE fill:#ffd43b,color:#000
    style SD fill:#845ef7,color:#fff
```

### Pull vs Push Model

**Pull Model (Prometheus Default):**
```mermaid
sequenceDiagram
    participant P as Prometheus
    participant T as Target
    
    loop Every 15s
        P->>T: HTTP GET /metrics
        T-->>P: Current metrics
        Note over P: Stores with timestamp
    end
```

**Benefits:**
- ✅ Target health detection (scrape failure = target down)
- ✅ Centralized configuration
- ✅ Easy debugging (can manually curl /metrics)
- ✅ No need for targets to know about Prometheus

**Push Model (via Pushgateway):**
```mermaid
sequenceDiagram
    participant J as Batch Job
    participant PG as Pushgateway
    participant P as Prometheus
    
    J->>PG: POST /metrics/job/batch_job
    Note over J: Job completes and exits
    
    loop Every 15s
        P->>PG: GET /metrics
        PG-->>P: Stored metrics
    end
```

**Use Cases:**
- Short-lived jobs (cron jobs, batch processing)
- Behind firewalls (can't be scraped)
- Network constraints

---

# Part 2: Installation & Setup

## 4. Installing on Virtual Machines

### Prerequisites

**System Requirements:**
- Ubuntu 20.04/22.04 or similar Linux distribution
- 2GB RAM minimum (4GB recommended)
- 2 CPU cores minimum
- 10GB free disk space
- Root or sudo access

**Network Requirements:**
- Port 9090: Prometheus web UI
- Port 3000: Grafana web UI
- Port 9100: Node Exporter
- Port 9093: AlertManager

### 4.1 Installing Prometheus

#### Step 1: Create User and Directories

```bash
# Create prometheus user (no login shell for security)
sudo useradd --no-create-home --shell /bin/false prometheus

# Create directories
sudo mkdir /etc/prometheus          # Configuration files
sudo mkdir /var/lib/prometheus      # Data storage

# Set ownership
sudo chown prometheus:prometheus /etc/prometheus
sudo chown prometheus:prometheus /var/lib/prometheus
```

#### Step 2: Download and Install

```bash
# Navigate to tmp directory
cd /tmp

# Download Prometheus (check https://prometheus.io/download/ for latest)
wget https://github.com/prometheus/prometheus/releases/download/v2.53.0/prometheus-2.53.0.linux-amd64.tar.gz

# Extract archive
tar -xvf prometheus-2.53.0.linux-amd64.tar.gz
cd prometheus-2.53.0.linux-amd64

# Copy binaries to system path
sudo cp prometheus /usr/local/bin/
sudo cp promtool /usr/local/bin/

# Copy console files
sudo cp -r consoles /etc/prometheus
sudo cp -r console_libraries /etc/prometheus

# Set ownership
sudo chown -R prometheus:prometheus /etc/prometheus/consoles
sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool

# Verify installation
prometheus --version
promtool --version
```

#### Step 3: Create Configuration File

```bash
sudo nano /etc/prometheus/prometheus.yml
```

```yaml
# Global configuration
global:
  scrape_interval: 15s              # How often to scrape targets
  evaluation_interval: 15s           # How often to evaluate rules
  
  # External labels (added to all metrics)
  external_labels:
    monitor: 'my-prometheus'
    environment: 'production'
    datacenter: 'dc1'

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - 'localhost:9093'  # Uncomment after installing AlertManager

# Load alert rules
rule_files:
  # - "rules/*.yml"           # Uncomment when you add rules

# Scrape configurations - define what to monitor
scrape_configs:
  # Monitor Prometheus itself
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
        labels:
          group: 'monitoring'
          type: 'prometheus'
  
  # Monitor system metrics via Node Exporter
  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']
        labels:
          group: 'servers'
          type: 'linux'
```

```bash
# Set ownership
sudo chown prometheus:prometheus /etc/prometheus/prometheus.yml

# Validate configuration
promtool check config /etc/prometheus/prometheus.yml
```

#### Step 4: Create Systemd Service

```bash
sudo nano /etc/systemd/system/prometheus.service
```

```ini
[Unit]
Description=Prometheus Time Series Database
Documentation=https://prometheus.io/docs/introduction/overview/
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=prometheus
Group=prometheus

# Prometheus binary and configuration
ExecStart=/usr/local/bin/prometheus \
    --config.file=/etc/prometheus/prometheus.yml \
    --storage.tsdb.path=/var/lib/prometheus/ \
    --web.console.templates=/etc/prometheus/consoles \
    --web.console.libraries=/etc/prometheus/console_libraries \
    --web.listen-address=0.0.0.0:9090 \
    --web.enable-lifecycle \
    --storage.tsdb.retention.time=15d \
    --storage.tsdb.retention.size=50GB

# Restart policy
Restart=always
RestartSec=10

# Security settings
NoNewPrivileges=true
ProtectSystem=full
ProtectHome=true

[Install]
WantedBy=multi-user.target
```

**Configuration Flags Explained:**
- `--config.file`: Path to configuration file
- `--storage.tsdb.path`: Where to store data
- `--web.listen-address`: IP and port to bind
- `--web.enable-lifecycle`: Allow config reload via API
- `--storage.tsdb.retention.time`: Keep data for 15 days
- `--storage.tsdb.retention.size`: Max 50GB storage

#### Step 5: Start Prometheus

```bash
# Reload systemd to recognize new service
sudo systemctl daemon-reload

# Start Prometheus
sudo systemctl start prometheus

# Enable to start on boot
sudo systemctl enable prometheus

# Check status
sudo systemctl status prometheus

# View logs (press Ctrl+C to exit)
sudo journalctl -u prometheus -f

# Check if Prometheus is responding
curl http://localhost:9090/metrics
```

#### Step 6: Access Prometheus UI

**Open your browser:**
```
http://YOUR_SERVER_IP:9090
```

**Things to check:**
1. **Status → Targets**: Should show Prometheus itself as UP
2. **Graph**: Try query `up` - should return 1
3. **Status → Configuration**: Shows loaded config
4. **Status → Runtime & Build Info**: Shows version

### 4.2 Installing Node Exporter

Node Exporter collects hardware and OS metrics (CPU, memory, disk, network).

#### Step 1: Create User

```bash
sudo useradd --no-create-home --shell /bin/false node_exporter
```

#### Step 2: Download and Install

```bash
cd /tmp

# Download Node Exporter
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.0/node_exporter-1.8.0.linux-amd64.tar.gz

# Extract
tar -xvf node_exporter-1.8.0.linux-amd64.tar.gz

# Copy binary
sudo cp node_exporter-1.8.0.linux-amd64/node_exporter /usr/local/bin/
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter

# Verify
node_exporter --version
```

#### Step 3: Create Systemd Service

```bash
sudo nano /etc/systemd/system/node_exporter.service
```

```ini
[Unit]
Description=Node Exporter
Documentation=https://github.com/prometheus/node_exporter
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=node_exporter
Group=node_exporter

ExecStart=/usr/local/bin/node_exporter \
    --collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/) \
    --collector.netclass.ignored-devices=^(veth.*|docker.*|br-.*)$

Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

#### Step 4: Start Node Exporter

```bash
sudo systemctl daemon-reload
sudo systemctl start node_exporter
sudo systemctl enable node_exporter
sudo systemctl status node_exporter

# Verify metrics are being exported
curl http://localhost:9100/metrics | head -20
```

**You should see metrics like:**
```
node_cpu_seconds_total{cpu="0",mode="idle"} 1234.56
node_memory_MemTotal_bytes 8589934592
node_filesystem_size_bytes{device="/dev/sda1"} 53687091200
```

#### Step 5: Update Prometheus to Scrape Node Exporter

The Node Exporter target should already be in your prometheus.yml. Reload Prometheus:

```bash
# Reload configuration (thanks to --web.enable-lifecycle flag)
curl -X POST http://localhost:9090/-/reload

# Or restart service
sudo systemctl restart prometheus
```

**Verify in Prometheus UI:**
1. Go to Status → Targets
2. Look for `node_exporter` job
3. Should show state: UP

### 4.3 Installing Grafana

#### Step 1: Add Grafana Repository

```bash
# Install prerequisites
sudo apt-get install -y software-properties-common apt-transport-https

# Add Grafana GPG key
sudo wget -q -O /usr/share/keyrings/grafana.key https://apt.grafana.com/gpg.key

# Add repository
echo "deb [signed-by=/usr/share/keyrings/grafana.key] https://apt.grafana.com stable main" | \
sudo tee -a /etc/apt/sources.list.d/grafana.list
```

#### Step 2: Install Grafana

```bash
# Update package list
sudo apt-get update

# Install Grafana
sudo apt-get install -y grafana

# Verify installation
grafana-server -v
```

#### Step 3: Configure Grafana (Optional)

```bash
sudo nano /etc/grafana/grafana.ini
```

**Key settings to review:**
```ini
[server]
http_port = 3000
domain = localhost

[security]
admin_user = admin
admin_password = admin  # Change this!

[auth]
disable_login_form = false

[users]
allow_sign_up = false   # Disable self-registration
```

#### Step 4: Start Grafana

```bash
# Start Grafana
sudo systemctl start grafana-server

# Enable on boot
sudo systemctl enable grafana-server

# Check status
sudo systemctl status grafana-server

# View logs
sudo tail -f /var/log/grafana/grafana.log
```

#### Step 5: Access Grafana UI

**Open browser:**
```
http://YOUR_SERVER_IP:3000
```

**Default credentials:**
- Username: `admin`
- Password: `admin`

**First login:**
1. You'll be prompted to change password
2. Set a strong password
3. You're now on Grafana home page!

#### Step 6: Add Prometheus as Data Source

1. **Click** Configuration (⚙️ icon) → **Data Sources**
2. **Click** "Add data source"
3. **Select** "Prometheus"
4. **Configure:**
   ```
   Name: Prometheus
   URL: http://localhost:9090
   Access: Server (default)
   ```
5. **Click** "Save & Test"
6. Should see: ✅ "Data source is working"

**Test with a query:**
1. Go to Explore (compass icon)
2. Enter query: `up`
3. Click "Run query"
4. Should see metrics from Prometheus and Node Exporter

### 4.4 Quick Verification Checklist

```bash
# Check all services are running
sudo systemctl status prometheus
sudo systemctl status node_exporter
sudo systemctl status grafana-server

# Check ports are listening
sudo netstat -tlnp | grep -E ':(9090|9100|3000)'

# Expected output:
# tcp6  0  0 :::9090   :::*  LISTEN  <pid>/prometheus
# tcp6  0  0 :::9100   :::*  LISTEN  <pid>/node_exporter
# tcp6  0  0 :::3000   :::*  LISTEN  <pid>/grafana-server

# Test endpoints
curl -s http://localhost:9090/api/v1/targets | jq '.data.activeTargets[].health'
# Should show: "up" for each target

curl -s http://localhost:3000/api/health
# Should return: {"commit":"...","database":"ok","version":"..."}
```

---

## 5. Installing on Kubernetes

### Prerequisites

**Required:**
- Kubernetes cluster (v1.21+)
  - Local: minikube, kind, k3s
  - Cloud: EKS, GKE, AKS
- kubectl installed and configured
- Helm 3.x installed

**Minimum Resources:**
- 4 CPU cores
- 8GB RAM
- 20GB storage

### 5.1 Setting Up with Helm (Recommended)

#### Step 1: Install Helm (if not already installed)

```bash
# Download Helm installation script
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# Verify installation
helm version
```

#### Step 2: Add Helm Repositories

```bash
# Add Prometheus community charts
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

# Add Grafana charts (optional, kube-prometheus-stack includes it)
helm repo add grafana https://grafana.github.io/helm-charts

# Update repositories
helm repo update

# Search for available charts
helm search repo prometheus
helm search repo grafana
```

#### Step 3: Install kube-prometheus-stack (All-in-One)

This installs: Prometheus, Grafana, AlertManager, Prometheus Operator, and more.

```bash
# Create monitoring namespace
kubectl create namespace monitoring

# Install with default values (quick start)
helm install prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring

# Or install with custom values
cat <<EOF > values.yaml
# Grafana configuration
grafana:
  adminPassword: "YourStrongPassword123!"
  ingress:
    enabled: false
  persistence:
    enabled: true
    size: 5Gi

# Prometheus configuration
prometheus:
  prometheusSpec:
    retention: 15d
    storageSpec:
      volumeClaimTemplate:
        spec:
          accessModes: ["ReadWriteOnce"]
          resources:
            requests:
              storage: 50Gi
    
    # Allow scraping from all namespaces
    serviceMonitorSelectorNilUsesHelmValues: false
    podMonitorSelectorNilUsesHelmValues: false
    
    # Resource limits
    resources:
      requests:
        cpu: 500m
        memory: 2Gi
      limits:
        cpu: 2
        memory: 4Gi

# AlertManager configuration
alertmanager:
  alertmanagerSpec:
    storage:
      volumeClaimTemplate:
        spec:
          accessModes: ["ReadWriteOnce"]
          resources:
            requests:
              storage: 5Gi

# Enable default rules
defaultRules:
  create: true
  rules:
    alertmanager: true
    etcd: true
    general: true
    k8s: true
    kubeApiserver: true
    kubePrometheusNodeRecording: true
    kubernetesResources: true
    kubernetesStorage: true
    kubernetesSystem: true
    kubeScheduler: true
    node: true
    prometheus: true
EOF

# Install with custom values
helm install prometheus prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --values values.yaml
```

#### Step 4: Verify Installation

```bash
# Check all pods are running
kubectl get pods -n monitoring

# Expected output (may take 2-3 minutes):
# NAME                                                     READY   STATUS
# alertmanager-prometheus-kube-prometheus-alertmanager-0   2/2     Running
# prometheus-grafana-xxx                                   3/3     Running
# prometheus-kube-prometheus-operator-xxx                  1/1     Running
# prometheus-kube-state-metrics-xxx                        1/1     Running
# prometheus-prometheus-kube-prometheus-prometheus-0       2/2     Running
# prometheus-prometheus-node-exporter-xxx                  1/1     Running

# Check services
kubectl get svc -n monitoring

# Check persistent volume claims
kubectl get pvc -n monitoring
```

#### Step 5: Access Services

**Option 1: Port Forwarding (Development)**

```bash
# Prometheus
kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-prometheus 9090:9090
# Access at: http://localhost:9090

# Grafana
kubectl port-forward -n monitoring svc/prometheus-grafana 3000:80
# Access at: http://localhost:3000
# Default credentials: admin / prom-operator (or your custom password)

# AlertManager
kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-alertmanager 9093:9093
# Access at: http://localhost:9093
```

**Option 2: NodePort (Testing)**

```bash
# Expose Grafana via NodePort
kubectl patch svc prometheus-grafana -n monitoring \
  -p '{"spec": {"type": "NodePort"}}'

# Get NodePort
kubectl get svc prometheus-grafana -n monitoring
# Note the port (e.g., 30080)

# Access at: http://NODE_IP:NODE_PORT
```

**Option 3: LoadBalancer (Cloud)**

```bash
# Change service type
kubectl patch svc prometheus-grafana -n monitoring \
  -p '{"spec": {"type": "LoadBalancer"}}'

# Get external IP
kubectl get svc prometheus-grafana -n monitoring -w
# Wait for EXTERNAL-IP

# Access at: http://EXTERNAL_IP
```

**Option 4: Ingress (Production)**

```yaml
# grafana-ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: grafana-ingress
  namespace: monitoring
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  tls:
  - hosts:
    - grafana.yourdomain.com
    secretName: grafana-tls
  rules:
  - host: grafana.yourdomain.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: prometheus-grafana
            port:
              number: 80
```

```bash
kubectl apply -f grafana-ingress.yaml
```

### 5.2 Kubernetes Architecture Overview

```mermaid
graph TB
    subgraph "Kubernetes Cluster"
        subgraph "Monitoring Namespace"
            PO[Prometheus Operator<br/>Manages Prometheus]
            P[Prometheus StatefulSet<br/>Scrapes & Stores]
            AM[AlertManager StatefulSet<br/>Handles Alerts]
            G[Grafana Deployment<br/>Visualizations]
            KSM[Kube State Metrics<br/>K8s Object Metrics]
        end
        
        subgraph "Every Node"
            NE[Node Exporter DaemonSet<br/>Node Metrics]
        end
        
        subgraph "Application Namespace"
            App[Your Application Pod]
            SM[ServiceMonitor CRD]
            PM[PodMonitor CRD]
        end
        
        subgraph "Kubernetes API"
            API[K8s API Server<br/>Service Discovery]
        end
    end
    
    PO -->|Creates/Updates| P
    PO -->|Watches| SM
    PO -->|Watches| PM
    SM -->|Configures Scraping| P
    PM -->|Configures Scraping| P
    P -->|Scrapes| App
    P -->|Scrapes| NE
    P -->|Scrapes| KSM
    P -->|Service Discovery| API
    P -->|Send Alerts| AM
    G -->|Query| P
    AM -->|Notify| External[Slack/Email/PagerDuty]
    
    style P fill:#ff6b6b,color:#fff
    style G fill:#4dabf7,color:#fff
    style PO fill:#51cf66,color:#fff
```

### 5.3 What Gets Installed?

**Prometheus Operator:**
- Manages Prometheus instances
- Watches for ServiceMonitor/PodMonitor CRDs
- Automatically updates Prometheus configuration

**Prometheus Server:**
- Deployed as StatefulSet (persistent storage)
- Scrapes metrics from discovered targets
- Stores data in TSDB
- Evaluates alert rules

**AlertManager:**
- Deployed as StatefulSet
- Handles alert routing and notifications
- Deduplicates and groups alerts

**Grafana:**
- Deployed as Deployment
- Pre-configured with Prometheus data source
- Includes default dashboards

**Node Exporter:**
- DaemonSet (runs on every node)
- Collects host-level metrics
- CPU, memory, disk, network

**Kube State Metrics:**
- Deployment
- Exposes Kubernetes object state
- Pods, deployments, nodes, etc.

**Default ServiceMonitors:**
- Monitors Kubernetes components
- API server, kubelet, controller-manager
- etcd, scheduler

### 5.4 Understanding Custom Resource Definitions (CRDs)

Prometheus Operator introduces several CRDs:

```bash
# List all Prometheus CRDs
kubectl get crd | grep monitoring.coreos.com

# Output:
# alertmanagerconfigs.monitoring.coreos.com
# alertmanagers.monitoring.coreos.com
# podmonitors.monitoring.coreos.com
# probes.monitoring.coreos.com
# prometheuses.monitoring.coreos.com
# prometheusrules.monitoring.coreos.com
# servicemonitors.monitoring.coreos.com
# thanosrulers.monitoring.coreos.com
```

**Key CRDs:**

| CRD | Purpose | Example Use |
|-----|---------|-------------|
| Prometheus | Define Prometheus instance | Set retention, storage, resources |
| ServiceMonitor | Monitor Services | Scrape app via Service |
| PodMonitor | Monitor Pods directly | Scrape DaemonSet pods |
| PrometheusRule | Alert & recording rules | CPU > 80% alert |
| AlertmanagerConfig | Alert routing | Route critical → PagerDuty |

### 5.5 Verification Commands

```bash
# Check Prometheus Operator logs
kubectl logs -n monitoring deployment/prometheus-kube-prometheus-operator -f

# Check Prometheus logs
kubectl logs -n monitoring prometheus-prometheus-kube-prometheus-prometheus-0 prometheus

# Check Grafana logs
kubectl logs -n monitoring deployment/prometheus-grafana

# Check if ServiceMonitors are discovered
kubectl exec -n monitoring prometheus-prometheus-kube-prometheus-prometheus-0 -c prometheus -- \
  promtool check config /etc/prometheus/config_out/prometheus.env.yaml

# View Prometheus configuration
kubectl exec -n monitoring prometheus-prometheus-kube-prometheus-prometheus-0 -c prometheus -- \
  cat /etc/prometheus/config_out/prometheus.env.yaml | less
```

---

## 6. Quick Start Examples

### 6.1 Test Metrics Collection

**Create a test application:**

```yaml
# test-app.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-app
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: test-app
  template:
    metadata:
      labels:
        app: test-app
    spec:
      containers:
      - name: app
        image: fabxc/instrumented_app
        ports:
        - name: web
          containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: test-app
  namespace: default
  labels:
    app: test-app
spec:
  selector:
    app: test-app
  ports:
  - name: web
    port: 8080
    targetPort: 8080
```

```bash
# Deploy test app
kubectl apply -f test-app.yaml

# Verify pod is running
kubectl get pods -l app=test-app

# Test metrics endpoint
kubectl port-forward svc/test-app 8080:8080
curl http://localhost:8080/metrics
```

**Create ServiceMonitor:**

```yaml
# test-servicemonitor.yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: test-app
  namespace: default
  labels:
    release: prometheus  # Must match Prometheus selector
spec:
  selector:
    matchLabels:
      app: test-app
  endpoints:
  - port: web
    interval: 15s
    path: /metrics
```

```bash
# Apply ServiceMonitor
kubectl apply -f test-servicemonitor.yaml

# Verify in Prometheus UI
kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-prometheus 9090:9090
# Go to http://localhost:9090/targets
# Look for "serviceMonitor/default/test-app"
```

### 6.2 First Dashboard in Grafana

**Login to Grafana:**
```bash
kubectl port-forward -n monitoring svc/prometheus-grafana 3000:80
# Open http://localhost:3000
# Login: admin / prom-operator
```

**Create dashboard:**
1. Click **+** → **Dashboard**
2. Click **Add new panel**
3. In query editor, enter:
   ```promql
   rate(http_requests_total{job="test-app"}[5m])
   ```
4. Set panel title: "Request Rate"
5. Click **Apply**
6. Click **Save dashboard** (💾 icon)
7. Name it: "My First Dashboard"
8. Click **Save**

### 6.3 First Alert Rule

**Create PrometheusRule:**

```yaml
# test-alert.yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: test-alerts
  namespace: monitoring
  labels:
    release: prometheus
spec:
  groups:
  - name: test
    interval: 30s
    rules:
    - alert: TestAppDown
      expr: up{job="test-app"} == 0
      for: 1m
      labels:
        severity: warning
      annotations:
        summary: "Test app is down"
        description: "Test app has been down for more than 1 minute"
```

```bash
# Apply alert
kubectl apply -f test-alert.yaml

# Verify in Prometheus UI
# Go to http://localhost:9090/alerts
# Should see "TestAppDown" alert

# Test alert by scaling down app
kubectl scale deployment test-app --replicas=0
# Wait 1-2 minutes, alert should fire

# Scale back up
kubectl scale deployment test-app --replicas=1
# Alert should resolve
```

---

# Part 3: Collecting Metrics

## 7. Understanding Metrics & Metric Types

### 7.1 What Are Metrics?

**Metrics are numerical measurements** collected over time.

**Analogy:** Think of metrics like weather measurements:
- Temperature (gauge) - can go up or down
- Total rainfall this year (counter) - only increases
- Distribution of temperatures throughout the day (histogram)

### 7.2 The Four Metric Types

```mermaid
graph TB
    M[Prometheus Metrics]
    M --> Counter[Counter<br/>📈 Only Goes Up]
    M --> Gauge[Gauge<br/>📊 Goes Up & Down]
    M --> Histogram[Histogram<br/>📉 Distribution]
    M --> Summary[Summary<br/>📊 Quantiles]
    
    Counter --> CE["Examples:<br/>• HTTP requests<br/>• Errors count<br/>• Tasks completed<br/>• Bytes sent"]
    Gauge --> GE["Examples:<br/>• CPU usage<br/>• Memory usage<br/>• Active connections<br/>• Queue length"]
    Histogram --> HE["Examples:<br/>• Request duration<br/>• Response sizes<br/>• Processing time"]
    Summary --> SE["Examples:<br/>• Request latency<br/>• 95th percentile<br/>• Pre-calculated stats"]
    
    style Counter fill:#ff6b6b,color:#fff
    style Gauge fill:#4dabf7,color:#fff
    style Histogram fill:#51cf66,color:#fff
    style Summary fill:#ffd43b,color:#000
```

### 7.3 Counter Detailed

**Definition:** A counter is a cumulative metric that only increases (or resets to zero on restart).

**Visual Representation:**
```mermaid
graph LR
    subgraph "Counter Metric Behavior"
        direction LR
        T0["Time: 0<br/>Value: 0"] 
        T1["Time: 1<br/>Value: 5"]
        T2["Time: 2<br/>Value: 12"]
        T3["Time: 3<br/>Value: 18"]
        T4["Time: 4<br/>Value: 25"]
        
        T0 -->|+5| T1
        T1 -->|+7| T2
        T2 -->|+6| T3
        T3 -->|+7| T4
    end
    
    style T0 fill:#e7f5ff
    style T1 fill:#d0ebff
    style T2 fill:#a5d8ff
    style T3 fill:#74c0fc
    style T4 fill:#4dabf7,color:#fff
```

**Real Example:**
```
http_requests_total 1000  (at 10:00)
http_requests_total 1523  (at 10:15)  ← Increased by 523
http_requests_total 2100  (at 10:30)  ← Increased by 577
```

**Common Queries:**
```promql
# Rate per second
rate(http_requests_total[5m])

# Total increase over 1 hour
increase(http_requests_total[1h])

# Instant rate (less smoothing)
irate(http_requests_total[5m])
```

**When to Use:**
- Total HTTP requests
- Number of errors
- Bytes sent/received
- Tasks completed
- Database queries executed

### 7.4 Gauge Detailed

**Definition:** A gauge can go up and down, representing a current value.

**Visual Representation:**
```mermaid
graph LR
    subgraph "Gauge Metric Behavior"
        direction LR
        G0["Time: 0<br/>Value: 50"]
        G1["Time: 1<br/>Value: 75"]
        G2["Time: 2<br/>Value: 45"]
        G3["Time: 3<br/>Value: 80"]
        G4["Time: 4<br/>Value: 60"]
        
        G0 -->|+25 ⬆| G1
        G1 -->|-30 ⬇| G2
        G2 -->|+35 ⬆| G3
        G3 -->|-20 ⬇| G4
    end
    
    style G0 fill:#fff3e0
    style G1 fill:#51cf66,color:#fff
    style G2 fill:#ff6b6b,color:#fff
    style G3 fill:#51cf66,color:#fff
    style G4 fill:#ffd43b
```

**Real Example:**
```
cpu_usage_percent 45.2  (at 10:00)
cpu_usage_percent 67.8  (at 10:15)  ← Went up
cpu_usage_percent 52.1  (at 10:30)  ← Went down
```

**Common Queries:**
```promql
# Current value
cpu_usage_percent

# Average over time
avg_over_time(cpu_usage_percent[5m])

# Maximum in time range
max_over_time(cpu_usage_percent[1h])

# Predict value in 10 minutes
predict_linear(cpu_usage_percent[5m], 600)
```

**When to Use:**
- CPU/Memory usage
- Temperature
- Number of active connections
- Queue length
- Cache size
- Number of running pods

### 7.5 Histogram Detailed

**Definition:** Observes values and counts them in configurable buckets.

**Visual Representation:**
```mermaid
graph TB
    subgraph "Histogram Distribution"
        B1["Bucket: 0-0.1s<br/>Count: 100<br/>████████████"]
        B2["Bucket: 0.1-0.5s<br/>Count: 80<br/>█████████"]
        B3["Bucket: 0.5-1s<br/>Count: 50<br/>██████"]
        B4["Bucket: 1-5s<br/>Count: 20<br/>███"]
        B5["Bucket: 5-10s<br/>Count: 5<br/>█"]
    end
    
    note["Most requests complete<br/>within 0.1 seconds<br/><br/>Cumulative:<br/>le=0.1: 100<br/>le=0.5: 180<br/>le=1: 230<br/>le=5: 250<br/>le=10: 255"]
    
    B1 -.-> note
    
    style B1 fill:#51cf66,color:#fff
    style B2 fill:#51cf66,color:#fff
    style B3 fill:#ffd43b,color:#000
    style B4 fill:#ff8c42,color:#fff
    style B5 fill:#ff6b6b,color:#fff
    style note fill:#e7f5ff,stroke:#1971c2
```

**Real Example:**
```
http_request_duration_seconds_bucket{le="0.1"} 100
http_request_duration_seconds_bucket{le="0.5"} 180
http_request_duration_seconds_bucket{le="1.0"} 230
http_request_duration_seconds_bucket{le="5.0"} 250
http_request_duration_seconds_bucket{le="10"} 255
http_request_duration_seconds_bucket{le="+Inf"} 255
http_request_duration_seconds_sum 324.5
http_request_duration_seconds_count 255
```

**Common Queries:**
```promql
# 95th percentile (95% of requests faster than this)
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))

# 99th percentile
histogram_quantile(0.99, rate(http_request_duration_seconds_bucket[5m]))

# Average duration
rate(http_request_duration_seconds_sum[5m]) / 
rate(http_request_duration_seconds_count[5m])

# Requests per second
rate(http_request_duration_seconds_count[5m])
```

**When to Use:**
- Request/response durations
- Request/response sizes
- Query execution times
- Processing times

### 7.6 Summary Detailed

**Definition:** Similar to histogram but calculates quantiles on the client side.

**Differences from Histogram:**

| Feature | Histogram | Summary |
|---------|-----------|---------|
| Quantile calculation | Server-side (flexible) | Client-side (fixed) |
| Aggregation | Can aggregate | Cannot aggregate |
| Precision | Approximate | More accurate |
| Performance | Better | Higher client CPU |

**Common Queries:**
```promql
# Pre-calculated quantiles
http_request_duration_seconds{quantile="0.95"}
http_request_duration_seconds{quantile="0.99"}

# Average
rate(http_request_duration_seconds_sum[5m]) /
rate(http_request_duration_seconds_count[5m])
```

**When to Use:**
- When you know exactly which quantiles you need
- When you can't aggregate across instances
- Small number of instances

### 7.7 Choosing the Right Metric Type

**Decision Flow:**
```mermaid
graph TD
    Start{What are you<br/>measuring?}
    
    Start -->|Cumulative count| Counter
    Start -->|Current value| CheckChange{Does it<br/>go up & down?}
    Start -->|Distribution/Duration| CheckAgg{Need to<br/>aggregate?}
    
    CheckChange -->|Yes| Gauge
    CheckChange -->|No| Counter
    
    CheckAgg -->|Yes| Histogram
    CheckAgg -->|No| Summary
    
    Counter[Use Counter<br/>Examples: requests, errors]
    Gauge[Use Gauge<br/>Examples: CPU, memory]
    Histogram[Use Histogram<br/>Examples: latency, size]
    Summary[Use Summary<br/>Examples: pre-calc percentiles]
    
    style Counter fill:#ff6b6b,color:#fff
    style Gauge fill:#4dabf7,color:#fff
    style Histogram fill:#51cf66,color:#fff
    style Summary fill:#ffd43b,color:#000
```

---

## 8. Instrumenting Applications

### 8.1 Why Instrument Your App?

**Without Instrumentation:**
- You only see generic system metrics (CPU, memory)
- No visibility into application behavior
- Can't track business metrics

**With Instrumentation:**
- Track request rates and latencies
- Monitor error rates
- Count business events (orders, signups)
- Measure custom logic performance

### 8.2 Python Application Example

**Install Prometheus client:**
```bash
pip install prometheus-client
```

**Complete Flask application with metrics:**

```python
# app.py
from flask import Flask, request, jsonify
from prometheus_client import Counter, Histogram, Gauge, generate_latest, CONTENT_TYPE_LATEST
import time
import random

app = Flask(__name__)

# === Define Metrics ===

# Counter: Total requests
REQUEST_COUNT = Counter(
    'http_requests_total',
    'Total HTTP requests',
    ['method', 'endpoint', 'status']
)

# Histogram: Request duration
REQUEST_DURATION = Histogram(
    'http_request_duration_seconds',
    'HTTP request duration in seconds',
    ['method', 'endpoint'],
    buckets=[0.01, 0.05, 0.1, 0.5, 1.0, 2.0, 5.0, 10.0]
)

# Gauge: Active connections
ACTIVE_REQUESTS = Gauge(
    'http_requests_active',
    'Number of active HTTP requests'
)

# Gauge: Database connections
DB_CONNECTIONS = Gauge(
    'db_connections_active',
    'Number of active database connections'
)

# Counter: Errors
ERROR_COUNT = Counter(
    'app_errors_total',
    'Total application errors',
    ['error_type']
)

# === Middleware to Track All Requests ===

@app.before_request
def before_request():
    request.start_time = time.time()
    ACTIVE_REQUESTS.inc()

@app.after_request
def after_request(response):
    # Calculate request duration
    request_duration = time.time() - request.start_time
    
    # Record metrics
    REQUEST_COUNT.labels(
        method=request.method,
        endpoint=request.endpoint or 'unknown',
        status=response.status_code
    ).inc()
    
    REQUEST_DURATION.labels(
        method=request.method,
        endpoint=request.endpoint or 'unknown'
    ).observe(request_duration)
    
    ACTIVE_REQUESTS.dec()
    return response

# === Application Routes ===

@app.route('/')
def index():
    return jsonify({
        "message": "Welcome to Metrics Demo API",
        "endpoints": [
            "/api/users",
            "/api/products",
            "/api/slow",
            "/api/error",
            "/metrics"
        ]
    })

@app.route('/api/users')
def users():
    # Simulate database query
    time.sleep(random.uniform(0.01, 0.1))
    
    return jsonify({
        "users": [
            {"id": 1, "name": "Alice"},
            {"id": 2, "name": "Bob"},
            {"id": 3, "name": "Charlie"}
        ]
    })

@app.route('/api/products')
def products():
    # Simulate work
    time.sleep(random.uniform(0.05, 0.2))
    
    return jsonify({
        "products": [
            {"id": 1, "name": "Laptop", "price": 999.99},
            {"id": 2, "name": "Phone", "price": 699.99},
            {"id": 3, "name": "Tablet", "price": 499.99}
        ]
    })

@app.route('/api/slow')
def slow():
    # Intentionally slow endpoint
    time.sleep(random.uniform(1, 3))
    
    return jsonify({
        "message": "This is a slow endpoint"
    })

@app.route('/api/error')
def error_route():
    # 30% chance of error
    if random.random() < 0.3:
        ERROR_COUNT.labels(error_type='server_error').inc()
        return jsonify({"error": "Something went wrong"}), 500
    
    return jsonify({"status": "ok"})

# === Metrics Endpoint ===

@app.route('/metrics')
def metrics():
    # Simulate database connections (for demo)
    DB_CONNECTIONS.set(random.randint(5, 20))
    
    # Return Prometheus metrics
    return generate_latest(), 200, {'Content-Type': CONTENT_TYPE_LATEST}

# === Health Check ===

@app.route('/health')
def health():
    return jsonify({"status": "healthy"}), 200

if __name__ == '__main__':
    print("Starting Flask app on port 8080")
    print("Metrics available at: http://localhost:8080/metrics")
    app.run(host='0.0.0.0', port=8080, debug=False)
```

**Run the application:**
```bash
python app.py
```

**Test the metrics endpoint:**
```bash
# Generate some traffic
curl http://localhost:8080/api/users
curl http://localhost:8080/api/products
curl http://localhost:8080/api/slow
curl http://localhost:8080/api/error

# View metrics
curl http://localhost:8080/metrics
```

**Expected metrics output:**
```
# HELP http_requests_total Total HTTP requests
# TYPE http_requests_total counter
http_requests_total{endpoint="users",method="GET",status="200"} 5.0
http_requests_total{endpoint="products",method="GET",status="200"} 3.0

# HELP http_request_duration_seconds HTTP request duration in seconds
# TYPE http_request_duration_seconds histogram
http_request_duration_seconds_bucket{endpoint="users",le="0.01",method="GET"} 0.0
http_request_duration_seconds_bucket{endpoint="users",le="0.05",method="GET"} 2.0
http_request_duration_seconds_bucket{endpoint="users",le="0.1",method="GET"} 5.0
http_request_duration_seconds_sum{endpoint="users",method="GET"} 0.325
http_request_duration_seconds_count{endpoint="users",method="GET"} 5.0

# HELP http_requests_active Number of active HTTP requests
# TYPE http_requests_active gauge
http_requests_active 1.0

# HELP db_connections_active Number of active database connections
# TYPE db_connections_active gauge
db_connections_active 12.0
```

### 8.3 Node.js Application Example

**Install Prometheus client:**
```bash
npm install prom-client express
```

**Complete Express application:**

```javascript
// app.js
const express = require('express');
const promClient = require('prom-client');

const app = express();
const port = 8080;

// Create a Registry
const register = new promClient.Registry();

// Add default metrics (CPU, memory, etc.)
promClient.collectDefaultMetrics({ register });

// === Define Custom Metrics ===

// Counter: Total requests
const httpRequestCounter = new promClient.Counter({
  name: 'http_requests_total',
  help: 'Total number of HTTP requests',
  labelNames: ['method', 'endpoint', 'status'],
  registers: [register]
});

// Histogram: Request duration
const httpRequestDuration = new promClient.Histogram({
  name: 'http_request_duration_seconds',
  help: 'Duration of HTTP requests in seconds',
  labelNames: ['method', 'endpoint'],
  buckets: [0.01, 0.05, 0.1, 0.5, 1.0, 2.0, 5.0, 10.0],
  registers: [register]
});

// Gauge: Active connections
const activeConnections = new promClient.Gauge({
  name: 'http_requests_active',
  help: 'Number of active HTTP requests',
  registers: [register]
});

// Counter: Errors
const errorCounter = new promClient.Counter({
  name: 'app_errors_total',
  help: 'Total application errors',
  labelNames: ['error_type'],
  registers: [register]
});

// === Middleware ===

app.use((req, res, next) => {
  const start = Date.now();
  activeConnections.inc();
  
  res.on('finish', () => {
    const duration = (Date.now() - start) / 1000;
    
    httpRequestCounter.labels(
      req.method,
      req.route ? req.route.path : 'unknown',
      res.statusCode.toString()
    ).inc();
    
    httpRequestDuration.labels(
      req.method,
      req.route ? req.route.path : 'unknown'
    ).observe(duration);
    
    activeConnections.dec();
  });
  
  next();
});

// === Routes ===

app.get('/', (req, res) => {
  res.json({
    message: 'Welcome to Metrics Demo API',
    endpoints: [
      '/api/users',
      '/api/products',
      '/api/slow',
      '/api/error',
      '/metrics'
    ]
  });
});

app.get('/api/users', (req, res) => {
  // Simulate database query
  setTimeout(() => {
    res.json({
      users: [
        { id: 1, name: 'Alice' },
        { id: 2, name: 'Bob' },
        { id: 3, name: 'Charlie' }
      ]
    });
  }, Math.random() * 100);
});

app.get('/api/products', (req, res) => {
  setTimeout(() => {
    res.json({
      products: [
        { id: 1, name: 'Laptop', price: 999.99 },
        { id: 2, name: 'Phone', price: 699.99 },
        { id: 3, name: 'Tablet', price: 499.99 }
      ]
    });
  }, Math.random() * 200);
});

app.get('/api/slow', (req, res) => {
  // Intentionally slow
  setTimeout(() => {
    res.json({ message: 'This is a slow endpoint' });
  }, 1000 + Math.random() * 2000);
});

app.get('/api/error', (req, res) => {
  // 30% chance of error
  if (Math.random() < 0.3) {
    errorCounter.labels('server_error').inc();
    res.status(500).json({ error: 'Something went wrong' });
  } else {
    res.json({ status: 'ok' });
  }
});

// === Metrics Endpoint ===

app.get('/metrics', async (req, res) => {
  res.set('Content-Type', register.contentType);
  res.end(await register.metrics());
});

// === Health Check ===

app.get('/health', (req, res) => {
  res.json({ status: 'healthy' });
});

// === Start Server ===

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
  console.log(`Metrics available at: http://localhost:${port}/metrics`);
});
```

**Run the application:**
```bash
node app.js
```

### 8.4 Deploying to Kubernetes

**Dockerfile:**
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY app.py requirements.txt ./
RUN pip install -r requirements.txt
EXPOSE 8080
CMD ["python", "app.py"]
```

**requirements.txt:**
```
flask==2.3.0
prometheus-client==0.17.0
```

**Build and push:**
```bash
docker build -t your-registry/flask-metrics-demo:v1 .
docker push your-registry/flask-metrics-demo:v1
```

**Kubernetes deployment:**
```yaml
# flask-app-complete.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: flask-app
  namespace: default
  labels:
    app: flask-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: flask-app
  template:
    metadata:
      labels:
        app: flask-app
        version: v1
    spec:
      containers:
      - name: flask-app
        image: your-registry/flask-metrics-demo:v1
        ports:
        - name: http
          containerPort: 8080
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "200m"
---
apiVersion: v1
kind: Service
metadata:
  name: flask-app
  namespace: default
  labels:
    app: flask-app
spec:
  selector:
    app: flask-app
  ports:
  - name: http
    port: 80
    targetPort: 8080
  type: ClusterIP
---
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: flask-app
  namespace: default
  labels:
    release: prometheus
spec:
  selector:
    matchLabels:
      app: flask-app
  endpoints:
  - port: http
    path: /metrics
    interval: 15s
```

```bash
kubectl apply -f flask-app-complete.yaml
```

---

## 9. Exporters & Data Collection

### 9.1 What Are Exporters?

**Exporters are translators** that convert metrics from third-party systems into Prometheus format.

**Analogy:** Think of exporters as tour guides who translate for foreign visitors - they take information in one language (system metrics) and present it in another (Prometheus format).

### 9.2 Official Exporters

**Node Exporter** (Hardware & OS metrics):
- CPU, memory, disk, network
- Already installed in our setup!

**Blackbox Exporter** (Probing):
- HTTP/HTTPS endpoints
- TCP connections
- ICMP pings
- DNS queries

**MySQL Exporter**:
- Database metrics
- Query performance
- Replication status

**PostgreSQL Exporter**:
- Connection pools
- Query stats
- Database size

**Redis Exporter**:
- Memory usage
- Command stats
- Keyspace info

### 9.3 Installing PostgreSQL Exporter Example

**On VM:**
```bash
cd /tmp
wget https://github.com/prometheus-community/postgres_exporter/releases/download/v0.15.0/postgres_exporter-0.15.0.linux-amd64.tar.gz
tar -xvf postgres_exporter-0.15.0.linux-amd64.tar.gz
sudo cp postgres_exporter-0.15.0.linux-amd64/postgres_exporter /usr/local/bin/

# Create environment file with connection string
sudo nano /etc/postgres_exporter.env
```

```bash
# /etc/postgres_exporter.env
DATA_SOURCE_NAME="postgresql://postgres:password@localhost:5432/postgres?sslmode=disable"
```

**Systemd service:**
```bash
sudo nano /etc/systemd/system/postgres_exporter.service
```

```ini
[Unit]
Description=PostgreSQL Exporter
After=network.target

[Service]
Type=simple
User=prometheus
EnvironmentFile=/etc/postgres_exporter.env
ExecStart=/usr/local/bin/postgres_exporter
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl start postgres_exporter
sudo systemctl enable postgres_exporter

# Test
curl http://localhost:9187/metrics | grep pg_
```

**On Kubernetes:**
```yaml
# postgres-exporter.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres-exporter
  namespace: database
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres-exporter
  template:
    metadata:
      labels:
        app: postgres-exporter
    spec:
      containers:
      - name: exporter
        image: prometheuscommunity/postgres-exporter:latest
        ports:
        - name: metrics
          containerPort: 9187
        env:
        - name: DATA_SOURCE_NAME
          valueFrom:
            secretKeyRef:
              name: postgres-credentials
              key: connection-string
---
apiVersion: v1
kind: Service
metadata:
  name: postgres-exporter
  namespace: database
  labels:
    app: postgres-exporter
spec:
  selector:
    app: postgres-exporter
  ports:
  - name: metrics
    port: 9187
---
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: postgres-exporter
  namespace: database
  labels:
    release: prometheus
spec:
  selector:
    matchLabels:
      app: postgres-exporter
  endpoints:
  - port: metrics
    interval: 30s
```

---

## 10. PromQL Query Language

### 10.1 PromQL Basics

PromQL is the query language for Prometheus. Think of it like SQL for time-series data.

### 10.2 Basic Queries

**Instant Vector (single value at current time):**
```promql
# Get current CPU usage
node_cpu_seconds_total

# Filter by label
node_cpu_seconds_total{mode="idle"}

# Filter by multiple labels
node_cpu_seconds_total{mode="idle",cpu="0"}
```

**Range Vector (values over time):**
```promql
# Last 5 minutes of data
node_cpu_seconds_total[5m]

# Last 1 hour
node_cpu_seconds_total[1h]
```

### 10.3 Label Matching

**Operators:**
```promql
# Equals
{job="prometheus"}

# Not equals
{job!="prometheus"}

# Regex match
{job=~"prometheus|node.*"}

# Regex not match
{job!~"test.*"}
```

### 10.4 Essential Functions

**rate() - Calculate per-second rate:**
```promql
# Requests per second
rate(http_requests_total[5m])

# CPU usage percentage
100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
```

**increase() - Total increase:**
```promql
# Total requests in last hour
increase(http_requests_total[1h])
```

**sum() - Add values together:**
```promql
# Total requests across all instances
sum(rate(http_requests_total[5m]))

# Sum by label
sum by(endpoint) (rate(http_requests_total[5m]))
```

**avg() - Average:**
```promql
# Average memory usage
avg(node_memory_MemAvailable_bytes)

# Average by instance
avg by(instance) (node_memory_MemAvailable_bytes)
```

**histogram_quantile() - Calculate percentiles:**
```promql
# 95th percentile latency
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))

# 99th percentile
histogram_quantile(0.99, rate(http_request_duration_seconds_bucket[5m]))
```

### 10.5 Useful Query Patterns

**Error Rate:**
```promql
rate(http_requests_total{status=~"5.."}[5m]) / rate(http_requests_total[5m])
```

**Memory Usage Percentage:**
```promql
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100
```

**Disk Usage Percentage:**
```promql
100 - ((node_filesystem_avail_bytes * 100) / node_filesystem_size_bytes)
```

**Top 5 Slowest Endpoints:**
```promql
topk(5, histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])))
```

**Predict Disk Full Time:**
```promql
predict_linear(node_filesystem_free_bytes[1h], 4 * 3600) < 0
```

### 10.6 PromQL Cheat Sheet

| Operation | Example |
|-----------|---------|
| Current value | `up` |
| Filter by label | `up{job="prometheus"}` |
| Rate (per second) | `rate(metric[5m])` |
| Increase | `increase(metric[1h])` |
| Sum | `sum(metric)` |
| Average | `avg(metric)` |
| Max | `max(metric)` |
| Min | `min(metric)` |
| Count | `count(metric)` |
| Topk | `topk(5, metric)` |
| Bottomk | `bottomk(5, metric)` |
| Abs | `abs(metric)` |
| Ceil | `ceil(metric)` |
| Floor | `floor(metric)` |

---

# Part 4: Configuration

## 11. VM Configuration

### 11.1 Alert Rules

Alert rules define conditions that trigger alerts.

**Create rules directory:**
```bash
sudo mkdir -p /etc/prometheus/rules
```

**Create comprehensive alert rules:**
```bash
sudo nano /etc/prometheus/rules/alerts.yml
```

```yaml
groups:
  # === System Alerts ===
  - name: system_alerts
    interval: 30s
    rules:
      - alert: InstanceDown
        expr: up == 0
        for: 5m
        labels:
          severity: critical
          category: availability
        annotations:
          summary: "Instance {{ $labels.instance }} is down"
          description: "{{ $labels.instance }} of job {{ $labels.job }} has been down for more than 5 minutes."
          runbook_url: "https://wiki.company.com/runbooks/instance-down"

      - alert: HighCPUUsage
        expr: 100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 80
        for: 10m
        labels:
          severity: warning
          category: performance
        annotations:
          summary: "High CPU usage on {{ $labels.instance }}"
          description: "CPU usage is above 80% for more than 10 minutes.\nCurrent value: {{ $value | humanizePercentage }}"
          dashboard_url: "https://grafana.company.com/d/node-exporter"

      - alert: CriticalCPUUsage
        expr: 100 - (avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100) > 95
        for: 5m
        labels:
          severity: critical
          category: performance
        annotations:
          summary: "Critical CPU usage on {{ $labels.instance }}"
          description: "CPU usage is above 95%!\nCurrent value: {{ $value | humanizePercentage }}"

      - alert: HighMemoryUsage
        expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100 > 85
        for: 5m
        labels:
          severity: warning
          category: performance
        annotations:
          summary: "High memory usage on {{ $labels.instance }}"
          description: "Memory usage is above 85%.\nCurrent value: {{ $value | humanizePercentage }}"

      - alert: CriticalMemoryUsage
        expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100 > 95
        for: 2m
        labels:
          severity: critical
          category: performance
        annotations:
          summary: "Critical memory usage on {{ $labels.instance }}"
          description: "Memory usage is above 95%!\nCurrent value: {{ $value | humanizePercentage }}"

      - alert: DiskSpaceLow
        expr: (node_filesystem_avail_bytes{fstype!~"tmpfs|fuse.lxcfs"} / node_filesystem_size_bytes{fstype!~"tmpfs|fuse.lxcfs"}) * 100 < 15
        for: 5m
        labels:
          severity: warning
          category: capacity
        annotations:
          summary: "Low disk space on {{ $labels.instance }}"
          description: "Disk {{ $labels.mountpoint }} has less than 15% free space.\nAvailable: {{ $value | humanizePercentage }}"

      - alert: DiskSpaceCritical
        expr: (node_filesystem_avail_bytes{fstype!~"tmpfs|fuse.lxcfs"} / node_filesystem_size_bytes{fstype!~"tmpfs|fuse.lxcfs"}) * 100 < 5
        for: 2m
        labels:
          severity: critical
          category: capacity
        annotations:
          summary: "Critical disk space on {{ $labels.instance }}"
          description: "Disk {{ $labels.mountpoint }} has less than 5% free space!\nAvailable: {{ $value | humanizePercentage }}"

      - alert: DiskWillFillIn4Hours
        expr: predict_linear(node_filesystem_free_bytes[1h], 4*3600) < 0
        for: 5m
        labels:
          severity: warning
          category: capacity
        annotations:
          summary: "Disk will fill in 4 hours on {{ $labels.instance }}"
          description: "Based on current trends, disk {{ $labels.mountpoint }} will fill in approximately 4 hours."

  # === Application Alerts ===
  - name: application_alerts
    interval: 30s
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) / rate(http_requests_total[5m]) > 0.05
        for: 5m
        labels:
          severity: warning
          category: errors
        annotations:
          summary: "High error rate on {{ $labels.instance }}"
          description: "Error rate is above 5% for {{ $labels.job }}.\nCurrent value: {{ $value | humanizePercentage }}"

      - alert: CriticalErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) / rate(http_requests_total[5m]) > 0.15
        for: 2m
        labels:
          severity: critical
          category: errors
        annotations:
          summary: "Critical error rate on {{ $labels.instance }}"
          description: "Error rate is above 15% for {{ $labels.job }}!\nCurrent value: {{ $value | humanizePercentage }}"

      - alert: HighRequestLatency
        expr: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])) > 1
        for: 10m
        labels:
          severity: warning
          category: performance
        annotations:
          summary: "High request latency on {{ $labels.instance }}"
          description: "95th percentile latency is above 1 second.\nCurrent value: {{ $value }}s"

      - alert: CriticalRequestLatency
        expr: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m])) > 5
        for: 5m
        labels:
          severity: critical
          category: performance
        annotations:
          summary: "Critical request latency on {{ $labels.instance }}"
          description: "95th percentile latency is above 5 seconds!\nCurrent value: {{ $value }}s"

  # === Recording Rules (Pre-compute expensive queries) ===
  - name: recording_rules
    interval: 30s
    rules:
      - record: job:node_cpu_usage:avg
        expr: 100 - (avg by(job, instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

      - record: job:node_memory_usage:percent
        expr: (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100

      - record: job:http_request_rate:5m
        expr: sum by(job, instance) (rate(http_requests_total[5m]))

      - record: job:http_error_rate:5m
        expr: sum by(job, instance) (rate(http_requests_total{status=~"5.."}[5m])) / sum by(job, instance) (rate(http_requests_total[5m]))
```

**Update prometheus.yml:**
```bash
sudo nano /etc/prometheus/prometheus.yml
```

Add:
```yaml
rule_files:
  - "rules/*.yml"
```

**Validate and reload:**
```bash
# Validate rules
promtool check rules /etc/prometheus/rules/alerts.yml

# Reload Prometheus
curl -X POST http://localhost:9090/-/reload

# Or restart
sudo systemctl restart prometheus
```

**Verify alerts in UI:**
```
http://localhost:9090/alerts
```

### 11.2 AlertManager Installation

```bash
# Create user
sudo useradd --no-create-home --shell /bin/false alertmanager

# Create directories
sudo mkdir /etc/alertmanager
sudo mkdir /var/lib/alertmanager

# Download
cd /tmp
wget https://github.com/prometheus/alertmanager/releases/download/v0.27.0/alertmanager-0.27.0.linux-amd64.tar.gz
tar -xvf alertmanager-0.27.0.linux-amd64.tar.gz
cd alertmanager-0.27.0.linux-amd64

# Install binaries
sudo cp alertmanager /usr/local/bin/
sudo cp amtool /usr/local/bin/

# Set ownership
sudo chown alertmanager:alertmanager /usr/local/bin/alertmanager
sudo chown alertmanager:alertmanager /usr/local/bin/amtool
sudo chown -R alertmanager:alertmanager /etc/alertmanager
sudo chown -R alertmanager:alertmanager /var/lib/alertmanager
```

**Create configuration:**
```bash
sudo nano /etc/alertmanager/alertmanager.yml
```

```yaml
global:
  # How long to wait before sending notification about new alerts
  resolve_timeout: 5m
  
  # Slack webhook (optional)
  slack_api_url: 'https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK'
  
  # Email configuration (optional)
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: 'alertmanager@company.com'
  smtp_auth_username: 'your-email@gmail.com'
  smtp_auth_password: 'your-app-password'
  smtp_require_tls: true

# Custom notification templates
templates:
  - '/etc/alertmanager/templates/*.tmpl'

# Root route - all alerts enter here
route:
  # Group alerts by these labels
  group_by: ['alertname', 'cluster', 'service', 'severity']
  
  # Wait before sending initial notification
  group_wait: 10s
  
  # Wait before sending notification about new alerts in group
  group_interval: 10s
  
  # Wait before resending notification
  repeat_interval: 12h
  
  # Default receiver
  receiver: 'default-team'
  
  # Child routes for specific alert types
  routes:
    # Critical alerts to PagerDuty immediately
    - match:
        severity: critical
      receiver: 'pagerduty-critical'
      continue: true  # Also send to other receivers
      group_wait: 0s
      repeat_interval: 5m
    
    # Warning alerts to Slack
    - match:
        severity: warning
      receiver: 'slack-warnings'
      group_wait: 30s
      repeat_interval: 4h
    
    # Performance issues to performance team
    - match:
        category: performance
      receiver: 'performance-team'
      repeat_interval: 6h
    
    # Capacity alerts to platform team
    - match:
        category: capacity
      receiver: 'platform-team'
      repeat_interval: 6h

# Inhibition rules - suppress certain alerts
inhibit_rules:
  # If instance is down, don't alert on its CPU/memory
  - source_match:
      alertname: 'InstanceDown'
    target_match_re:
      alertname: '(HighCPUUsage|HighMemoryUsage)'
    equal: ['instance']
  
  # If critical alert fires, suppress warning
  - source_match:
      severity: 'critical'
    target_match:
      severity: 'warning'
    equal: ['alertname', 'instance']

# Notification receivers
receivers:
  # Default email receiver
  - name: 'default-team'
    email_configs:
      - to: 'team@company.com'
        send_resolved: true
        headers:
          Subject: '[{{ .Status | toUpper }}{{ if eq .Status "firing" }}:{{ .Alerts.Firing | len }}{{ end }}] {{ .GroupLabels.alertname }}'

  # Slack for warnings
  - name: 'slack-warnings'
    slack_configs:
      - channel: '#alerts-warning'
        send_resolved: true
        title: '{{ .Status | toUpper }}{{ if eq .Status "firing" }}:{{ .Alerts.Firing | len }}{{ end }} - {{ .CommonLabels.alertname }}'
        text: |
          {{ range .Alerts }}
          *Alert:* {{ .Annotations.summary }}
          *Severity:* {{ .Labels.severity }}
          *Description:* {{ .Annotations.description }}
          *Instance:* {{ .Labels.instance }}
          {{ if .Annotations.dashboard_url }}*Dashboard:* {{ .Annotations.dashboard_url }}{{ end }}
          {{ if .Annotations.runbook_url }}*Runbook:* {{ .Annotations.runbook_url }}{{ end }}
          {{ end }}

  # PagerDuty for critical
  - name: 'pagerduty-critical'
    pagerduty_configs:
      - service_key: 'YOUR_PAGERDUTY_SERVICE_KEY'
        send_resolved: true
        description: '{{ .CommonAnnotations.summary }}'
        details:
          firing: '{{ .Alerts.Firing | len }}'
          resolved: '{{ .Alerts.Resolved | len }}'
          alert_name: '{{ .CommonLabels.alertname }}'
          severity: '{{ .CommonLabels.severity }}'

  # Performance team
  - name: 'performance-team'
    email_configs:
      - to: 'performance-team@company.com'
        send_resolved: true

  # Platform team
  - name: 'platform-team'
    email_configs:
      - to: 'platform-team@company.com'
        send_resolved: true
```

**Create systemd service:**
```bash
sudo nano /etc/systemd/system/alertmanager.service
```

```ini
[Unit]
Description=AlertManager
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=alertmanager
Group=alertmanager

ExecStart=/usr/local/bin/alertmanager \
    --config.file=/etc/alertmanager/alertmanager.yml \
    --storage.path=/var/lib/alertmanager/ \
    --web.listen-address=0.0.0.0:9093 \
    --cluster.listen-address=0.0.0.0:9094

Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

**Start AlertManager:**
```bash
sudo systemctl daemon-reload
sudo systemctl start alertmanager
sudo systemctl enable alertmanager
sudo systemctl status alertmanager
```

**Update Prometheus to use AlertManager:**
```bash
sudo nano /etc/prometheus/prometheus.yml
```

Uncomment:
```yaml
alerting:
  alertmanagers:
    - static_configs:
        - targets:
            - 'localhost:9093'
```

```bash
curl -X POST http://localhost:9090/-/reload
```

**Access AlertManager UI:**
```
http://YOUR_SERVER_IP:9093
```

---

## 12. Kubernetes Configuration

### 12.1 ServiceMonitor Explained

**ServiceMonitor** tells Prometheus which Kubernetes Services to scrape.

**How it works:**
```mermaid
graph TB
    subgraph K8S["Kubernetes Cluster"]
        subgraph SM_Config["ServiceMonitor Configuration"]
            SM["ServiceMonitor<br/>━━━━━━━━━━━━━<br/>selector:<br/>  matchLabels:<br/>    app: my-app<br/>    monitoring: true"]
        end
        
        subgraph SVC_Config["Service Configuration"]
            SVC["Service<br/>━━━━━━━━━━━━━<br/>labels:<br/>  app: my-app ✓<br/>  monitoring: true ✓<br/><br/>ports:<br/>  - name: metrics<br/>    port: 8080"]
        end
        
        subgraph PODS["Pod Endpoints"]
            P1["Pod 1<br/>10.0.1.10:8080/metrics"]
            P2["Pod 2<br/>10.0.1.11:8080/metrics"]
            P3["Pod 3<br/>10.0.1.12:8080/metrics"]
        end
        
        subgraph PROM["Prometheus"]
            PS["Prometheus Server<br/>Scrapes every 15s"]
        end
        
        SM -->|"1. Matches labels"| SVC
        SVC -->|"2. Exposes"| P1
        SVC -->|"2. Exposes"| P2
        SVC -->|"2. Exposes"| P3
        PS -->|"3. Discovers & scrapes"| SVC
    end
    
    PO["Prometheus Operator<br/>(watches ServiceMonitors)"] -.->|"Updates config"| PS
    PO -.->|"Watches"| SM
    
    style SM fill:#51cf66,color:#fff
    style SVC fill:#4dabf7,color:#fff
    style P1 fill:#ffd43b,color:#000
    style P2 fill:#ffd43b,color:#000
    style P3 fill:#ffd43b,color:#000
    style PS fill:#ff6b6b,color:#fff
    style PO fill:#845ef7,color:#fff
```

**Complete example:**

```yaml
# complete-app-monitoring.yaml
---
# Application Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: production
  labels:
    app: my-app
    version: v1.0
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
        version: v1.0
      annotations:
        prometheus.io/scrape: "true"  # Optional annotation
        prometheus.io/port: "8080"
        prometheus.io/path: "/metrics"
    spec:
      containers:
      - name: app
        image: your-registry/my-app:v1.0
        ports:
        - name: http
          containerPort: 8080
        - name: metrics
          containerPort: 8080
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"

---
# Service
apiVersion: v1
kind: Service
metadata:
  name: my-app
  namespace: production
  labels:
    app: my-app
    monitoring: "true"  # ServiceMonitor will select this
spec:
  selector:
    app: my-app
  ports:
  - name: http
    port: 80
    targetPort: 8080
  - name: metrics  # IMPORTANT: Must have a name
    port: 8080
    targetPort: 8080
  type: ClusterIP

---
# ServiceMonitor
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: my-app
  namespace: production
  labels:
    release: prometheus  # MUST match Prometheus selector
    app: my-app
spec:
  # Select services to monitor
  selector:
    matchLabels:
      app: my-app
      monitoring: "true"
  
  # Which namespaces to look in
  namespaceSelector:
    matchNames:
      - production
  
  # Scraping configuration
  endpoints:
  - port: metrics        # Must match service port name
    interval: 30s        # Scrape every 30 seconds
    path: /metrics       # Metrics endpoint path
    scheme: http
    
    # Optional: Relabeling
    relabelings:
    # Add pod name as label
    - sourceLabels: [__meta_kubernetes_pod_name]
      targetLabel: pod
    # Add namespace as label
    - sourceLabels: [__meta_kubernetes_namespace]
      targetLabel: namespace
    # Add container name
    - sourceLabels: [__meta_kubernetes_pod_container_name]
      targetLabel: container
```

```bash
kubectl apply -f complete-app-monitoring.yaml

# Verify
kubectl get servicemonitor -n production
kubectl describe servicemonitor my-app -n production

# Check if Prometheus discovered it
kubectl logs -n monitoring prometheus-prometheus-kube-prometheus-prometheus-0 | grep my-app
```

### 12.2 PodMonitor Explained

**PodMonitor** scrapes pods directly, bypassing services.

**When to use PodMonitor:**
- DaemonSets (one pod per node)
- Jobs/CronJobs (temporary pods)
- Sidecars
- Pods without a service

**Example:**

```yaml
# daemonset-with-podmonitor.yaml
---
# DaemonSet
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-problem-detector
  namespace: kube-system
  labels:
    app: node-problem-detector
spec:
  selector:
    matchLabels:
      app: node-problem-detector
  template:
    metadata:
      labels:
        app: node-problem-detector
        monitoring: "enabled"
    spec:
      hostNetwork: true
      containers:
      - name: detector
        image: k8s.gcr.io/node-problem-detector:v0.8.12
        ports:
        - name: metrics
          containerPort: 20257
        resources:
          limits:
            memory: 80Mi
          requests:
            memory: 80Mi

---
# PodMonitor
apiVersion: monitoring.coreos.com/v1
kind: PodMonitor
metadata:
  name: node-problem-detector
  namespace: kube-system
  labels:
    release: prometheus
spec:
  # Select pods to monitor
  selector:
    matchLabels:
      app: node-problem-detector
      monitoring: "enabled"
  
  # Which namespaces
  namespaceSelector:
    matchNames:
      - kube-system
  
  # Scrape configuration
  podMetricsEndpoints:
  - port: metrics
    interval: 30s
    path: /metrics
    
    # Add node information
    relabelings:
    - sourceLabels: [__meta_kubernetes_pod_node_name]
      targetLabel: node
      action: replace
    - sourceLabels: [__meta_kubernetes_pod_name]
      targetLabel: pod
      action: replace
```

---

## 13. Alert Rules & Recording Rules

### 13.1 PrometheusRule in Kubernetes

```yaml
# prometheus-rules-comprehensive.yaml
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: comprehensive-alerts
  namespace: monitoring
  labels:
    release: prometheus
    prometheus: kube-prometheus
spec:
  groups:
  # === Kubernetes Node Alerts ===
  - name: kubernetes_nodes
    interval: 30s
    rules:
    - alert: NodeNotReady
      expr: kube_node_status_condition{condition="Ready",status="true"} == 0
      for: 5m
      labels:
        severity: critical
        component: node
      annotations:
        summary: "Node {{ $labels.node }} is not ready"
        description: "Node {{ $labels.node }} has been unready for more than 5 minutes."
        runbook_url: "https://wiki.company.com/runbooks/node-not-ready"

    - alert: NodeMemoryPressure
      expr: kube_node_status_condition{condition="MemoryPressure",status="true"} == 1
      for: 5m
      labels:
        severity: warning
        component: node
      annotations:
        summary: "Node {{ $labels.node }} under memory pressure"
        description: "Node has insufficient memory available."

    - alert: NodeDiskPressure
      expr: kube_node_status_condition{condition="DiskPressure",status="true"} == 1
      for: 5m
      labels:
        severity: warning
        component: node
      annotations:
        summary: "Node {{ $labels.node }} under disk pressure"
        description: "Node has insufficient disk space available."

    - alert: NodeHighCPU
      expr: (100 - (avg by (instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)) > 85
      for: 10m
      labels:
        severity: warning
        component: node
      annotations:
        summary: "High CPU on node {{ $labels.instance }}"
        description: "CPU usage is {{ $value | humanizePercentage }}"

  # === Pod Alerts ===
  - name: kubernetes_pods
    interval: 30s
    rules:
    - alert: PodCrashLooping
      expr: rate(kube_pod_container_status_restarts_total[15m]) > 0
      for: 5m
      labels:
        severity: warning
        component: pod
      annotations:
        summary: "Pod {{ $labels.namespace }}/{{ $labels.pod }} is crash looping"
        description: "Pod has restarted {{ $value }} times in the last 15 minutes."

    - alert: PodNotReady
      expr: sum by (namespace, pod) (kube_pod_status_phase{phase=~"Pending|Unknown|Failed"}) > 0
      for: 15m
      labels:
        severity: warning
        component: pod
      annotations:
        summary: "Pod {{ $labels.namespace }}/{{ $labels.pod }} not ready"
        description: "Pod has been in {{ $labels.phase }} state for more than 15 minutes."

    - alert: PodHighMemory
      expr: |
        sum(container_memory_usage_bytes{pod!=""}) by (namespace, pod)
        /
        sum(kube_pod_container_resource_limits{resource="memory",pod!=""}) by (namespace, pod)
        * 100 > 90
      for: 5m
      labels:
        severity: warning
        component: pod
      annotations:
        summary: "Pod {{ $labels.namespace }}/{{ $labels.pod }} high memory"
        description: "Memory usage is {{ $value | humanizePercentage }} of limit"

    - alert: PodHighCPU
      expr: |
        sum(rate(container_cpu_usage_seconds_total{pod!=""}[5m])) by (namespace, pod)
        /
        sum(kube_pod_container_resource_limits{resource="cpu",pod!=""}) by (namespace, pod)
        * 100 > 90
      for: 10m
      labels:
        severity: warning
        component: pod
      annotations:
        summary: "Pod {{ $labels.namespace }}/{{ $labels.pod }} high CPU"
        description: "CPU usage is {{ $value | humanizePercentage }} of limit"

  # === Deployment Alerts ===
  - name: kubernetes_deployments
    interval: 30s
    rules:
    - alert: DeploymentReplicasMismatch
      expr: |
        kube_deployment_spec_replicas
        !=
        kube_deployment_status_replicas_available
      for: 15m
      labels:
        severity: warning
        component: deployment
      annotations:
        summary: "Deployment {{ $labels.namespace }}/{{ $labels.deployment }} replica mismatch"
        description: "Desired: {{ $labels.deployment_spec_replicas }}, Available: {{ $labels.deployment_status_replicas_available }}"

    - alert: DeploymentGenerationMismatch
      expr: |
        kube_deployment_status_observed_generation
        !=
        kube_deployment_metadata_generation
      for: 15m
      labels:
        severity: warning
        component: deployment
      annotations:
        summary: "Deployment {{ $labels.namespace }}/{{ $labels.deployment }} generation mismatch"
        description: "Deployment has not been rolled out successfully."

  # === PersistentVolume Alerts ===
  - name: persistent_volumes
    interval: 30s
    rules:
    - alert: PersistentVolumeFillingUp
      expr: |
        (
          kubelet_volume_stats_available_bytes
          /
          kubelet_volume_stats_capacity_bytes
        ) < 0.1
      for: 5m
      labels:
        severity: warning
        component: storage
      annotations:
        summary: "PV {{ $labels.persistentvolumeclaim }} filling up"
        description: "Only {{ $value | humanizePercentage }} available in {{ $labels.namespace }}/{{ $labels.persistentvolumeclaim }}"

    - alert: PersistentVolumeCritical
      expr: |
        (
          kubelet_volume_stats_available_bytes
          /
          kubelet_volume_stats_capacity_bytes
        ) < 0.03
      for: 1m
      labels:
        severity: critical
        component: storage
      annotations:
        summary: "PV {{ $labels.persistentvolumeclaim }} critically full"
        description: "Only {{ $value | humanizePercentage }} available!"

  # === Recording Rules ===
  - name: recording_rules
    interval: 30s
    rules:
    # Node CPU usage
    - record: instance:node_cpu_utilisation:rate5m
      expr: 1 - avg by(instance) (rate(node_cpu_seconds_total{mode="idle"}[5m]))

    # Node memory usage
    - record: instance:node_memory_utilisation:ratio
      expr: 1 - ((node_memory_MemAvailable_bytes) / (node_memory_MemTotal_bytes))

    # Pod CPU usage
    - record: namespace_pod:kube_pod_container_resource_requests:sum
      expr: sum by (namespace, pod) (kube_pod_container_resource_requests{resource="cpu"})

    # Pod memory usage
    - record: namespace_pod:container_memory_usage_bytes:sum
      expr: sum by (namespace, pod) (container_memory_usage_bytes{pod!=""})
```

```bash
kubectl apply -f prometheus-rules-comprehensive.yaml

# Verify
kubectl get prometheusrule -n monitoring

# Check in Prometheus UI
kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-prometheus 9090:9090
# Visit http://localhost:9090/rules
```

---

## 14. AlertManager Configuration

### 14.1 AlertmanagerConfig in Kubernetes

```yaml
# alertmanager-config-comprehensive.yaml
apiVersion: v1
kind: Secret
metadata:
  name: slack-webhook
  namespace: monitoring
type: Opaque
stringData:
  api_url: "https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK"
---
apiVersion: v1
kind: Secret
metadata:
  name: pagerduty-key
  namespace: monitoring
type: Opaque
stringData:
  routing_key: "YOUR_PAGERDUTY_INTEGRATION_KEY"
---
apiVersion: monitoring.coreos.com/v1alpha1
kind: AlertmanagerConfig
metadata:
  name: team-config
  namespace: monitoring
  labels:
    alertmanagerConfig: main
spec:
  # Route configuration
  route:
    groupBy: ['alertname', 'namespace', 'severity']
    groupWait: 30s
    groupInterval: 5m
    repeatInterval: 12h
    receiver: 'default-receiver'
    
    # Child routes
    routes:
    # Critical alerts - immediate PagerDuty
    - matchers:
      - name: severity
        value: critical
      receiver: 'pagerduty-critical'
      continue: true
      groupWait: 0s
      repeatInterval: 5m
    
    # Warning alerts - Slack after 30s
    - matchers:
      - name: severity
        value: warning
      receiver: 'slack-warnings'
      groupWait: 30s
      repeatInterval: 4h
    
    # Node issues - infrastructure team
    - matchers:
      - name: component
        value: node
      receiver: 'infra-team'
      repeatInterval: 6h
    
    # Storage issues - platform team
    - matchers:
      - name: component
        value: storage
      receiver: 'platform-team'
      repeatInterval: 6h

  # Inhibition rules
  inhibitRules:
  # If node is down, don't alert on its pods
  - sourceMatch:
    - name: alertname
      value: NodeNotReady
    targetMatch:
    - name: component
      value: pod
    equal:
    - node
  
  # Critical suppresses warning
  - sourceMatch:
    - name: severity
      value: critical
    targetMatch:
    - name: severity
      value: warning
    equal:
    - alertname
    - namespace

  # Receivers
  receivers:
  # Default email
  - name: 'default-receiver'
    emailConfigs:
    - to: 'team@company.com'
      sendResolved: true
      headers:
      - key: 'Subject'
        value: '[{{ .Status | toUpper }}] {{ .CommonLabels.alertname }}'

  # Slack for warnings
  - name: 'slack-warnings'
    slackConfigs:
    - apiURL:
        key: api_url
        name: slack-webhook
      channel: '#alerts-warning'
      sendResolved: true
      title: '{{ .Status | toUpper }}{{ if eq .Status "firing" }}:{{ .Alerts.Firing | len }}{{ end }} Alerts'
      text: |
        {{ range .Alerts }}
        *Alert:* {{ .Annotations.summary }}
        *Severity:* `{{ .Labels.severity }}`
        *Description:* {{ .Annotations.description }}
        {{ if .Labels.namespace }}*Namespace:* {{ .Labels.namespace }}{{ end }}
        {{ if .Labels.pod }}*Pod:* {{ .Labels.pod }}{{ end }}
        {{ if .Annotations.runbook_url }}*Runbook:* {{ .Annotations.runbook_url }}{{ end }}
        {{ end }}
      color: '{{ if eq .Status "firing" }}danger{{ else }}good{{ end }}'

  # PagerDuty for critical
  - name: 'pagerduty-critical'
    pagerdutyConfigs:
    - routingKey:
        key: routing_key
        name: pagerduty-key
      sendResolved: true
      description: '{{ .CommonAnnotations.summary }}'
      details:
      - key: 'Firing Alerts'
        value: '{{ .Alerts.Firing | len }}'
      - key: 'Severity'
        value: '{{ .CommonLabels.severity }}'
      - key: 'Description'
        value: '{{ .CommonAnnotations.description }}'

  # Infrastructure team
  - name: 'infra-team'
    emailConfigs:
    - to: 'infra@company.com'
      sendResolved: true

  # Platform team
  - name: 'platform-team'
    emailConfigs:
    - to: 'platform@company.com'
      sendResolved: true
```

```bash
kubectl apply -f alertmanager-config-comprehensive.yaml

# Verify
kubectl get alertmanagerconfig -n monitoring

# Check AlertManager configuration
kubectl exec -n monitoring alertmanager-prometheus-kube-prometheus-alertmanager-0 -- amtool config show
```

---

# Part 5: Visualization

## 15. Grafana Dashboards

### 15.1 Creating Your First Dashboard

**Step-by-step:**

1. **Login to Grafana**
   - URL: http://YOUR_GRAFANA_URL:3000
   - Credentials: admin / your-password

2. **Create Dashboard**
   - Click **+** (plus icon) → **Dashboard**
   - Click **+ Add visualization**
   - Select **Prometheus** data source

3. **Add CPU Usage Panel**
   ```promql
   100 - (avg by(instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)
   ```
   
   - **Panel Title:** "CPU Usage"
   - **Description:** "System CPU utilization percentage"
   - **Unit:** Percent (0-100)
   - **Legend:** {{ instance }}
   - **Thresholds:** 
     - Warning: 70
     - Critical: 85
   - Click **Apply**

4. **Add Memory Usage Panel**
   - Click **Add** → **Visualization**
   - Query:
   ```promql
   (node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100
   ```
   - **Title:** "Memory Usage"
   - **Unit:** Percent (0-100)
   - Click **Apply**

5. **Save Dashboard**
   - Click **Save dashboard** (💾 icon)
   - **Name:** "System Monitoring"
   - **Folder:** Create new folder "Infrastructure"
   - Click **Save**

### 15.2 Dashboard JSON Example

Complete dashboard with multiple panels:

```json
{
  "dashboard": {
    "title": "System Overview",
    "tags": ["infrastructure", "system"],
    "timezone": "browser",
    "refresh": "30s",
    "time": {
      "from": "now-6h",
      "to": "now"
    },
    "panels": [
      {
        "id": 1,
        "title": "CPU Usage",
        "type": "graph",
        "gridPos": {"x": 0, "y": 0, "w": 12, "h": 8},
        "targets": [
          {
            "expr": "100 - (avg by(instance) (irate(node_cpu_seconds_total{mode=\"idle\"}[5m])) * 100)",
            "legendFormat": "{{ instance }}",
            "refId": "A"
          }
        ],
        "yaxes": [
          {
            "format": "percent",
            "min": 0,
            "max": 100
          }
        ],
        "alert": {
          "conditions": [
            {
              "evaluator": {
                "params": [80],
                "type": "gt"
              },
              "operator": {"type": "and"},
              "query": {"params": ["A", "5m", "now"]},
              "reducer": {"type": "avg"},
              "type": "query"
            }
          ],
          "executionErrorState": "alerting",
          "frequency": "1m",
          "handler": 1,
          "name": "High CPU Alert",
          "noDataState": "no_data"
        }
      },
      {
        "id": 2,
        "title": "Memory Usage",
        "type": "graph",
        "gridPos": {"x": 12, "y": 0, "w": 12, "h": 8},
        "targets": [
          {
            "expr": "(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100",
            "legendFormat": "{{ instance }}",
            "refId": "A"
          }
        ]
      },
      {
        "id": 3,
        "title": "Disk Usage",
        "type": "gauge",
        "gridPos": {"x": 0, "y": 8, "w": 8, "h": 8},
        "targets": [
          {
            "expr": "100 - ((node_filesystem_avail_bytes{mountpoint=\"/\"} * 100) / node_filesystem_size_bytes{mountpoint=\"/\"})",
            "legendFormat": "{{ instance }}",
            "refId": "A"
          }
        ],
        "fieldConfig": {
          "defaults": {
            "unit": "percent",
            "min": 0,
            "max": 100,
            "thresholds": {
              "mode": "absolute",
              "steps": [
                {"value": 0, "color": "green"},
                {"value": 70, "color": "yellow"},
                {"value": 85, "color": "red"}
              ]
            }
          }
        }
      },
      {
        "id": 4,
        "title": "Network Traffic",
        "type": "graph",
        "gridPos": {"x": 8, "y": 8, "w": 16, "h": 8},
        "targets": [
          {
            "expr": "rate(node_network_receive_bytes_total[5m])",
            "legendFormat": "{{ instance }} - RX",
            "refId": "A"
          },
          {
            "expr": "rate(node_network_transmit_bytes_total[5m])",
            "legendFormat": "{{ instance }} - TX",
            "refId": "B"
          }
        ],
        "yaxes": [
          {
            "format": "Bps"
          }
        ]
      }
    ]
  }
}
```

**Import this dashboard:**
1. Click **+** → **Import**
2. Paste JSON above
3. Click **Load**
4. Select Prometheus data source
5. Click **Import**

---

## 16. Dashboard Design Best Practices

### 16.1 Dashboard Layout Hierarchy

```mermaid
graph TB
    Dashboard[Dashboard] --> Row1[Row 1: Key Metrics<br/>High-level overview]
    Dashboard --> Row2[Row 2: Detailed Graphs<br/>Time-series data]
    Dashboard --> Row3[Row 3: Additional Context<br/>Supporting information]
    Dashboard --> Row4[Row 4: Alerts/Tables<br/>Actionable items]
    
    Row1 --> Stat1[Stat Panel<br/>Single value]
    Row1 --> Stat2[Gauge<br/>With thresholds]
    Row1 --> Stat3[Stat with Sparkline<br/>Trend indicator]
    
    Row2 --> Graph1[Time Series<br/>Main metric]
    
    Row3 --> Graph2[Supporting Graph 1]
    Row3 --> Graph3[Supporting Graph 2]
    
    Row4 --> Table[Alert Table]
    
    style Dashboard fill:#228be6,color:#fff
    style Row1 fill:#51cf66,color:#fff
    style Row2 fill:#ffd43b,color:#000
    style Row3 fill:#ff6b6b,color:#fff
    style Row4 fill:#f08c00,color:#fff
```

### 16.2 Visualization Types Guide

| Panel Type | Best For | Example Use |
|-----------|----------|-------------|
| **Time Series** | Trends over time | CPU, memory, requests |
| **Gauge** | Current value with thresholds | Disk usage, error rate |
| **Stat** | Single important number | Total requests, uptime |
| **Bar Gauge** | Comparing multiple values | Top endpoints, instances |
| **Table** | Detailed data, logs | Alert list, error details |
| **Heatmap** | Distribution over time | Latency distribution |
| **Pie Chart** | Proportions | Status code distribution |

### 16.3 Color Scheme Best Practices

**Status Colors:**
- 🟢 Green: Good, healthy (< 70%)
- 🟡 Yellow: Warning (70-85%)
- 🟠 Orange: Concern (85-95%)
- 🔴 Red: Critical (> 95%)

**Graph Colors:**
- Blue shades: Primary metrics
- Green shades: Success, positive
- Red shades: Errors, negative
- Purple shades: Secondary metrics

### 16.4 Dashboard Variables

Variables make dashboards dynamic and reusable.

**Query Variable:**
```
Variable name: instance
Type: Query
Query: label_values(up, instance)
Multi-value: Yes
Include All: Yes
```

**Use in query:**
```promql
rate(http_requests_total{instance=~"$instance"}[5m])
```

**Interval Variable:**
```
Variable name: interval
Type: Interval
Values: 1m,5m,10m,30m,1h
Auto: Yes
```

**Use in query:**
```promql
rate(http_requests_total[$interval])
```

**Complete Example:**
```json
{
  "templating": {
    "list": [
      {
        "name": "datasource",
        "type": "datasource",
        "query": "prometheus"
      },
      {
        "name": "namespace",
        "type": "query",
        "datasource": "$datasource",
        "query": "label_values(kube_pod_info, namespace)",
        "multi": true,
        "includeAll": true
      },
      {
        "name": "pod",
        "type": "query",
        "datasource": "$datasource",
        "query": "label_values(kube_pod_info{namespace=~\"$namespace\"}, pod)",
        "multi": true,
        "includeAll": true
      },
      {
        "name": "interval",
        "type": "interval",
        "query": "1m,5m,10m,30m,1h,6h,12h,1d",
        "auto": true,
        "auto_count": 30,
        "auto_min": "10s"
      }
    ]
  }
}
```

---

## 17. Pre-built Dashboards

### 17.1 Popular Grafana Dashboards

Import from https://grafana.com/grafana/dashboards/

| ID | Name | Description |
|----|------|-------------|
| 1860 | Node Exporter Full | Complete system metrics dashboard |
| 3662 | Prometheus 2.0 Stats | Prometheus server metrics |
| 7249 | Kubernetes Cluster Monitoring | Comprehensive K8s dashboard |
| 6417 | Kubernetes Pod Metrics | Pod-level resource usage |
| 12740 | Kubernetes / System / Overview | Cluster-wide overview |
| 9628 | PostgreSQL Database | PostgreSQL monitoring |
| 763 | Redis Dashboard | Redis metrics |
| 2949 | MySQL Overview | MySQL monitoring |

### 17.2 How to Import

**Method 1: By ID**
1. Click **+** → **Import**
2. Enter dashboard ID (e.g., 1860)
3. Click **Load**
4. Select Prometheus data source
5. Click **Import**

**Method 2: By JSON**
1. Click **+** → **Import**
2. Paste JSON code
3. Click **Load**
4. Click **Import**

**Method 3: By File**
1. Download .json file
2. Click **+** → **Import**
3. Click **Upload JSON file**
4. Select file
5. Click **Import**

---

# Part 6: Real-World Examples

## 18. Complete Application Monitoring

### 18.1 Full Stack Example

```yaml
# full-stack-monitoring.yaml
---
# Namespace
apiVersion: v1
kind: Namespace
metadata:
  name: webapp
---
# Application Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
  namespace: webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
        version: v1
    spec:
      containers:
      - name: webapp
        image: fabxc/instrumented_app
        ports:
        - name: http
          containerPort: 8080
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "200m"
---
# Service
apiVersion: v1
kind: Service
metadata:
  name: webapp
  namespace: webapp
  labels:
    app: webapp
spec:
  selector:
    app: webapp
  ports:
  - name: http
    port: 8080
    targetPort: 8080
---
# ServiceMonitor
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: webapp
  namespace: webapp
  labels:
    release: prometheus
spec:
  selector:
    matchLabels:
      app: webapp
  endpoints:
  - port: http
    path: /metrics
    interval: 15s
---
# PrometheusRule
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: webapp-alerts
  namespace: monitoring
  labels:
    release: prometheus
spec:
  groups:
  - name: webapp
    interval: 30s
    rules:
    - alert: WebAppDown
      expr: up{job="webapp/webapp"} == 0
      for: 1m
      labels:
        severity: critical
        app: webapp
      annotations:
        summary: "WebApp is down"
        description: "WebApp in namespace webapp has been down for more than 1 minute"
    
    - alert: WebAppHighLatency
      expr: histogram_quantile(0.95, rate(http_request_duration_seconds_bucket{job="webapp/webapp"}[5m])) > 0.5
      for: 5m
      labels:
        severity: warning
        app: webapp
      annotations:
        summary: "WebApp high latency"
        description: "95th percentile latency is {{ $value }}s"
    
    - alert: WebAppHighErrorRate
      expr: |
        rate(http_requests_total{job="webapp/webapp",status=~"5.."}[5m])
        /
        rate(http_requests_total{job="webapp/webapp"}[5m]) > 0.05
      for: 5m
      labels:
        severity: warning
        app: webapp
      annotations:
        summary: "WebApp high error rate"
        description: "Error rate is {{ $value | humanizePercentage }}"
```

### 18.2 Grafana Dashboard for This App

```json
{
  "dashboard": {
    "title": "WebApp Monitoring",
    "panels": [
      {
        "title": "Request Rate",
        "targets": [{
          "expr": "sum(rate(http_requests_total{job='webapp/webapp'}[5m]))"
        }]
      },
      {
        "title": "Error Rate",
        "targets": [{
          "expr": "sum(rate(http_requests_total{job='webapp/webapp',status=~'5..'}[5m])) / sum(rate(http_requests_total{job='webapp/webapp'}[5m]))"
        }]
      },
      {
        "title": "Latency (P95)",
        "targets": [{
          "expr": "histogram_quantile(0.95, rate(http_request_duration_seconds_bucket{job='webapp/webapp'}[5m]))"
        }]
      }
    ]
  }
}
```

---

## 19. Database Monitoring

### 19.1 PostgreSQL Complete Setup

```yaml
# postgres-monitoring.yaml
---
# PostgreSQL Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  namespace: database
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:15
        env:
        - name: POSTGRES_PASSWORD
          value: "yourpassword"
        ports:
        - containerPort: 5432
        volumeMounts:
        - name: data
          mountPath: /var/lib/postgresql/data
      volumes:
      - name: data
        persistentVolumeClaim:
          claimName: postgres-pvc
---
# PostgreSQL Exporter
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres-exporter
  namespace: database
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres-exporter
  template:
    metadata:
      labels:
        app: postgres-exporter
    spec:
      containers:
      - name: exporter
        image: prometheuscommunity/postgres-exporter:latest
        env:
        - name: DATA_SOURCE_NAME
          value: "postgresql://postgres:yourpassword@postgres:5432/postgres?sslmode=disable"
        ports:
        - name: metrics
          containerPort: 9187
---
# Service for Exporter
apiVersion: v1
kind: Service
metadata:
  name: postgres-exporter
  namespace: database
  labels:
    app: postgres-exporter
spec:
  selector:
    app: postgres-exporter
  ports:
  - name: metrics
    port: 9187
---
# ServiceMonitor
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: postgres-exporter
  namespace: database
  labels:
    release: prometheus
spec:
  selector:
    matchLabels:
      app: postgres-exporter
  endpoints:
  - port: metrics
    interval: 30s
```

**Key Metrics to Monitor:**
```promql
# Connection count
pg_stat_database_numbackends

# Active queries
pg_stat_activity_count{state="active"}

# Database size
pg_database_size_bytes

# Transaction rate
rate(pg_stat_database_xact_commit[5m])

# Cache hit ratio
rate(pg_stat_database_blks_hit[5m]) / 
(rate(pg_stat_database_blks_hit[5m]) + rate(pg_stat_database_blks_read[5m]))
```

---

## 20. Kubernetes Cluster Monitoring

### 20.1 Complete Cluster Monitoring

Already included with kube-prometheus-stack! Just verify:

```bash
# Check what's monitoring your cluster
kubectl get servicemonitor -n monitoring

# Expected outputs:
# - kube-state-metrics
# - kubelet
# - kube-apiserver
# - kube-controller-manager
# - kube-scheduler
# - node-exporter
```

### 20.2 Key Kubernetes Queries

```promql
# === Node Metrics ===

# Node count
count(kube_node_info)

# Nodes not ready
kube_node_status_condition{condition="Ready",status="true"} == 0

# Node CPU usage
sum(rate(node_cpu_seconds_total{mode!="idle"}[5m])) by (instance)

# Node memory usage
(node_memory_MemTotal_bytes - node_memory_MemAvailable_bytes) / node_memory_MemTotal_bytes * 100

# === Pod Metrics ===

# Pod count by namespace
count(kube_pod_info) by (namespace)

# Pods not running
sum(kube_pod_status_phase{phase!="Running"}) by (namespace, pod, phase)

# Pod CPU usage
sum(rate(container_cpu_usage_seconds_total{pod!=""}[5m])) by (namespace, pod)

# Pod memory usage
sum(container_memory_usage_bytes{pod!=""}) by (namespace, pod)

# === Deployment Metrics ===

# Deployment replicas
kube_deployment_spec_replicas

# Available replicas
kube_deployment_status_replicas_available

# === Persistent Volume Metrics ===

# PV usage
(kubelet_volume_stats_used_bytes / kubelet_volume_stats_capacity_bytes) * 100

# === API Server Metrics ===

# Request rate
rate(apiserver_request_total[5m])

# Request latency
histogram_quantile(0.95, rate(apiserver_request_duration_seconds_bucket[5m]))
```

---

# Part 7: Production & Operations

## 21. Troubleshooting Guide

### 21.1 Targets Not Showing Up

**Symptoms:**
- Service not in Prometheus targets
- Metrics not being collected

**Diagnosis Steps:**

```bash
# 1. Check if metrics endpoint works
kubectl exec -it POD_NAME -- curl localhost:8080/metrics

# 2. Check ServiceMonitor exists
kubectl get servicemonitor -n NAMESPACE

# 3. Check ServiceMonitor labels match Prometheus selector
kubectl describe servicemonitor NAME -n NAMESPACE

# 4. Check Prometheus Operator logs
kubectl logs -n monitoring deployment/prometheus-kube-prometheus-operator

# 5. Check Prometheus configuration
kubectl exec -n monitoring prometheus-prometheus-0 -- \
  cat /etc/prometheus/config_out/prometheus.env.yaml | grep -A 20 "job_name: YOUR_JOB"

# 6. Check Prometheus logs
kubectl logs -n monitoring prometheus-prometheus-0 prometheus | grep -i "error\|warn"
```

**Common Fixes:**

1. **Missing `release: prometheus` label**
   ```yaml
   labels:
     release: prometheus  # Add this!
   ```

2. **Wrong namespace selector**
   ```yaml
   namespaceSelector:
     matchNames:
       - your-namespace  # Must match where service is
   ```

3. **Port name mismatch**
   ```yaml
   # ServiceMonitor
   endpoints:
   - port: metrics  # Must match service port name
   
   # Service
   ports:
   - name: metrics  # Must match!
   ```

### 21.2 High Memory Usage

**Symptoms:**
- Prometheus OOM killed
- Slow queries
- High TSDB memory

**Diagnosis:**

```promql
# Check series count
prometheus_tsdb_symbol_table_size_bytes

# High cardinality metrics
topk(10, count by (__name__)({__name__=~".+"}))

# Memory usage
process_resident_memory_bytes

# TSDB head series
prometheus_tsdb_head_series
```

**Solutions:**

1. **Drop high-cardinality metrics**
   ```yaml
   metric_relabel_configs:
   - source_labels: [__name__]
     regex: 'high_cardinality_metric.*'
     action: drop
   ```

2. **Increase retention**
   ```yaml
   retention: 15d  # Instead of 30d
   ```

3. **Increase resources**
   ```yaml
   resources:
     requests:
       memory: 8Gi  # Increase
   ```

### 21.3 Alerts Not Firing

**Diagnosis:**

```bash
# Check alert rules are loaded
curl http://localhost:9090/api/v1/rules

# Check alert state
curl http://localhost:9090/api/v1/alerts

# Check AlertManager status
curl http://localhost:9093/api/v1/status

# Check Prometheus → AlertManager connectivity
curl http://localhost:9090/api/v1/alertmanagers
```

**Common Issues:**

1. **Alert rule syntax error**
   ```bash
   promtool check rules alerts.yml
   ```

2. **AlertManager not configured**
   ```yaml
   alerting:
     alertmanagers:
       - static_configs:
           - targets: ['localhost:9093']
   ```

3. **Alert in pending state too long**
   ```yaml
   for: 5m  # Reduce if needed
   ```

---

## 22. Performance Tuning

### 22.1 Scrape Configuration Optimization

```yaml
scrape_configs:
  # High-priority metrics - frequent scraping
  - job_name: 'critical-services'
    scrape_interval: 10s
    scrape_timeout: 5s
  
  # Normal metrics
  - job_name: 'standard-services'
    scrape_interval: 30s
  
  # Low-priority metrics
  - job_name: 'batch-jobs'
    scrape_interval: 60s
```

### 22.2 Storage Optimization

```yaml
# Prometheus configuration
storage:
  tsdb:
    retention.time: 15d      # Keep data for 15 days
    retention.size: 50GB     # Or max 50GB
    min-block-duration: 2h
    max-block-duration: 36h
```

### 22.3 Resource Limits

```yaml
# Kubernetes
resources:
  requests:
    memory: 4Gi
    cpu: 2
  limits:
    memory: 8Gi
    cpu: 4
```

---

## 23. Security Best Practices

### 23.1 Enable Authentication

**Prometheus Basic Auth:**
```yaml
global:
  basic_auth:
    username: 'prometheus'
    password_file: '/etc/prometheus/password'
```

```bash
# Create password file
htpasswd -c /etc/prometheus/password prometheus
```

**Grafana:**
Already has authentication by default.

### 23.2 TLS/SSL Configuration

```yaml
# Prometheus
tls_server_config:
  cert_file: /etc/prometheus/prometheus.crt
  key_file: /etc/prometheus/prometheus.key
```

### 23.3 Kubernetes RBAC

```yaml
# prometheus-rbac.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: prometheus
  namespace: monitoring
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: prometheus
rules:
- apiGroups: [""]
  resources:
  - nodes
  - nodes/metrics
  - services
  - endpoints
  - pods
  verbs: ["get", "list", "watch"]
- apiGroups: [""]
  resources:
  - configmaps
  verbs: ["get"]
- nonResourceURLs: ["/metrics"]
  verbs: ["get"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: prometheus
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: prometheus
subjects:
- kind: ServiceAccount
  name: prometheus
  namespace: monitoring
```

---

## 24. Backup & High Availability

### 24.1 Backup Strategies

**Snapshot Backup (VM):**
```bash
# Create snapshot
curl -XPOST http://localhost:9090/api/v1/admin/tsdb/snapshot

# Backup snapshot
SNAPSHOT_NAME=$(curl -XPOST http://localhost:9090/api/v1/admin/tsdb/snapshot | jq -r '.data.name')
tar -czf prometheus-backup-$(date +%Y%m%d).tar.gz /var/lib/prometheus/snapshots/$SNAPSHOT_NAME
```

**Kubernetes Persistent Storage:**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: prometheus-storage
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 100Gi
  storageClassName: fast-ssd
```

### 24.2 High Availability Setup

**Federation (Multiple Prometheus):**

```yaml
# Global Prometheus scrapes from regional Prometheus
scrape_configs:
  - job_name: 'federate'
    scrape_interval: 30s
    honor_labels: true
    metrics_path: '/federate'
    params:
      'match[]':
        - '{job="prometheus"}'
        - '{__name__=~"job:.*"}'
    static_configs:
      - targets:
        - 'prometheus-us-east:9090'
        - 'prometheus-us-west:9090'
        - 'prometheus-eu:9090'
```

---

# Part 8: Quick Reference

## 25. Command Cheat Sheet

### Essential Commands

```bash
# === Prometheus ===

# Check configuration
promtool check config prometheus.yml

# Check rules
promtool check rules rules.yml

# Reload configuration
curl -X POST http://localhost:9090/-/reload

# Query API
curl 'http://localhost:9090/api/v1/query?query=up'

# Check targets
curl http://localhost:9090/api/v1/targets

# === AlertManager ===

# Check configuration
amtool check-config alertmanager.yml

# Silence alert
amtool silence add alertname=HighCPU --duration=1h --comment="Maintenance"

# List silences
amtool silence query

# === Kubernetes ===

# Get monitoring resources
kubectl get all -n monitoring

# Port forward Prometheus
kubectl port-forward -n monitoring svc/prometheus-kube-prometheus-prometheus 9090:9090

# Port forward Grafana
kubectl port-forward -n monitoring svc/prometheus-grafana 3000:80

# Check ServiceMonitors
kubectl get servicemonitor -A

# Check PrometheusRules
kubectl get prometheusrule -A

# === System ===

# Check service status
systemctl status prometheus
systemctl status grafana-server

# View logs
journalctl -u prometheus -f
kubectl logs -f -n monitoring prometheus-prometheus-0

# Check ports
netstat -tlnp | grep -E ':(9090|9100|3000|9093)'
```

---

## 26. PromQL Query Reference

### System Metrics

```promql
# CPU Usage (%)
100 - (avg by(instance) (irate(node_cpu_seconds_total{mode="idle"}[5m])) * 100)

# Memory Usage (%)
(1 - (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes)) * 100

# Disk Usage (%)
100 - ((node_filesystem_avail_bytes * 100) / node_filesystem_size_bytes)

# Load Average
node_load1
node_load5
node_load15

# Network Receive
rate(node_network_receive_bytes_total[5m])

# Network Transmit
rate(node_network_transmit_bytes_total[5m])
```

### Application Metrics

```promql
# Request Rate
rate(http_requests_total[5m])

# Error Rate (%)
(rate(http_requests_total{status=~"5.."}[5m]) / rate(http_requests_total[5m])) * 100

# P95 Latency
histogram_quantile(0.95, rate(http_request_duration_seconds_bucket[5m]))

# P99 Latency
histogram_quantile(0.99, rate(http_request_duration_seconds_bucket[5m]))

# Average Latency
rate(http_request_duration_seconds_sum[5m]) / rate(http_request_duration_seconds_count[5m])

# Top 5 Endpoints
topk(5, sum by(endpoint) (rate(http_requests_total[5m])))
```

### Kubernetes Metrics

```promql
# Pod CPU Usage
sum(rate(container_cpu_usage_seconds_total{pod!=""}[5m])) by (namespace, pod)

# Pod Memory Usage
sum(container_memory_usage_bytes{pod!=""}) by (namespace, pod)

# Pod Count
count(kube_pod_info) by (namespace)

# Pods Not Running
sum(kube_pod_status_phase{phase!="Running"}) by (namespace, pod, phase)

# Container Restarts
rate(kube_pod_container_status_restarts_total[15m]) > 0

# Node Status
kube_node_status_condition{condition="Ready",status="true"}
```

---

## 27. Common Configuration Templates

### Prometheus Configuration Template

```yaml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']

rule_files:
  - "rules/*.yml"

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'node_exporter'
    static_configs:
      - targets: ['localhost:9100']
```

### AlertManager Configuration Template

```yaml
global:
  resolve_timeout: 5m
  slack_api_url: 'YOUR_WEBHOOK_URL'

route:
  receiver: 'default'
  routes:
  - match:
      severity: critical
    receiver: 'pagerduty'

receivers:
- name: 'default'
  slack_configs:
  - channel: '#alerts'
    
- name: 'pagerduty'
  pagerduty_configs:
  - service_key: 'YOUR_KEY'
```

### Alert Rule Template

```yaml
groups:
- name: my_alerts
  rules:
  - alert: HighCPU
    expr: cpu_usage > 80
    for: 5m
    labels:
      severity: warning
    annotations:
      summary: "High CPU on {{ $labels.instance }}"
      description: "CPU is {{ $value }}%"
```

---

## 28. Emergency Procedures

### Prometheus Down

```bash
# 1. Check if process running
ps aux | grep prometheus

# 2. Check logs
journalctl -u prometheus -n 50

# 3. Validate config
promtool check config /etc/prometheus/prometheus.yml

# 4. Check disk space
df -h /var/lib/prometheus

# 5. Restart
systemctl restart prometheus

# 6. Watch logs
journalctl -u prometheus -f
```

### Disk Full

```bash
# 1. Check usage
df -h

# 2. Find large files
du -sh /var/lib/prometheus/*

# 3. Reduce retention
# Edit prometheus.yml or command flags:
--storage.tsdb.retention.time=7d

# 4. Delete old blocks (if desperate)
rm -rf /var/lib/prometheus/data/01*

# 5. Restart Prometheus
systemctl restart prometheus
```

### High Cardinality Crisis

```promql
# Find problematic metrics
topk(20, count by (__name__)({__name__=~".+"}))
```

```yaml
# Drop them immediately
metric_relabel_configs:
  - source_labels: [__name__]
    regex: 'bad_metric.*'
    action: drop
```

---

## 📚 Appendix

### Glossary

| Term | Definition |
|------|------------|
| **Cardinality** | Number of unique time series (label combinations) |
| **CRD** | Custom Resource Definition (Kubernetes) |
| **Exporter** | Component that translates metrics to Prometheus format |
| **Federation** | Multiple Prometheus servers sharing data |
| **Histogram** | Metric type for distributions |
| **Inhibition** | Suppressing certain alerts based on others |
| **Label** | Key-value pair identifying a time series |
| **PromQL** | Prometheus Query Language |
| **Recording Rule** | Pre-computed query saved as new metric |
| **Retention** | How long Prometheus keeps data |
| **Scrape** | Act of collecting metrics from target |
| **Service Discovery** | Automatic target detection |
| **TSDB** | Time Series Database |

### Port Reference

| Service | Port | Purpose |
|---------|------|---------|
| Prometheus | 9090 | Web UI & API |
| AlertManager | 9093 | Alert handling UI |
| Grafana | 3000 | Dashboard UI |
| Node Exporter | 9100 | System metrics |
| Pushgateway | 9091 | Push metrics |
| PostgreSQL Exporter | 9187 | Database metrics |
| MySQL Exporter | 9104 | Database metrics |
| Redis Exporter | 9121 | Cache metrics |

### External Resources

**Official Documentation:**
- Prometheus: https://prometheus.io/docs/
- Grafana: https://grafana.com/docs/
- Prometheus Operator: https://prometheus-operator.dev/

**Community:**
- Prometheus Slack: https://prometheus.io/community/
- CNCF Slack #prometheus: https://slack.cncf.io/
- Stack Overflow: [prometheus] [grafana]

**Learning:**
- Prometheus Training: https://training.promlabs.com/
- Grafana Tutorials: https://grafana.com/tutorials/
- KodeKloud: Prometheus courses

**Tools:**
- PromLens: https://promlens.com/ (PromQL query builder)
- Prometheus Demo: https://demo.promlabs.com/
- Grafana Play: https://play.grafana.org/

---

## 🎓 Learning Checklist

### Beginner ✅
- [ ] Understand what monitoring is and why it matters
- [ ] Know the difference between Prometheus and Grafana
- [ ] Can install Prometheus on a VM
- [ ] Can access Prometheus UI
- [ ] Can write basic PromQL queries
- [ ] Can create a simple Grafana dashboard

### Intermediate ✅
- [ ] Can configure scrape targets
- [ ] Understand metric types (counter, gauge, histogram)
- [ ] Can instrument an application
- [ ] Can create ServiceMonitors in Kubernetes
- [ ] Can write alert rules
- [ ] Can configure AlertManager
- [ ] Can create dashboards with variables

### Advanced ✅
- [ ] Can design monitoring architecture
- [ ] Can optimize for high cardinality
- [ ] Can create recording rules
- [ ] Can set up federation
- [ ] Can implement high availability
- [ ] Can troubleshoot complex issues
- [ ] Can train others on monitoring

---

## 🎉 Conclusion

You now have everything you need to implement production-grade monitoring with Prometheus and Grafana!

**Remember:**
- Start small, iterate
- Monitor what matters
- Keep dashboards simple
- Alert on symptoms, not causes
- Document your runbooks
- Test your alerts

**Next Steps:**
1. Set up monitoring for one application
2. Create useful dashboards
3. Configure actionable alerts
4. Train your team
5. Iterate and improve

**"You can't improve what you don't measure."**

---

*Last Updated: October 2025*  
*Version: 1.0*  
*Total Pages: 150+*  
*Total Examples: 50+*  
*Total Diagrams: 25+*

**Happy Monitoring! 🚀📊🎉**
