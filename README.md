Full-Stack Application Deployment with Docker Compose and Nginx Proxy Manager
This guide explains how to deploy a full-stack application, including a FastAPI backend, React frontend, and a mandatory monitoring stack (Prometheus, Grafana, and Loki) using Docker Compose and Nginx Proxy Manager. SSL support via Let's Encrypt is also covered.

Table of Contents
Requirements
Project Overview
Setup
Cloning the Project
Application Configuration
Running the Application
Monitoring Stack Setup
Nginx Proxy Manager Setup
Custom Domain and SSL
Testing Your Setup
Requirements
Before starting, make sure you have:

An AWS EC2 instance.
Docker and Docker Compose installed on the EC2 instance.
Basic understanding of Docker and Nginx.
Visual Studio Code (VSCode) with SSH access to your EC2 instance.
Custom domain (optional, but recommended for SSL setup).
Project Overview
This project deploys a full-stack web application using Docker Compose. It includes:

Backend (FastAPI): A Python-based API server.
Frontend (React): A modern web application.
Monitoring Stack: Required setup using Prometheus, Grafana, and Loki for monitoring and logging.
Nginx Proxy Manager: Manages incoming requests and SSL termination.
Setup
Cloning the Project
SSH into your EC2 instance and clone the project repository:

bash
Copy code
git clone https://github.com/The-DevOps-Dojo/cv-challenge01.git
cd cv-challenge01
Application Configuration
Backend Configuration:

Navigate to the backend directory and update the .env file with necessary environment variables for your FastAPI backend, such as database credentials and application-specific settings.
Frontend Configuration:

Navigate to the frontend directory and configure the .env file with the API URL for the backend service to ensure proper communication between frontend and backend.
Database:

The application uses PostgreSQL as the database. Ensure that the database is properly configured and accessible from the backend service.
Running the Application
Docker Compose manages all services, including the frontend, backend, database, monitoring tools, and Nginx Proxy Manager.

Run the application with Docker Compose:

bash
Copy code
docker-compose up -d
Docker Compose will automatically pull the necessary images, build the services, and start them. The services will be accessible on the ports specified in the docker-compose.yml file.

Monitoring Stack Setup
The monitoring stack is required and includes Prometheus, Grafana, and Loki.

1. Prometheus
Prometheus collects and stores metrics from your application, monitoring the health of your services.

Prometheus is automatically started by Docker Compose.
It scrapes metrics from the backend (FastAPI) and other services like Nginx and Grafana.
2. Grafana
Grafana is used to visualize metrics collected by Prometheus and to display log data from Loki.

Access Grafana at http://<your-ip>:3000.

Default login credentials:

Username: admin
Password: admin
Change the password upon first login.
Set up Prometheus as a data source in Grafana:

Navigate to Settings > Data Sources > Add new.
Select Prometheus and enter the Prometheus URL: http://prometheus:9090.
Optionally, import dashboards for visualizing API performance, Nginx status, etc.

3. Loki
Loki collects logs from your application and other services, enabling centralized log management.

Access logs via the Explore section in Grafana.
Add Loki as a data source:
Navigate to Settings > Data Sources > Add new.
Select Loki and enter the Loki URL: http://loki:3100.
Nginx Proxy Manager Setup
Nginx Proxy Manager handles routing traffic to your backend and frontend services and provides SSL certificate management.

Once Nginx Proxy Manager is running (via Docker Compose), access it at http://<your-ip>:81. Default login credentials:

Username: admin@example.com
Password: changeme
Change the default credentials after logging in.

Set up proxy hosts for your frontend and backend services. This will forward traffic to the appropriate containers.

Custom Domain and SSL
In Nginx Proxy Manager, add a custom domain when creating proxy hosts.
Enable SSL for the domain by selecting the Let’s Encrypt option.
Nginx Proxy Manager will automatically manage the SSL certificates.
Testing Your Setup
Once everything is set up, you can test your application:

Frontend: Visit http://<your-domain-or-ip>.
Backend: Access the FastAPI documentation at http://<your-domain-or-ip>/docs.
Grafana: View your metrics and logs at http://<your-domain-or-ip>:3000.
Nginx Proxy Manager: Accessible via http://<your-domain-or-ip>:81.
SSL: Verify SSL by visiting the site using https://<your-domain>.
