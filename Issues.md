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
