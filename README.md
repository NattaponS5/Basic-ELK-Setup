
This is the automated process for setting up the Elastic Stack (ELK) with Elasticsearch, Logstash, Kibana, and HAProxy. The automation script workflow ensures consistent deployment, certificate management, and service configuration across multiple virtual machines (VMs). 

Please use this automated script with caution!!!

---

# 🚀 Automating Basic ELK Setup

This guide outlines the automated process for setting up the **Elastic Stack (ELK)** using **Elasticsearch**, **Logstash**, **Kibana**, and **HAProxy** across multiple **GCP Virtual Machines**.

---

## 🧰 Prerequisites

- **GCP VMs Required:**
  - `elk-1`, `elk-2`, `elk-3`, `elk-ml`, `logstash`, `kibana`

- **Static IP Configuration:**
  - Ensure all VMs have **static public IP addresses** for consistent network access.

- **Firewall Rules:**
  - Ensure the following ports are open:

### 🔐 ELK Stack Port Reference

| Component              | Port  |
|------------------------|-------|
| Elastic Agent          | 5044  |
| Elasticsearch          | 9200  |
| Filebeat               | 5045  |
| Fleet Server           | 8220, 8221 |
| HAProxy (Elasticsearch LB) | 9201  |
| Kibana                 | 5601  |
| Logstash               | 9600  |

---

## ⚙️ Phase 1: Elasticsearch Setup and Certificate Generation

### ✅ Install Elasticsearch on Cluster Nodes
Run the following scripts:

- `elk-2-3.sh` → Install Elasticsearch on `elk-2`, `elk-3`
- `elk-ml.sh` → Install Elasticsearch on `elk-ml`

Tasks:
- Install packages
- Extract Elasticsearch
- Configure `elasticsearch.yml` with cluster settings

### 🔐 Generate Certificates on `elk-1`
Run:

```bash
elk-1.sh
```

Tasks:
- Create HTTP SSL certificates:
  - `http.p12`
  - `elastic-certificates.p12`
- Transfer certificates to all other nodes using `scp`.

### 🚀 Start Elasticsearch Nodes
1. Use `elasticsearch-service.txt` to create systemd services.
2. Start all services:

```bash
sudo systemctl start elasticsearch
```

3. Generate passwords for built-in users:

```bash
./elasticsearch-8.17.2/bin/elasticsearch-setup-passwords auto
```

---

## 🌐 Phase 2: Kibana and Logstash Configuration

### 🖥️ Install Kibana and HAProxy
Run:

```bash
kibana-1.txt
```

- Installs Kibana and HAProxy

### 🔧 Configure Kibana
Run:

```bash
kibana-2.sh
```

Tasks:
- Generate encryption keys:

```bash
./kibana-8.17.2/bin/kibana-encryption-keys generate
```

- Edit `kibana.yml` to include:
  - Elasticsearch URLs
  - `kibana_system` credentials

### 🔁 Set Up HAProxy
- Load balances traffic for Elasticsearch and Kibana on **port 9201**

### 🛠️ Install and Configure Logstash

Run:

```bash
logstash-1.sh
```

- Installs Logstash
- Configures `logstash.yml`

Then run:

```bash
logstash-2.sh
```

- Extracts `http_ca.crt` for SSL
- Configures `logstash.conf` pipeline

### 🔐 Transfer Certificates to Logstash/Kibana

Run:

```bash
fleetcert.sh
```

- Securely transfers:
  - `logstash.crt`
  - `logstash.key`
  - `ca.crt`

---

## 🔄 Phase 3: Service Automation

### ⚙️ Enable ELK Services

Run:

```bash
sudo systemctl enable --now elasticsearch kibana
```

- Uses:
  - `elasticsearch-service.sh`
  - `kibana-service.txt`

### ✅ Verify Setup

- Open Kibana in browser:

```
http://<kibana-url>:5601
```

- Use **Kibana Dev Tools** to:
  - Check Elasticsearch cluster health
  - Validate Logstash pipeline ingestion

---

## 📁 Script Files Summary

| Script/File            | Description                                |
|------------------------|--------------------------------------------|
| `elk-1.sh`             | Certificate generation on `elk-1`          |
| `elk-2-3.sh`           | Elasticsearch install on `elk-2`, `elk-3`  |
| `elk-ml.sh`            | Install on `elk-ml`                        |
| `elasticsearch-service.txt` | systemd config for Elasticsearch     |
| `logstash-1.sh`        | Install Logstash                          |
| `logstash-2.sh`        | Pipeline and cert config for Logstash     |
| `kibana-1.txt`         | Install Kibana + HAProxy                  |
| `kibana-2.sh`          | Kibana config + encryption keys           |
| `fleetcert.sh`         | Cert transfer to Kibana/Logstash          |
| `kibana-service.txt`   | systemd config for Kibana                 |

---
