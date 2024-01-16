<div align="center">
  <a href="http://netflix-clone-with-tmdb-using-react-mui.vercel.app/">
    <img src="./public/assets/netflix-logo.png" alt="Logo" width="100" height="32">
  </a>

  <h3 align="center">Netflix Clone</h3>

  </p>
</div>

<div align="center">
  <img src="./public/assets/pipeline.gif" alt="Logo" width="100%" height="100%">
  </div>


## Prerequisites:

- A TMDB API key for accessing movie databases in your Netflix Clone application.
- DockerHub account for pushing and pulling Docker images.
- Gmail account for email notifications.
- Jenkins, Kubernetes, Docker, and necessary plugins installed.

## Overview:

This project establishes a robust DevOps environment for seamless software development, integration, and deployment. Key components include Jenkins for continuous integration, SonarQube for code quality, Trivy and OWASP Dependency-Check for security scanning, and Prometheus with Grafana for monitoring.

## Project Highlights:

1. **Infrastructure Setup:**
   - Provisioned servers for Jenkins, Monitoring, and Kubernetes nodes.

2. **Toolchain Integration:**
   - Integrated Jenkins as the central orchestrator.
   - Incorporated tools like SonarQube, Trivy, Prometheus, Grafana, and OWASP Dependency-Check.

3. **CI/CD Pipelines:**
   - Automated workflows using Jenkins pipelines.
   - Included stages for code analysis, Docker image building, and Kubernetes deployment.

4. **Security Scanning:**
   - Implemented Trivy and OWASP Dependency-Check for vulnerability scanning.
   - Ensured continuous security checks in the DevOps pipeline.

5. **Monitoring and Visualization:**
   - Set up Prometheus for real-time metrics collection.
   - Integrated Grafana for comprehensive visualization.

6. **Email Notifications:**
   - Configured Jenkins for email alerts based on pipeline results.

## Project Files:

- **Jenkinsfile:** Customizable Jenkins pipeline for your specific workflows.
- **Kubernetes Manifest:** Modify for tailored application deployment on Kubernetes.
- **Dockerfile:** Customize for building Docker images according to project requirements.

## Server Requirements:

We need four servers for our today’s Project:

- **Jenkins Server:** Jenkins, SonarQube (Docker container), Trivy, kubectl.
- **Monitoring Server:** Prometheus, Node Exporter, Grafana.
- **Kubernetes Master Server:** Kubernetes Master Cluster Node for deploying applications on worker nodes.
- **Kubernetes Worker Server:** Kubernetes Worker Node for deploying applications.

# Setup Instructions

## Jenkins Instance:
- **Name:** Provide a name for your Jenkins instance.
- **OS:** Select Ubuntu 22.04 version.
- **Instance Type:** Choose t2.large.
- **Key Pair:** Provide an existing key or create a new one.
- **Networking:** Keep the default settings.
- **Security Groups:** Open all inbound and outbound traffic.
- **Storage:** Increase capacity from 8GB to 35GB.

## Monitoring Server:
- **Name:** Provide a name for your Monitoring instance.
- **OS:** Select Ubuntu 22.04 version.
- **Instance Type:** Choose t2.medium (minimum 4GB RAM for monitoring tools).
- **Key Pair:** Provide an existing key or create a new one.
- **Networking:** Keep the default settings.
- **Security Groups:** Open all inbound and outbound traffic.
- **Storage:** Increase capacity from 8GB to 15GB.
## Kubernetes Master Instance:
- **Name:** Provide a name for your Kubernetes Master instance.
- **OS:** Select Ubuntu 22.04 version.
- **Number of Instances:** Replace 1 with 2 for two Kubernetes Nodes.
- **Instance Type:** Choose t2.medium (at least 2 CPUs).
- **Key Pair:** Provide an existing key or create a new one.
- **Networking:** Keep the default settings.
- **Security Groups:** Open all inbound and outbound traffic.

Great! You've provided a set of commands for installing OpenJDK and Jenkins on your Jenkins Server. Let's summarize the steps:

# Jenkins Server Setup

### Connect to Jenkins Server:
- Use SSH to connect to your Jenkins Server.

### Install OpenJDK and Jenkins:

#### Install OpenJDK:
```bash
sudo apt update -y
sudo apt install openjdk-11-jre -y
java --version
```

#### Install Jenkins:
```bash
curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y
```

#### To Start Jenkins:
```bash
sudo systemctl enable Jenkins.service
sudo systemctl start Jenkins.service
```

### Check Jenkins Server Status:
- After installation, ensure that Jenkins is running properly.
```bash
sudo systemctl status Jenkins.service
```

### Access Jenkins Web Interface:
- Copy Jenkins Server Public IP and paste it into your browser with port number 8080.

### Obtain Jenkins Initial Admin Password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### Continue Setup:
- Paste the obtained password into the Jenkins web interface and click on Continue.

### Jenkins Configuration:

### Install Suggested Plugins:
- Install Suggested Plugins:
   - Click on "Install suggested plugins."
   - You will get “Create First Admin User” or You can “Skip and Continue as Admin” and setup later
### Save and Finish:
- Click on "Save and Finish."

# Installing Docker, Configuring on Jenkins Server, and Installing SonarQube

## Install Docker and Configure on Jenkins Server:

### Update and Install Docker:
```bash
sudo apt update
sudo apt install docker.io -y
```

### Add Jenkins User to Docker Group:
```bash
sudo usermod -aG docker jenkins
```

### Add Ubuntu User to Docker Group (if needed):
```bash
sudo usermod -aG docker ubuntu
```

### Restart Docker Service:
```bash
sudo systemctl restart docker
```

### Adjust Docker Socket Permissions:
```bash
sudo chmod 777 /var/run/docker.sock
```

## Install SonarQube on Jenkins Server:

### Run SonarQube Docker Container:
```bash
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
```

### Access SonarQube Web Interface:
- Copy your Jenkins Server Public IP and add port 9000 in your browser.

### Login to SonarQube:
- Username: admin
- Password: admin

### Reset Password and Update:
- Reset the password and click on Update.

### Verify SonarQube Server:
- Confirm that your SonarQube Server is accessible.

## Installing Trivy on Jenkins Server

### Install Required Packages:
```bash
sudo apt-get install wget apt-transport-https gnupg lsb-release
```

### Add Trivy GPG Key:
```bash
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
```

### Add Trivy Repository:
```bash
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
```

### Update Package List:
```bash
sudo apt-get update
```

### Install Trivy:
```bash
sudo apt-get install trivy
```
Feel free to adjust the formatting or include additional details if needed. Let me know if you have any specific preferences or modifications you'd like to make!

# Monitoring Server Setup - Prometheus, Node Exporter, and Grafana

## Install and Configure Prometheus:

### Create Prometheus User:
```bash
sudo useradd -r -s /bin/false prometheus
```

### Download and Extract Prometheus:
```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.49.0-rc.1/prometheus-2.49.0-rc.1.linux-amd64.tar.gz
tar -xvf prometheus-2.49.0-rc.1.linux-amd64.tar.gz
cd prometheus-2.49.0-rc.1.linux-amd64/
sudo mv prometheus promtool /usr/local/bin/
sudo mv consoles console_libraries/ prometheus.yml /etc/prometheus/
```

### Set Permissions:
```bash
sudo chown -R prometheus:prometheus /etc/prometheus/ /data/
```

### Check and Validate the Prometheus Version:
```bash
prometheus --version
```

### Create systemd Configuration for Prometheus:
- Edit `/etc/systemd/system/prometheus.service` and paste the provided configuration.

### Enable and Start Prometheus Service:
```bash
sudo systemctl enable prometheus.service
sudo systemctl start prometheus.service
sudo systemctl status prometheus.service
```

### Access Prometheus Web Interface:
- Open Monitoring Server Public IP with port 9090 in your browser.

## Install and Configure Node Exporter:

### Create Node Exporter User:
```bash
sudo useradd -r -s /bin/false node_exporter
```

### Download and Extract Node Exporter:
```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz
tar -xvf node_exporter-1.7.0.linux-amd64.tar.gz
sudo mv node_exporter-1.7.0.linux-amd64/node_exporter /usr/local/bin/
```

### Create systemd Configuration for Node Exporter:
- Edit `/etc/systemd/system/node_exporter.service` and paste the provided configuration.

### Enable and Start Node Exporter Service:
```bash
sudo systemctl enable node_exporter
sudo systemctl start node_exporter
sudo systemctl status node_exporter.service
```

### Update Prometheus Configuration:
- Edit `/etc/prometheus/prometheus.yml` and add a new target for Node Exporter.

### Validate and Reload Prometheus Configuration:
```bash
promtool check config /etc/prometheus/prometheus.yml
curl -X POST http://localhost:9090/-/reload
```

### Verify Node Exporter in Prometheus:
- Confirm that your Node Exporter target is up and running in Prometheus.

## Install and Configure Grafana:

### Install Dependencies:
```bash
sudo apt-get install -y apt-transport-https software-properties-common wget
```

### Add Grafana GPG Key and Repository:
```bash
sudo mkdir -p /etc/apt/keyrings/
wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list
```

### Update Package List:
```bash
sudo apt-get update
```

### Install Grafana:
```bash
sudo apt-get install grafana
```

### Enable and Start Grafana Service:
```bash
sudo systemctl enable grafana-server.service
sudo systemctl start grafana-server.service
sudo systemctl status grafana-server.service
```

### Access Grafana Web Interface:
- Copy Monitoring Server Public IP and open it in your browser with port 3000.
  - Username: admin
  - Password: admin

### Configure Prometheus Data Source:
- Go to "Data sources" in Grafana.
- Select Prometheus.
- Provide Monitoring Server Public IP with port 9090.
- Click on Save and test.

### Import Node Exporter Dashboard:
- Go to "Dashboard" -> "Import."
- Add 1860 for the Node Exporter dashboard.
- Select Prometheus as the data source.
- Click on Import.

### Monitor Jenkins Server:

#### Install Prometheus Metric Plugin on Jenkins.

#### Edit `/etc/prometheus/prometheus.yml` and add a job for Jenkins.
- job_name: "jenkins"
  static_configs:
    - targets: ["<jenkins-server-public-ip>:8080"]

#### Validate the Prometheus configuration.
```bash
promtool check config /etc/prometheus/prometheus.yml
```

#### Reload Prometheus configuration:
```bash
curl -X POST http://localhost:9090/-/reload
```

#### Access Prometheus targets in the browser.

### Import Jenkins Dashboard to Grafana:
- Go to "Dashboard" -> "Import."
- Add 9964 for the Jenkins Monitoring dashboard.
- Select Prometheus as the data source.
- Click on Import.

### View Dashboards:
- You will see your Node Exporter and Jenkins Monitoring dashboards in Grafana.
