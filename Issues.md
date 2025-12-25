# 🚀 Complete DevOps CI/CD Pipeline Project

## 📋 Project Overview

This project documents my journey building a complete CI/CD pipeline from scratch as a DevOps beginner. This README contains not just the final working solution, but also every challenge I faced, mistakes I made, and lessons I learned along the way.

**Project Goal:** Automate the build, test, analysis, security scanning, and deployment of a Java Spring Boot application using industry-standard DevOps tools.

---

## 🎯 What I Built

A complete CI/CD pipeline that:
- ✅ Automatically builds Java applications with Maven
- ✅ Runs automated tests
- ✅ Performs code quality analysis with SonarQube
- ✅ Scans for security vulnerabilities using Trivy
- ✅ Packages artifacts and deploys to Nexus Repository
- ✅ Uses Infrastructure as Code (Terraform)
- ✅ Automates configuration with Ansible

---

## 🛠️ Technology Stack

| Tool | Version | Purpose |
|------|---------|---------|
| **AWS EC2** | Ubuntu 22.04 | Cloud Infrastructure |
| **Terraform** | Latest | Infrastructure as Code |
| **Ansible** | Latest | Configuration Management |
| **Jenkins** | Latest | CI/CD Orchestration |
| **Docker** | Latest | Containerization |
| **Maven** | 3.9.x | Build Tool |
| **JDK** | Temurin 17 | Java Runtime |
| **SonarQube** | LTS Community | Code Quality Analysis |
| **Trivy** | Latest | Security Scanning |
| **Nexus** | OSS 3.x | Artifact Repository |

---

## 📐 Architecture

```
GitHub Repo
    ↓
Jenkins Pipeline
    ↓
    ├─→ Compile & Test (Maven + JDK 17)
    ├─→ Code Quality (SonarQube)
    ├─→ Security Scan (Trivy)
    ├─→ Package (Maven)
    └─→ Deploy (Nexus Repository)
```

**Infrastructure:**
- **Server 1:** Jenkins (t2.medium)
- **Server 2:** SonarQube (t2.medium - needs 4GB RAM)
- **Server 3:** Nexus Repository (t2.medium)
- **Server 4:** Ansible Control Node (t2.micro)
- **Servers 5-6:** Managed nodes via Ansible (t2.medium)

---

## 🚧 Phase-by-Phase Journey

### Phase 1: Infrastructure Setup with Terraform

**What I Did:**
- Created AWS IAM user with programmatic access
- Configured AWS CLI locally
- Wrote Terraform configuration to provision EC2 instances
- Generated SSH key pairs for secure access

**Challenges I Faced:**

❌ **Problem 1: AWS CLI installation failed on `/mnt/c` (WSL)**
```bash
# This failed:
cd /mnt/c/Users/scl
sudo ./aws/install
# Error: Python execution failed
```

✅ **Solution:** Always work in Linux filesystem, not Windows-mounted paths
```bash
cd ~
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o awscliv2.zip
unzip awscliv2.zip
sudo ./aws/install
```

**Key Learning:** WSL has two filesystems - use `~` (Linux) for DevOps tools, not `/mnt/c` (Windows).

---

❌ **Problem 2: Terraform couldn't find SSH public key**
```bash
terraform validate
# Error: no file exists at "project-key-m2.pub"
```

✅ **Solution:** Move key files into Terraform directory
```bash
mv ~/project-key-m2* ~/devops-cicd-project/
```

**Key Learning:** Terraform's `file()` function only reads files within the project directory.

---

❌ **Problem 3: EC2 creation failed - "couldn't find resource"**
```bash
terraform apply
# Error: collecting instance settings: couldn't find resource
```

**Root Cause:** Hardcoded AMI ID was deprecated/unavailable.

✅ **Solution:** Use dynamic AMI lookup
```hcl
data "aws_ami" "ubuntu" {
  most_recent = true
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-jammy-22.04-amd64-server-*"]
  }
  owners = ["099720109477"] # Canonical
}

resource "aws_instance" "ins-01" {
  ami = data.aws_ami.ubuntu.id
  # ...
}
```

**Key Learning:** Never hardcode AMI IDs - they're region-specific and get deprecated.

---

❌ **Problem 4: SSH timeout when connecting to EC2**
```bash
ssh -i project-key-m2 ubuntu@13.203.67.250
# ssh: connect to host port 22: Connection timed out
```

✅ **Solution:** Add SSH rule to Security Group
- AWS Console → EC2 → Security Groups
- Add Inbound Rule: Type=SSH, Port=22, Source=0.0.0.0/0

**Key Learning:** No matter how correct your SSH key is, without port 22 open in Security Group, connection will timeout.

---

### Phase 2: Configuration Management with Ansible

**What I Did:**
- Set up Ansible control node
- Created inventory file for managed nodes
- Wrote playbook to install Docker on all servers

**Challenges I Faced:**

❌ **Problem 1: Docker permission denied**
```bash
docker ps
# permission denied while trying to connect to Docker daemon socket
```

✅ **Solution:**
```bash
sudo usermod -aG docker ubuntu
newgrp docker
```

**Key Learning:** By default, only root can access Docker. Add users to `docker` group instead of using `sudo` everywhere.

---

❌ **Problem 2: Ansible couldn't SSH to managed nodes**
```bash
ansible servers -i inventory -m ping
# Permission denied (publickey)
```

**Root Cause:** Private key wasn't present on Ansible control node.

✅ **Solution:** Securely copy SSH key using `scp`
```bash
# From local machine:
scp -i project-key-m2 project-key-m2 ubuntu@<ANSIBLE-IP>:/home/ubuntu/
```

**Key Learning:** Never manually copy-paste private keys. Use `scp` for secure file transfer between servers.

---

### Phase 3: Jenkins Setup & Configuration

**What I Did:**
- Installed Jenkins on EC2
- Configured JDK 17 and Maven 3.9
- Installed required plugins

**Challenges I Faced:**

❌ **Problem 1: Plugin installation taking 20+ minutes**

✅ **Solution:** This is normal on small EC2 instances. Restarted Jenkins after 20 mins:
```bash
sudo systemctl restart jenkins
```

**Key Learning:** Jenkins plugin installation on t2.micro/small can be slow. t2.medium is recommended.

---

❌ **Problem 2: Jenkins tool "jdk-17" was actually Java 8**
```bash
java -version
# openjdk version "1.8.0_472"
```

**Root Cause:** Jenkins cached wrong JDK during installation.

✅ **Solution:** 
1. Delete existing jdk-17 in Jenkins → Tools
2. Add new JDK → Install from adoptium.net
3. Name: `jdk-17`, Version: `jdk-17.x (latest)`
4. **Leave JAVA_HOME empty** (Jenkins manages it)

**Key Learning:** Jenkins tool names don't guarantee versions. Always verify with `java -version` in a test pipeline.

---

❌ **Problem 3: Maven not found in Execute Shell**
```bash
mvn -version
# mvn: not found
```

**Root Cause:** Execute Shell doesn't automatically get Jenkins tools.

✅ **Solution:** Use "Invoke top-level Maven targets" instead of Execute Shell

**Key Learning:** Jenkins provides tool-aware build steps. Use them instead of raw shell commands.

---

❌ **Problem 4: Maven compiler error - "invalid target release: 11"**
```bash
mvn compile
# Fatal error compiling: invalid target release: 11
```

**Root Cause:** Project targets Java 11, but Jenkins was using Java 8.

✅ **Solution:** Force JAVA_HOME in pipeline
```groovy
environment {
    JAVA_HOME = tool(name: 'jdk-17', type: 'hudson.model.JDK')
    PATH = "${JAVA_HOME}/bin:${env.PATH}"
}
```

**Key Learning:** Maven uses `JAVA_HOME`, not just Jenkins tool selection. Set it explicitly.

---

### Phase 4: GitHub Integration

**Challenges I Faced:**

❌ **Problem: GitHub authentication failed**
```bash
git fetch
# Authentication failed for 'https://github.com/...'
```

**Root Cause:** GitHub disabled password authentication in 2021.

✅ **Solution:** Use Personal Access Token
1. GitHub → Settings → Developer settings → Tokens (classic)
2. Generate token with `repo` scope
3. Jenkins → Credentials → Add → Secret text
4. Use `credentialsId` in pipeline:
```groovy
git branch: 'main',
    credentialsId: 'github-creds',
    url: 'https://github.com/Tejas1024/Boardgame.git'
```

**Key Learning:** Never use passwords for Git. Always use tokens or SSH keys.

---

### Phase 5: SonarQube Integration

**What I Did:**
- Deployed SonarQube container on dedicated EC2
- Configured Jenkins-SonarQube connection
- Added code analysis to pipeline

**Challenges I Faced:**

❌ **Problem 1: SonarQube container immediately stopped**
```bash
docker ps
# (empty - sonarqube not running)

docker logs sonarqube
# Elasticsearch bootstrap checks failed
```

**Root Cause:** Linux kernel parameter `vm.max_map_count` too low for Elasticsearch.

✅ **Solution:**
```bash
sudo sysctl -w vm.max_map_count=262144
echo "vm.max_map_count=262144" | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

**Key Learning:** SonarQube requires this kernel setting on all Linux systems. It's mandatory, not optional.

---

❌ **Problem 2: SonarQube kept stopping on t2.micro**
```bash
docker logs sonarqube
# Process[ElasticSearch] exited with value 143
```

**Root Cause:** 1GB RAM is insufficient for SonarQube + Elasticsearch.

✅ **Solution:** Upgraded EC2 instance to t2.medium (4GB RAM)

**Key Learning:** SonarQube minimum requirement is ~4GB RAM. Don't use t2.micro/small.

---

❌ **Problem 3: Jenkins couldn't reach SonarQube - "Connect timed out"**
```bash
mvn sonar:sonar
# SonarQube server [http://IP:9000] can not be reached
```

**Root Cause:** Port 9000 not open in Security Group.

✅ **Solution:** 
- AWS Console → EC2 → Security Group
- Add Inbound Rule: Type=Custom TCP, Port=9000, Source=0.0.0.0/0

**Key Learning:** Every service needs its port explicitly opened in AWS Security Groups.

---

❌ **Problem 4: Wrong SonarQube IP configured**
```bash
# Jenkins was configured with old IP
http://13.127.48.8:9000

# But actual IP was:
curl ifconfig.me
# 3.108.219.215
```

✅ **Solution:** Updated Jenkins → System → SonarQube server URL with correct IP

**Key Learning:** AWS public IPs can change if instance is stopped/started. Use Elastic IPs for production.

---

### Phase 6: Trivy Security Scanning

**What I Did:**
- Installed Trivy on Jenkins server
- Added filesystem vulnerability scanning
- Generated HTML reports

**Challenges I Faced:**

❌ **Problem 1: trivy: not found**
```bash
trivy fs --format table .
# trivy: not found
```

✅ **Solution:** Install Trivy on Jenkins server
```bash
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy
```

**Key Learning:** Security scanners must be installed on the CI server, not inside pipeline.

---

❌ **Problem 2: HTML template not found**
```bash
trivy fs --format template --template @$HOME/html.tpl .
# no such file or directory: /var/lib/jenkins/html.tpl
```

✅ **Solution:** Create template with proper ownership
```bash
sudo nano /var/lib/jenkins/html.tpl
# (paste template content)
sudo chown jenkins:jenkins /var/lib/jenkins/html.tpl
```

**Key Learning:** Jenkins runs as user `jenkins`, not `ubuntu`. Files must be accessible to jenkins user.

---

### Phase 7: Nexus Artifact Repository

**What I Did:**
- Deployed Nexus container on dedicated EC2
- Configured Maven to deploy artifacts
- Integrated with Jenkins pipeline

**Challenges I Faced:**

❌ **Problem 1: Both SonarQube and Nexus on same server**
```bash
docker ps -a
# sonarqube   Exited (255)
# nexus       Exited (255)
```

**Root Cause:** Both services fighting for resources on single instance.

✅ **Solution:** Separated to different EC2 instances
- Server 1: SonarQube only
- Server 2: Nexus only

**Key Learning:** SonarQube and Nexus should run on separate hosts to avoid resource contention.

---

❌ **Problem 2: pom.xml had duplicate and broken configurations**
```xml
<distributionManagement>
    <repository>
        <url>http://<http://13.127.109.29:8081/...</url>
```

✅ **Solution:** Cleaned up pom.xml with correct structure
```xml
<distributionManagement>
    <repository>
        <id>maven-releases</id>
        <url>http://13.127.109.29:8081/repository/maven-releases/</url>
    </repository>
    <snapshotRepository>
        <id>maven-snapshots</id>
        <url>http://13.127.109.29:8081/repository/maven-snapshots/</url>
    </snapshotRepository>
</distributionManagement>
```

**Key Learning:** Maven `<id>` in settings.xml must match `<id>` in pom.xml exactly.

---

## 🎓 Major Lessons Learned

### 1. **WSL Filesystem Matters**
- ❌ Never install DevOps tools in `/mnt/c` (Windows mount)
- ✅ Always use `~` (Linux home directory)
- Why: Permission issues, path problems, Python execution failures

### 2. **Security Groups Are Critical**
- Every service needs explicit port opening
- Timeout errors are usually Security Group issues, not code
- Check this FIRST before debugging application

### 3. **Resource Requirements Are Real**
- t2.micro: Jenkins only (with patience)
- t2.medium: SonarQube, Nexus (minimum)
- Don't undersize - you'll waste more time troubleshooting

### 4. **Tool Names vs Actual Versions**
- Jenkins tool name "jdk-17" doesn't guarantee Java 17
- Always verify: `java -version` in actual pipeline
- Delete and recreate if wrong version cached

### 5. **Maven Needs Explicit JAVA_HOME**
- Jenkins tool selection isn't enough
- Set in pipeline environment block
- Compiler target must be ≤ JDK version

### 6. **Never Hardcode AMI IDs**
- They're region-specific
- They get deprecated
- Use data sources to fetch latest

### 7. **Private Keys Are Sensitive**
- Never copy-paste manually
- Use `scp` for server-to-server transfer
- Set correct permissions (400)

### 8. **GitHub Doesn't Use Passwords Anymore**
- Use Personal Access Tokens
- Store in Jenkins Credentials
- Never commit tokens to code

### 9. **SonarQube Needs Special Setup**
- Kernel parameter: `vm.max_map_count=262144`
- Minimum 4GB RAM
- Takes 2-3 minutes to start

### 10. **Separate Services, Separate Servers**
- SonarQube + Nexus on one server = both crash
- Each needs dedicated resources
- This is standard practice, not overkill

---

## 🏗️ Final Working Pipeline

```groovy
pipeline {
    agent any

    tools {
        jdk 'jdk-17'
        maven 'maven-3'
    }

    stages {
        stage('Clone GitHub Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Tejas1024/Boardgame.git'
            }
        }

        stage('Compile Source Code') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test Source Code') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Code Analysis') {
            steps {
                withSonarQubeEnv('Sonarqube') {
                    sh '''
                        mvn sonar:sonar \
                        -Dsonar.projectKey=boardgame \
                        -Dsonar.projectName=boardgame \
                        -Dsonar.java.binaries=target/classes
                    '''
                }
            }
        }

        stage('Trivy File System Scan') {
            steps {
                sh '''
                    trivy fs --format table --output trivy-fs-report.txt .
                    trivy fs --format template --template @/var/lib/jenkins/html.tpl --output trivy-fs-report.html .
                '''
            }
        }

        stage('Publish Trivy Report') {
            steps {
                publishHTML(target: [
                    reportDir: '.',
                    reportFiles: 'trivy-fs-report.html',
                    reportName: 'Trivy File System Scan Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }

        stage('Package Application') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Deploy to Nexus Repository') {
            steps {
                withMaven(
                    globalMavenSettingsConfig: 'maven-settings',
                    jdk: 'jdk-17',
                    maven: 'maven-3'
                ) {
                    sh 'mvn deploy'
                }
            }
        }
    }
}
```

---

## 📊 Results

✅ **Successful Pipeline Execution:**
- Average build time: ~3-5 minutes
- All stages passing consistently
- Artifacts successfully deployed to Nexus
- Security reports generated and published
- Code quality metrics tracked in SonarQube

---

## 🚀 How to Reproduce This Project

### Prerequisites
- AWS Account with billing enabled
- GitHub account
- Local machine with WSL2 (Windows) or native Linux/Mac
- Basic understanding of command line

### Step-by-Step Setup

1. **Clone this repository**
```bash
git clone https://github.com/Tejas1024/Boardgame.git
cd Boardgame
```

2. **Configure AWS CLI**
```bash
aws configure
# Enter your Access Key ID and Secret Access Key
```

3. **Generate SSH Key**
```bash
ssh-keygen -t rsa -b 4096 -f project-key-m2
```

4. **Deploy Infrastructure**
```bash
cd terraform
terraform init
terraform plan
terraform apply
```

5. **Follow phases 2-7** as documented in this README

---

## 🔧 Improvements for Production

While this works great for learning, here's what I'd change for production:

1. **Security:**
   - Use AWS Secrets Manager for credentials
   - Implement least-privilege IAM roles
   - Enable HTTPS for all services
   - Use private subnets with NAT gateway

2. **High Availability:**
   - Multi-AZ deployment
   - Load balancers for Jenkins
   - Backup strategies for Nexus/SonarQube

3. **Monitoring:**
   - CloudWatch for AWS resources
   - Prometheus + Grafana for metrics
   - Centralized logging with ELK stack

4. **Cost Optimization:**
   - Use spot instances for non-critical servers
   - Auto-scaling groups
   - Scheduled start/stop for dev environments

---

## 📚 Resources That Helped Me

- [Terraform AWS Provider Docs](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Jenkins Documentation](https://www.jenkins.io/doc/)
- [SonarQube Documentation](https://docs.sonarqube.org/latest/)
- [Trivy Documentation](https://trivy.dev/docs/)
- [Maven POM Reference](https://maven.apache.org/pom.html)

---

## 🤝 Contributing

This is a learning project, but I welcome suggestions! If you find better ways to solve any of the challenges I faced, please open an issue or PR.

---

## 📧 Contact

- **GitHub:** [@Tejas1024](https://github.com/Tejas1024)
- **Project Link:** [Boardgame CI/CD Pipeline](https://github.com/Tejas1024/Boardgame)

---

## 🙏 Acknowledgments

Special thanks to:
- The DevOps community for excellent documentation
- StackOverflow for debugging help
- Claude AI for patient troubleshooting guidance

---

**⭐ If this helped you, please star this repo!**

**Note:** This documentation represents my actual learning journey, including all mistakes and corrections. Real DevOps is about learning from failures, not just showing successes.

# Part 2: CI/CD Pipeline Integration & Monitoring Setup Issues

## Issues Encountered During Pipeline Configuration & Monitoring

---

## Issue #06: DockerHub Username Confusion

**Problem:**  
Confusion about which DockerHub username to use when setting up Jenkins credentials.

**Initial Confusion:**
- Had DockerHub account with username `tejas0010`
- But saw profile showing `tejas1024`
- Uncertain which one to use for Jenkins credentials

**Root Cause:**  
DockerHub displays both username and Docker ID - needed to use the actual username for authentication.

**What Didn't Work:**
- Using email address instead of username
- Trying to use GitHub username

**What Worked:**
```bash
# Verified actual username from DockerHub profile (top-right corner)
Username: tejas0010

# Jenkins Credential Configuration:
Kind: Username with password
Username: tejas0010
Password: DockerHub account password
ID: docker-cred
Description: DockerHub Credentials
```

**Key Learning:**  
Always verify the exact username from the service's profile page, not assumptions based on other account names.

---

## Issue #07: Docker Image Push Access Denied

**Error Message:**
```
denied: requested access to the resource is denied
```

**Problem:**  
Docker push failing even though Docker login succeeded in Jenkins.

**Root Cause:**  
Mismatch between Docker login username and image tag username:
- Login: `tejas0010`
- Image tag: `tejas1024/boardgame:latest`

**Pipeline Configuration Issue:**
```groovy
environment {
    DOCKER_IMAGE = "tejas1024/boardgame"  // ❌ Wrong username
}
```

**What Didn't Work:**
- Trying to push to different username's repository
- Re-logging in multiple times
- Changing only Jenkins credentials

**What Worked:**
```groovy
environment {
    DOCKER_IMAGE = "tejas0010/boardgame"  // ✅ Correct username
}
```

Updated all references:
- Jenkins pipeline environment variable
- Kubernetes deployment YAML
- Verified consistency across all configs

**Key Learning:**  
Docker username must match exactly across:
- Docker login credentials
- Image naming (username/image:tag)
- Registry authentication
- Kubernetes deployment manifests

---

## Issue #08: AWS CLI Not Found During EKS Deployment

**Error Message:**
```
/var/lib/jenkins/workspace/boardgame-cicd-pipeline@tmp/durable-ceeeafc6/script.sh.copy: 2: aws: not found
```

**Problem:**  
Deploy to EKS stage failed because AWS CLI was not installed on Jenkins server.

**Root Cause:**  
Jenkins can only run commands that are installed on the machine. AWS CLI was missing.

**What Didn't Work:**
- Running pipeline without dependencies
- Assuming AWS CLI was pre-installed

**What Worked:**
```bash
# Install AWS CLI on Jenkins server
sudo apt update
sudo apt install -y unzip curl
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o awscliv2.zip
unzip awscliv2.zip
sudo ./aws/install

# Verify installation
aws --version

# Restart Jenkins
sudo systemctl restart jenkins
```

**Why Jenkins Restart Was Needed:**  
Jenkins loads tools/environment at startup, so restart was necessary for new PATH to take effect.

---

## Issue #09: kubectl Command Not Found

**Error Message:**
```
kubectl: not found
```

**Problem:**  
EKS deployment stage failed because kubectl was not installed on Jenkins server.

**What Didn't Work:**
```bash
# Using snap (suggested by system but didn't integrate with Jenkins properly)
sudo snap install kubectl
```

**What Worked:**
```bash
# Download kubectl binary
curl -LO "https://dl.k8s.io/release/$(curl -Ls https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

# Make executable and move to PATH
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Verify installation
kubectl version --client

# Restart Jenkins
sudo systemctl restart jenkins
```

**Key Learning:**  
CLI tools must be:
- Installed on Jenkins server (not just developer machine)
- Available in system PATH
- Accessible to Jenkins user

---

## Issue #10: Gmail SMTP Authentication Failures

**Multiple Error Messages Encountered:**

**Error 1 - Connection Refused:**
```
java.net.ConnectException: Connection refused
Couldn't connect to host, port: localhost, 25
```

**Error 2 - Authentication Required:**
```
530-5.7.0 Authentication Required
```

**Problem:**  
Jenkins email notifications completely failed despite correct Gmail credentials.

**Root Causes Identified:**

1. **localhost:25 error** - Default E-mail Notification not configured
2. **Authentication error** - Normal Gmail password not accepted

**What Didn't Work:**
- Using normal Gmail password
- Using email address as username
- Configuring only Extended Email Notification
- Assuming credentials would auto-populate

**What Finally Worked:**

**Step 1: Enable Google 2-Step Verification**
```
Google Account → Security → 2-Step Verification → Enable
```

**Step 2: Generate App Password**
```
Google Account → Security → App passwords
App: Mail
Device: Other (name it "Jenkins")
Copy 16-character password (no spaces)
```

**Step 3: Configure BOTH Email Sections in Jenkins**

**Extended E-mail Notification:**
```
SMTP server: smtp.gmail.com
SMTP Port: 465
Use SSL: ✅ Checked
Use TLS: ❌ Unchecked
Credentials: Select gmail-cred
```

**E-mail Notification (Critical - Often Missed):**
```
SMTP server: smtp.gmail.com
Default user e-mail suffix: @gmail.com

Advanced Settings:
✅ Use SMTP Authentication
✅ Use SSL
❌ Use TLS
SMTP Port: 465
Username: tejaspavithra2002@gmail.com
Password: [16-character App Password]
Reply-To Address: tejaspavithra2002@gmail.com
Charset: UTF-8
```

**Key Learnings:**
- Gmail blocks normal passwords for security
- App Password is mandatory for third-party apps
- BOTH email sections must be configured (not just Extended)
- Username/password must be manually entered in E-mail Notification section
- Credentials in Extended Email don't auto-apply to default Email Notification

---

## Issue #11: Jenkins Pipeline Not Showing Deploy to EKS Stage

**Problem:**  
EKS deployment stage existed in Jenkinsfile but didn't appear in Jenkins Stage View.

**Possible Causes:**
1. Jenkins reading old Jenkinsfile from SCM
2. Jenkinsfile not committed/pushed to GitHub
3. Pipeline configured as "Pipeline script" instead of "Pipeline script from SCM"
4. Syntax error in pipeline preventing stage rendering

**What Didn't Work:**
- Just editing Jenkinsfile locally
- Rebuilding without committing changes

**What Worked:**
```bash
# Verify changes are tracked
git status

# Commit and push changes
git add Jenkinsfile
git commit -m "Add Deploy to EKS stage"
git push origin main

# Verify Jenkins job configuration
Jenkins Job → Configure → Pipeline
Pipeline Definition: Pipeline script from SCM ✅
SCM: Git
Repository URL: https://github.com/Tejas1024/Boardgame.git
Branch: */main
Script Path: Jenkinsfile
```

Then clicked "Build Now"

---

## Issue #12: Deployment YAML File Not Found

**Error in Jenkins:**
```
kubectl apply -f deployment-service.yaml
error: the path "deployment-service.yaml" does not exist
```

**Root Cause:**  
Kubernetes manifest file was missing from repository root.

**What Worked:**

**Created deployment-service.yaml:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: boardgame
spec:
  replicas: 2
  selector:
    matchLabels:
      app: boardgame
  template:
    metadata:
      labels:
        app: boardgame
    spec:
      containers:
      - name: boardgame
        image: tejas0010/boardgame:latest
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: boardgame-service
spec:
  type: LoadBalancer
  selector:
    app: boardgame
  ports:
  - port: 80
    targetPort: 8080
```

**Committed to repository:**
```bash
git add deployment-service.yaml
git commit -m "Add Kubernetes deployment manifest"
git push origin main
```

---

## Issue #13: Prometheus YAML Indentation Error

**Error Message:**
```
Error loading config (--config.file=prometheus.yml)
err="parsing YAML file prometheus.yml: yaml: line 29: did not find expected key"
```

**Problem:**  
Prometheus refused to start due to YAML formatting issues.

**Root Cause:**  
- Incorrect indentation (YAML is whitespace-sensitive)
- Mixed tabs and spaces
- Job added outside `scrape_configs:` section

**What Didn't Work:**
- Copying YAML with inconsistent spacing
- Adding jobs at end of file randomly
- Using tabs instead of spaces

**What Worked:**
```yaml
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]
    static_configs:
      - targets:
        - http://a0d184c9ae570472cb136188d3cf77ff-2008120462.ap-south-1.elb.amazonaws.com
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: 13.201.93.244:9115
```

**Restarted Prometheus:**
```bash
pkill prometheus
./prometheus &
```

**Key Learnings:**
- YAML requires exact indentation (2 spaces per level)
- Never use tabs in YAML
- Jobs must be under `scrape_configs:`
- Validate YAML before restarting services

---

## Issue #14: Grafana Login Failed on First Access

**Error:**
```
Invalid username or password
```

**Problem:**  
Default admin/admin credentials didn't work on fresh Grafana installation.

**Root Cause:**  
Grafana sometimes auto-generates or secures the admin password on apt installation.

**What Didn't Work:**
- Trying admin/admin repeatedly
- Using system user password
- Using Jenkins password

**What Worked:**
```bash
# Reset admin password
sudo grafana-cli admin reset-admin-password admin

# Restart Grafana
sudo systemctl restart grafana-server

# Verify service status
sudo systemctl status grafana-server
```

**Access Grafana:**
```
URL: http://13.201.93.244:3000
Username: admin
Password: admin (or the password you set)
```

Grafana forced password change on first login.

---

## Issue #15: Grafana Dashboard Shows No Data

**Problem:**  
After importing Blackbox Exporter dashboard (ID: 7587), all panels were empty.

**Root Cause:**  
Prometheus was not added as a data source in Grafana.

**What Worked:**

**Step 1: Add Prometheus Data Source**
```
Grafana → Connections → Data sources → Add data source
Select: Prometheus
URL: http://localhost:9090
Click: Save & Test
```

**Expected Result:**
```
✅ Data source is working
```

**Step 2: Import Dashboard**
```
Dashboards → Import
Dashboard ID: 7587
Load → Select Prometheus → Import
```

**Dashboard Started Showing:**
- Target uptime status (UP/DOWN)
- HTTP response codes
- Latency metrics
- Response time graphs

---

## Issue #16: Blackbox Exporter Not Accessible

**Problem:**  
Prometheus targets showed blackbox as "DOWN".

**Root Cause:**  
Blackbox exporter was not running on port 9115.

**Verification:**
```bash
# Check if blackbox is running
ps aux | grep blackbox

# Check port
netstat -tulnp | grep 9115
```

**What Worked:**
```bash
cd ~/blackbox_exporter-0.24.0.linux-amd64
./blackbox_exporter &
```

**Verify in browser:**
```
http://13.201.93.244:9115
```

---

## Issue #17: AWS Resource Cleanup Challenges

**Problem:**  
Even after stopping EC2 instances, AWS costs continued to increase.

**Root Causes:**
- Stopped EC2 instances still charge for EBS storage
- NAT Gateway remained active (expensive)
- Elastic IPs not released
- Load Balancers not deleted
- EKS created hidden resources

**Elastic IP Release Error:**
```
Cannot be released with association IDs
Remove the association by using Actions > Disassociate Elastic IP
```

**What Didn't Work:**
- Directly releasing Elastic IP (blocked by NAT Gateway)
- Only stopping instances
- Assuming stopped = free

**What Worked - Complete Cleanup Order:**

**1. Delete EKS Cluster**
```
EKS → Clusters → Delete cluster
Wait 10-15 minutes
```

**2. Terminate EC2 Instances**
```
EC2 → Instances → Select all Stopped
Actions → Instance state → Terminate
```

**3. Delete NAT Gateways (Critical for Cost)**
```
VPC → NAT Gateways → Select → Delete
Wait 2-3 minutes
```

**4. Release Elastic IPs**
```
EC2 → Elastic IPs → Select unassociated → Release
```

**5. Delete Load Balancers**
```
EC2 → Load Balancers → Select → Delete
```

**6. Delete Target Groups**
```
EC2 → Target Groups → Select → Delete
```

**7. Clean Security Groups**
```
VPC → Security Groups → Delete unused
```

**8. Delete VPC (Optional)**
```
VPC → Your VPCs → Select project VPC → Delete
```

**Key Learnings:**
- **Stopped ≠ Terminated** - Stopped instances still cost money
- NAT Gateway is one of the most expensive forgotten resources
- Elastic IPs can't be released while attached
- Always check Load Balancers after EKS deletion
- EKS cleanup doesn't delete all resources automatically
- Follow cleanup order: EKS → Compute → Network → VPC

---

## Better Approaches Discovered

### 1. **Use Infrastructure as Code (Terraform/CloudFormation)**
Would have prevented orphaned resources and made cleanup easier with `terraform destroy`.

### 2. **Pipeline Validation Before Execution**
```groovy
stage('Validate Config') {
    steps {
        sh 'kubectl apply --dry-run=client -f deployment-service.yaml'
    }
}
```

### 3. **Credentials Management**
Better approach: Use AWS Secrets Manager or Jenkins Credentials Plugin from the start.

### 4. **Monitoring Setup Earlier**
Should have set up Prometheus/Grafana before deployment issues occurred.

### 5. **Cost Alerts**
AWS Budgets should have been configured before resource creation.

---

## Final Working Configuration Summary

### Jenkins Pipeline Environment
```groovy
environment {
    DOCKER_IMAGE = "tejas0010/boardgame"
    DOCKER_TAG = "latest"
    AWS_REGION = "ap-south-1"
    CLUSTER_NAME = "boardgame-cluster"
    EMAIL_TO = "tejaspavithra2002@gmail.com"
}
```

### Required Jenkins Credentials
- `docker-cred` - DockerHub (username + password)
- `aws-cred` - AWS IAM (Access Key + Secret)
- `gmail-cred` - Gmail (email + app password)

### Required Tools on Jenkins Server
```bash
# Java, Maven (via Jenkins tools)
# Docker
# AWS CLI v2
# kubectl v1.29
# trivy
```

### Prometheus Configuration
```yaml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]
    static_configs:
      - targets:
        - http://[LOAD_BALANCER_URL]
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: localhost:9115
```

### Grafana Data Source
```
Type: Prometheus
URL: http://localhost:9090
Access: Server (default)
```

---

## Project Outcomes

### ✅ Successfully Implemented
- Complete CI/CD pipeline with Jenkins
- Docker image build and push to DockerHub
- Kubernetes deployment on AWS EKS
- Infrastructure monitoring with Prometheus
- Visualization with Grafana
- Email notifications on build status
- Security scanning with Trivy

### 🎓 Skills Developed
- Debugging YAML configuration errors
- AWS resource management and cost optimization
- Docker registry authentication
- Kubernetes manifest creation
- Monitoring stack setup (Prometheus + Grafana)
- SMTP configuration with Gmail
- Pipeline troubleshooting
- Cloud resource cleanup

### 📊 Metrics Achieved
- Pipeline execution time: ~8-12 minutes
- Successful builds: 32+
- Docker images pushed: 32 versions
- Application uptime monitoring: Active
- Email alerts: Working
- Resource cleanup: Complete (no ongoing costs)

---

 
---

## Useful Resources That Helped

- Jenkins Pipeline Syntax Reference
- Docker Hub Authentication Documentation
- AWS EKS Getting Started Guide
- Prometheus Configuration Documentation
- Grafana Dashboard Library (Dashboard ID 7587)
- Gmail App Password Generation Guide
- kubectl Installation Guide
- YAML Linting Tools

---

## Conclusion

This project provided hands-on experience with real DevOps challenges. The issues encountered and resolved demonstrate practical problem-solving skills that are valuable in production environments. Every error was an opportunity to learn proper configuration, debugging techniques, and best practices in cloud-native application deployment and monitoring.

The complete pipeline now successfully automates the entire software delivery process from code commit to production deployment with monitoring and alerting.
