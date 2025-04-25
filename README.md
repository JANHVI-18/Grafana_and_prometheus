



# 🚀 **Monitoring with Prometheus and Grafana**

## 📦 **1. Install Node Exporter on Linux/WSL/Docker**
Node Exporter collects system metrics like CPU, memory, and disk and exposes them for Prometheus.

### 🛠️ **Steps:**

1. 📥 **Download and Extract Node Exporter**:
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.9.1/node_exporter-1.9.1.linux-amd64.tar.gz
tar xvfz node_exporter-*.*-amd64.tar.gz
cd node_exporter-*.*-amd64
```
📸 ![Step 1](https://github.com/JANHVI-18/Grafana_and_prometheus/blob/main/images/1.png)

2. ▶️ **Run Node Exporter**:
```bash
./node_exporter
```
🌐 This starts Node Exporter at: `http://localhost:9100`  
📸 ![Step 2](https://github.com/JANHVI-18/Grafana_and_prometheus/blob/main/images/2.png)

3. 🔍 **Verify Metrics**:
Open in browser 👉 [http://localhost:9100/metrics](http://localhost:9100/metrics)  
Or via terminal:
```bash
curl http://localhost:9100/metrics
curl http://localhost:9100/metrics | grep "node_"
```
📸 ![Step 3](https://github.com/JANHVI-18/Grafana_and_prometheus/blob/main/images/3.png)

4. 🧠 **Note WSL IP (if required)**:
```bash
hostname -I
```
📌 If Prometheus runs outside WSL, replace `localhost` with this IP in `prometheus.yml`.

---

## 🐳 **2. Start Prometheus & Grafana with Docker Compose**
Prometheus 🔁 scrapes metrics, and Grafana 📊 visualizes them beautifully.

### 🛠️ **Steps:**

1. 📁 **Create `docker-compose.yml`**:
```yaml
services:
  prometheus:
    image: prom/prometheus
    container_name: prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
    ports:
      - 9090:9090
    restart: unless-stopped
    volumes:
      - ./prometheus:/etc/prometheus
      - prom_data:/prometheus

  grafana:
    image: grafana/grafana
    container_name: grafana
    ports:
      - 3000:3000
    restart: unless-stopped
    environment:
      - GF_SECURITY_ADMIN_USER=admin
      - GF_SECURITY_ADMIN_PASSWORD=grafana
    volumes:
      - ./grafana:/etc/grafana/provisioning/datasources

volumes:
  prom_data:
```

2. 📝 **Create `prometheus.yml`**:
```yaml
global:
  scrape_interval: 15s
  scrape_timeout: 10s
  evaluation_interval: 15s

scrape_configs:
  - job_name: prometheus
    static_configs:
      - targets: ['localhost:9090']

  - job_name: node_exporter
    static_configs:
      - targets: ['172.27.200.56:9100']  # Use WSL IP from `hostname -I`
```

3. 🚀 **Start Monitoring Stack**:
```bash
docker-compose up -d
```
📸 ![Docker Compose](https://github.com/JANHVI-18/Grafana_and_prometheus/blob/main/images/4.png)

4. ✅ **Verify Prometheus**:
- Open [http://localhost:9090](http://localhost:9090)
- Navigate to: **Status → Targets**  
Ensure `node_exporter` shows **UP** ✅

📸 ![Prometheus Targets](https://github.com/JANHVI-18/Grafana_and_prometheus/blob/main/images/5.png)
📸 ![Prometheus Status](https://github.com/JANHVI-18/Grafana_and_prometheus/blob/main/images/6.png)

---

## 📈 **3. Set Up Grafana Dashboard**

### 🛠️ **Steps:**

1. 🔑 **Login to Grafana**:
- Open [http://localhost:3000](http://localhost:3000)
- Default credentials: `admin / admin`
📸 ![Login](https://github.com/JANHVI-18/Grafana_and_prometheus/blob/main/images/7.png)

2. 🔌 **Add Prometheus as a Data Source**:
- Navigate: **Configuration → Data Sources → Add data source**
- Select **Prometheus**
- URL: `http://prometheus:9090` (or your Prometheus container IP)

📸 ![Data Source](https://github.com/JANHVI-18/Grafana_and_prometheus/blob/main/images/9.png)

3. 📥 **Import Node Exporter Dashboard**:
- Go to: **Create (+) → Import**
- Dashboard ID: `1860`
- Select the Prometheus data source → **Import**

📸 ![Import Dashboard](https://github.com/JANHVI-18/Grafana_and_prometheus/blob/main/images/12.1.png)

4. 📊 **View Your Dashboard**:
You’ll now see real-time metrics on:
- CPU 🔧
- Memory 🧠
- Disk 💾
- Network 📡

📸 ![Dashboard](https://github.com/JANHVI-18/Grafana_and_prometheus/blob/main/images/11.png)

---

## 🛠️ **Troubleshooting Tips**

🔌 **Connection Issues?**
- Make sure Node Exporter is running:
```bash
curl http://localhost:9100/metrics
```
- Confirm correct IP is used in `prometheus.yml`.

🔥 **Firewall Block?**
- Ensure port `9100` is not blocked by Windows Defender/WSL firewall.

🔄 **Prometheus Not Scraping?**
- Restart services:
```bash
docker-compose restart
```
- Check logs:
```bash
docker-compose logs prometheus
```

---

## ✅ **Summary**

| Component        | Status                         |
|------------------|-------------------------------|
| 🖥️ Node Exporter | Running on `:9100/metrics`     |
| 📡 Prometheus     | Scraping metrics at `:9090`    |
| 📊 Grafana        | Visualizing data at `:3000`    |

You're now monitoring your system like a pro! 🎉  
Happy Observing! 👩‍💻🧑‍💻📊

--- 
