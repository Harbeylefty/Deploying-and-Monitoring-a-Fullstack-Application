# Deploying and Monitoring a Full-Stack Application

This project demonstrates how to deploy and monitor a full-stack application using Docker, Prometheus, Grafana, Loki, and Nginx Proxy Manager on a custom domain. It features a FastAPI backend and ReactJS frontend, containerized with Docker Compose, deployed on AWS EC2, and enhanced with observability tools and secure traffic management.

## Project Overview

- **Objective**: Containerize and deploy a full-stack application with real-time monitoring and secure access via a custom domain.
- **Tech Stack**:
  - **Backend**: FastAPI
  - **Frontend**: ReactJS
  - **Containerization**: Docker, Docker Compose
  - **Monitoring**: Prometheus, Grafana, Loki, Promtail, cAdvisor
  - **Traffic Management**: Nginx Proxy Manager
  - **Database**: PostgreSQL with Adminer
  - **Deployment**: AWS EC2
  - **Security**: SSL certificates via Let’s Encrypt

## Setup Instructions

### 1. Initial Setup
1. Launch an EC2 instance and configure security groups for ports (e.g., 80, 443, 3000, 5432, 8000, 8080, 9090).
2. SSH into the instance and clone the repository:
   ```bash
   git clone https://github.com/The-DevOps-Dojo/cv-challenge01.git
   cd cv-challenge01
   ```
3. Optionally, connect VSCode to your EC2 instance for easier editing.

### 2. Containerize the Application
- **Backend (FastAPI)**:
  - Navigate to `backend/` and create a `Dockerfile` (see project files for contents).
- **Frontend (ReactJS)**:
  - Navigate to `frontend/` and create a `Dockerfile` (see project files).
- **Docker Compose**:
  - Create `compose.yml` in the root directory to define services: `backend`, `frontend`, `db`, `adminer`, and `proxy_manager` (see project files).

### 3. Configure Monitoring Stack
1. Create a `monitoring/` folder and add:
   - `prometheus.yml`: Configures Prometheus scraping.
   - `loki-config.yml`: Sets up Loki log storage.
   - `promtail-config.yml`: Configures Promtail log scraping.
2. Create `compose.monitoring.yml` to define monitoring services: `prometheus`, `loki`, `promtail`, `grafana`, and `cadvisor` (see project files).
3. Install the Loki Docker driver:
   ```bash
   docker plugin install grafana/loki-docker-driver:2.9.1 --alias loki --grant-all-permissions
   ```
4. Edit `/etc/docker/daemon.json` to use the Loki log driver:
   ```json
   {
     "log-driver": "loki",
     "log-opts": {
       "loki-url": "http://localhost:3100/loki/api/v1/push",
       "loki-batch-size": "400"
     }
   }
   ```

### 4. Environment Variables
- **Backend**: Update `backend/.env` with your EC2 IP and database credentials.
- **Frontend**: Update `frontend/.env` with your EC2 IP.

### 5. Test Initial Deployment
1. Start the application:
   ```bash
   docker compose up -d
   ```
2. Verify containers are running:
   ```bash
   docker ps -a
   ```
3. Check logs if issues arise:
   ```bash
   docker logs <container_id>
   ```
4. Access services via browser (e.g., `http://<EC2_IP>:5173` for frontend).

### 6. Custom Domain Setup
1. Obtain subdomains (e.g., from AfraidDNS) and point them to your EC2 IP.
2. Access Nginx Proxy Manager UI (`<EC2_IP>:81`, default creds: `admin@example.com`/`changeme`).
3. Generate SSL certificates for each subdomain in the UI.
4. Create `Nginx/nginx.conf` with routing rules (see project files).
5. Update `compose.yml` to mount `nginx.conf`:
   ```yaml
   volumes:
     - ./Nginx/nginx.conf:/data/nginx/custom/http_top.conf
   ```
6. Update `backend/.env` and `frontend/.env` with your domain and set `Environment=production`.
7. Restart the application:
   ```bash
   docker compose down
   docker compose up -d
   ```

### 7. Grafana Dashboards
1. Log into Grafana (`<domain>/grafana` or `<EC2_IP>:3000`).
2. Add data sources:
   - **Prometheus**: `http://prometheus:9090/prometheus`
   - **Loki**: `http://loki:3100`
3. Import container dashboard:
   - Use ID `19792`, select Prometheus, and load.
4. Create logs dashboard:
   - Add visualization with Loki, filter by `job=varlogs`, and switch to table view.

### 8. Verify Deployment
- Test subdomains (e.g., `harbeyy.mooo.com`, `db.harbeyy.mooo.com`) for proper routing and SSL.

## Key Features
- **Scalability**: Docker ensures consistent deployment across environments.
- **Security**: SSL certificates and HTTP-to-HTTPS redirection.
- **Observability**: Real-time metrics (Prometheus/Grafana) and logs (Loki/Promtail).
- **Traffic Management**: Nginx Proxy Manager routes requests efficiently.

## Conclusion
This project showcases a production-ready setup for a full-stack application, combining containerization, monitoring, and secure traffic management. It’s ideal for developers looking to scale applications while maintaining performance and observability.

---

