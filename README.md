# E-commerce
# **Automated Flask App Deployment with GitHub, Docker, EC2, Ansible, Prometheus & Grafana**

## **📌 Overview**
This project automates the **end-to-end deployment** of a Flask application with full monitoring using **Prometheus** and **Grafana**.  
The pipeline covers:
- **Source Code Management** → GitHub  
- **Containerization** → Docker  
- **Provisioning** → AWS EC2  
- **Automation** → Ansible  
- **Monitoring Stack** → Prometheus Node Exporter, Prometheus Server, and Grafana

---

## **🚀 Features**
- **GitHub** for code collaboration  
- **Dockerized Flask App** for consistent deployments  
- **AWS EC2** hosting for cloud scalability  
- **Ansible** automation for setup and deployment  
- **Prometheus Node Exporter** for system metrics  
- **Prometheus Server** for metrics collection  
- **Grafana** dashboards for visualization  
- **Configurable inventory** for multiple server targets  

---

## **🛠️ Tech Stack**
| Component | Purpose |
|-----------|---------|
| GitHub | Source control |
| Docker | Containerized services |
| AWS EC2 | Cloud hosting |
| Ansible | Automated provisioning |
| Flask | Python web backend |
| Prometheus Node Exporter | Metrics endpoint |
| Prometheus Server | Central metrics collection |
| Grafana | Metrics visualization |

---

## **📂 Project Structure**
```
.
├── ansible/
│   ├── inventory.ini          # Target EC2 instance(s)
│   ├── playbook.yml           # Main automation playbook
│   ├── roles/                 # Role-based Ansible tasks
│   └── templates/             # Config templates
├── app/
│   ├── app.py                 # Flask application
│   ├── requirements.txt       # Python dependencies
│   └── Dockerfile             # Container build definition
├── monitoring/
│   ├── prometheus.yml         # Prometheus configuration
│   └── docker-compose.yml     # Prometheus + Grafana stack
└── README.md                  # Documentation
```

---

## **⚙️ Setup Instructions**

### **1️⃣ GitHub Setup**
```bash
git init
git remote add origin git@github.com:<username>/<repo>.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

---

### **2️⃣ Dockerize Flask App**
**`app/Dockerfile`**
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt --break-system-packages
COPY . .
CMD ["python", "app.py"]
```

Build & run locally:
```bash
docker build -t flask-app .
docker run -p 5000:5000 flask-app
```

---

### **3️⃣ Launch AWS EC2**
- Ubuntu 22.04
- Open inbound rules for ports:
  - `5000` (Flask)
  - `9100` (Node Exporter)
  - `9090` (Prometheus)
  - `3000` (Grafana)

---

### **4️⃣ Configure Ansible Inventory**
`ansible/inventory.ini`
```ini
[ec2-app]
<EC2_PUBLIC_IP> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/mykey.pem
```

---

### **5️⃣ Ansible Playbook**
Your playbook should:
- Install Docker & dependencies
- Clone Flask app from GitHub
- Build & run Docker containers
- Deploy Node Exporter
- Deploy Prometheus & Grafana

Run:
```bash
ansible-playbook -i ansible/inventory.ini ansible/playbook.yml
```

---

### **6️⃣ Verify Deployment**
- **Flask App:** `http://<EC2_PUBLIC_IP>:5000`
- **Node Exporter:** `http://<EC2_PUBLIC_IP>:9100/metrics`
- **Prometheus:** `http://<EC2_PUBLIC_IP>:9090`
- **Grafana:** `http://<EC2_PUBLIC_IP>:3000` (Default login: `admin/admin`)

---

## **📈 Monitoring Setup**
1. **Prometheus Configuration** (`monitoring/prometheus.yml`):
```yaml
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: "node_exporter"
    static_configs:
      - targets: ["<EC2_PRIVATE_IP>:9100"]
```
2. **Grafana Dashboards**  
   - Add Prometheus as a data source in Grafana  
   - Import dashboard ID `1860` (Node Exporter Full) from Grafana.com

---

## **🐛 Troubleshooting**
| Issue | Fix |
|-------|-----|
| Port already allocated | Stop container or change port in `docker-compose.yml` |
| SSH key permission denied | `chmod 400 mykey.pem` |
| Pip package policy error | Use `--break-system-packages` |

---

## **🔮 Future Enhancements**
- Terraform to automate EC2 provisioning
- GitHub Actions CI/CD pipeline
- Reverse proxy with Nginx & SSL
