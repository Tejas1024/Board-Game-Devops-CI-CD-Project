# 🎉 Complete DevOps CI/CD Project - Board Game Application

## 🏆 PROJECT COMPLETED - ALL 25 PHASES! 🏆

**Production-ready CI/CD pipeline built from scratch by a complete beginner!**

---

## 📋 Project Overview

**Achievement:** Built end-to-end DevOps CI/CD pipeline for Board Game Database application

**Technology Stack:**
- ✅ **Infrastructure**: AWS EC2, Terraform
- ✅ **Configuration Management**: Ansible
- ✅ **CI/CD**: Jenkins Pipeline (13 stages)
- ✅ **Code Quality**: SonarQube
- ✅ **Security**: Trivy (FS + Image scanning)
- ✅ **Build & Package**: Maven
- ✅ **Artifact Repository**: Nexus Repository Manager
- ✅ **Containerization**: Docker + DockerHub
- ✅ **Orchestration**: Kubernetes (AWS EKS)
- ✅ **Monitoring**: Prometheus + Grafana + Blackbox Exporter
- ✅ **Notifications**: Email alerts
- ✅ **Automation**: GitHub Webhooks

---

## ✅ ALL COMPLETED PHASES (1-25)

### Infrastructure & Setup (Phases 1-6)

**Phase 1: Terraform Infrastructure** ✅
- Automated AWS EC2 provisioning
- SSH key pair generation
- 2 instances with 20GB storage

**Phase 2: Ansible Configuration** ✅
- Automated Docker installation
- Multi-server management
- Inventory-based deployment

**Phase 3: SonarQube Setup** ✅
- Code quality analysis server
- Docker containerized deployment
- Port 9000 exposure

**Phase 4: Nexus Repository** ✅
- Artifact repository manager
- Docker containerized
- maven-releases & maven-snapshots configured

**Phase 5: Jenkins Installation** ✅
- CI/CD automation server
- Java 17 + Jenkins scripted install
- Auto-start on boot

**Phase 6: Jenkins Tools Configuration** ✅
- Maven Integration plugin
- Eclipse Temurin (JDK 17)
- SonarQube Scanner
- Tool auto-installation

---

### Pipeline Development (Phases 7-11)

**Phase 7: First Pipeline** ✅
- 3-stage pipeline: Clone → Compile → Test
- GitHub integration
- Maven build automation

**Phase 8: Code Quality Integration** ✅
- SonarQube analysis stage
- Code smell detection
- Quality gates

**Phase 9: Security Scanning** ✅
- Trivy filesystem scan
- HTML report generation
- Vulnerability detection

**Phase 10: Application Packaging** ✅
- Maven package stage
- JAR artifact creation
- Build output in target/

**Phase 11: Nexus Deployment** ✅
- Artifact upload to Nexus
- SNAPSHOT version management
- distributionManagement configuration

---

### Containerization (Phases 14-16)

**Phase 14: Docker Image Build** ✅
- Dockerfile creation
- Docker image build stage
- Multi-tag strategy (latest + build number)
- Image: tejas1024/boardgame:latest

**Phase 15: Docker Image Security** ✅
- Trivy image scan
- Container vulnerability detection
- HTML security report

**Phase 16: DockerHub Push** ✅
- Image push to registry
- Version tagging
- Public image availability

---

### Kubernetes Deployment (Phases 17-20)

**Phase 17-18: EKS Cluster Setup** ✅
- AWS EKS cluster creation
- 2-node cluster (t2.medium)
- IAM OIDC provider
- Node group configuration
- kubectl/eksctl installation

**Phase 19: Kubernetes Jenkins Integration** ✅
- kubectl plugin in Jenkins
- AWS CLI on Jenkins
- kubeconfig setup
- Security group port 6443

**Phase 20: Application Deployment** ✅
- deployment-service.yaml created
- 2 replica pods
- LoadBalancer service
- Application accessible via ELB
- Auto-scaling configured

---

### Automation & Monitoring (Phases 21-25)

**Phase 21: Notifications & Webhooks** ✅
- Email notifications (success/failure)
- GitHub webhook trigger
- Auto-build on code push
- Extended email plugin

**Phase 22: Prometheus Setup** ✅
- Prometheus server installation
- Metrics collection on port 9090
- Time-series database

**Phase 23: Blackbox Exporter** ✅
- HTTP endpoint monitoring
- Application health checks
- Port 9115 exposure

**Phase 24: Grafana Dashboards** ✅
- Grafana installation (port 3000)
- Prometheus data source
- Blackbox exporter dashboard (ID: 7587)
- Real-time application monitoring

**Phase 25: Project Completion** ✅
- All 13 pipeline stages working
- Complete documentation
- Production-ready pipeline

---

## 📸 Project Screenshots

### Phase 19: EKS Setup
![AWS CLI Config](screenshots/phase19-aws-cli-config.png)
*AWS CLI configured for EKS management*
 

### Phase 20: Kubernetes Deployment
![Deployment YAML](screenshots/phase20-deployment-yaml.png)
*Kubernetes deployment and service configuration*

![Pipeline EKS](screenshots/phase20-pipeline-eks-stage.png)
*Pipeline with EKS deployment stage*

![Stage View Success](screenshots/phase20-stage-view-success.png)
*All stages including EKS deployment successful*

![App Running](screenshots/phase20-app-running.png)
*Board Game application live on EKS*

### Phase 21: Automation
![GitHub Webhook](screenshots/phase21-github-webhook.png)
*GitHub webhook for auto-triggering builds*

### Phase 22-24: Monitoring
![Prometheus](screenshots/phase22-prometheus.png)
*Prometheus metrics collection*

![Grafana Login](screenshots/phase22-grafana-login.png)
*Grafana monitoring interface*

![Prometheus Targets](screenshots/phase24-prometheus-targets.png)
*Prometheus monitoring application endpoints*

![Grafana Dashboard](screenshots/phase24-grafana-dashboard.png)
*Real-time application metrics in Grafana*

### Phase 25: Complete Pipeline
![Complete Pipeline](screenshots/phase25-complete-pipeline.png)
*All 13 stages of production pipeline*

---

## 🏗️ Complete Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      GITHUB REPOSITORY                           │
│               (Source Code + Kubernetes Manifests)               │
└─────────────────────────────────────────────────────────────────┘
                              ↓
                    ┌──────────────────┐
                    │  GitHub Webhook  │
                    │  (Auto-trigger)  │
                    └──────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│                   JENKINS CI/CD PIPELINE                         │
│                                                                   │
│  Stage 1:  Clone GitHub Repository                               │
│  Stage 2:  Compile Source Code (Maven)                           │
│  Stage 3:  Run Unit Tests (JUnit)                                │
│  Stage 4:  SonarQube Code Quality Analysis                       │
│  Stage 5:  Trivy File System Security Scan                       │
│  Stage 6:  Publish Trivy FS Report (HTML)                        │
│  Stage 7:  Package Application (JAR)                             │
│  Stage 8:  Deploy Artifact to Nexus Repository                   │
│  Stage 9:  Build Docker Image (tejas1024/boardgame)              │
│  Stage 10: Trivy Docker Image Security Scan                      │
│  Stage 11: Publish Trivy Image Report (HTML)                     │
│  Stage 12: Push Docker Image to DockerHub                        │
│  Stage 13: Deploy to AWS EKS Kubernetes Cluster                  │
│                                                                   │
│  Post Actions: Email notifications, Workspace cleanup            │
└─────────────────────────────────────────────────────────────────┘
         ↓                    ↓                    ↓
    ┌─────────┐        ┌──────────┐        ┌──────────────┐
    │ NEXUS   │        │ DOCKERHUB│        │   AWS EKS    │
    │Repository│        │ Registry │        │   CLUSTER    │
    │         │        │          │        │              │
    │ maven-  │        │ tejas1024│        │ ┌──────────┐ │
    │snapshots│        │/boardgame│        │ │  Pod 1   │ │
    │         │        │ :latest  │        │ │ :8080    │ │
    │ JAR     │        │ :v1, :v2 │        │ └──────────┘ │
    │ Files   │        │ :v3, ... │        │ ┌──────────┐ │
    └─────────┘        └──────────┘        │ │  Pod 2   │ │
                                            │ │ :8080    │ │
                                            │ └──────────┘ │
                                            │      ↓       │
                                            │ ┌──────────┐ │
                                            │ │   LB     │ │
                                            │ │Service   │ │
                                            │ │  :80     │ │
                                            │ └──────────┘ │
                                            └──────────────┘
                                                   ↓
                                       ┌────────────────────┐
                                       │  PUBLIC INTERNET   │
                                       │  Load Balancer URL │
                                       │  Application Live! │
                                       └────────────────────┘
                                                   ↓
                                       ┌────────────────────┐
                                       │   MONITORING       │
                                       │                    │
                                       │  Prometheus :9090  │
                                       │  Blackbox   :9115  │
                                       │  Grafana    :3000  │
                                       │                    │
                                       │  Real-time metrics │
                                       │  Health checks     │
                                       │  Alerting          │
                                       └────────────────────┘
```

---

## 💻 Complete Jenkins Pipeline Code

```groovy
pipeline {
    agent any

    tools {
        jdk 'jdk-17'
        maven 'maven-3'
    }

    environment {
        DOCKER_IMAGE = "tejas0010/boardgame"
        DOCKER_TAG   = "latest"
        AWS_REGION   = "ap-south-1"
        CLUSTER_NAME = "boardgame-cluster"
        EMAIL_TO     = "tejaspavithra2002@gmail.com"
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
                    trivy fs --format template --template @$HOME/html.tpl --output trivy-fs-report.html .
                '''
            }
        }

        stage('Publish Trivy FS Report') {
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
                    maven: 'maven-3',
                    traceability: true
                ) {
                    sh 'mvn deploy'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh """
                            docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                            docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:v${BUILD_NUMBER}
                        """
                    }
                }
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh """
                    trivy image --format table --output trivy-image-report.txt ${DOCKER_IMAGE}:${DOCKER_TAG}
                    trivy image --format template --template @\$HOME/html.tpl --output trivy-image-report.html ${DOCKER_IMAGE}:${DOCKER_TAG}
                """
            }
        }

        stage('Publish Trivy Image Report') {
            steps {
                publishHTML(target: [
                    reportDir: '.',
                    reportFiles: 'trivy-image-report.html',
                    reportName: 'Trivy Image Scan Report',
                    keepAll: true,
                    alwaysLinkToLastBuild: true,
                    allowMissing: false
                ])
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh """
                            docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                            docker push ${DOCKER_IMAGE}:v${BUILD_NUMBER}
                        """
                    }
                }
            }
        }

        stage('Deploy to EKS') {
            steps {
                script {
                    withAWS(credentials: 'aws-cred', region: "${AWS_REGION}") {
                        sh """
                            aws eks update-kubeconfig --region ${AWS_REGION} --name ${CLUSTER_NAME}
                            kubectl apply -f deployment-service.yaml
                            kubectl get pods
                            kubectl get svc
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }

        success {
            echo 'Pipeline completed successfully!'
            mail to: "${EMAIL_TO}",
                 subject: "✅ SUCCESS: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                 body: """
Hello Tejas,

✅ Your Jenkins pipeline completed successfully!

Job Name  : ${env.JOB_NAME}
Build No  : ${env.BUILD_NUMBER}

🔗 Build URL:
${env.BUILD_URL}

🎉 Docker Image pushed and application deployed to EKS.

Regards,
Jenkins CI/CD
"""
        }

        failure {
            echo 'Pipeline failed. Check logs for details.'
            mail to: "${EMAIL_TO}",
                 subject: "❌ FAILURE: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                 body: """
Hello Tejas,

❌ Your Jenkins pipeline FAILED.

Job Name  : ${env.JOB_NAME}
Build No  : ${env.BUILD_NUMBER}

🔍 Check the console logs here:
${env.BUILD_URL}console

Please review and fix the issue.

Regards,
Jenkins CI/CD
"""
        }
    }
}

```

---

## 🎯 Complete Project Statistics

**Infrastructure:**
- AWS EC2 Instances: 5 (Jenkins, SonarQube, Nexus, Ansible, EKS Control, Monitoring)
- EKS Cluster Nodes: 2 (t2.medium)
- Total Compute: 7 instances
- Region: ap-south-1 (Mumbai)

**Pipeline Metrics:**
- Total Stages: 13
- Automated Tests: Unit tests + Security scans
- Build Time: 8-12 minutes (full pipeline)
- Docker Images: tejas1024/boardgame (multiple versions)
- Deployment: Kubernetes with 2 replicas

**Tools Integrated:**
- Source Control: Git + GitHub
- CI/CD: Jenkins
- Code Quality: SonarQube
- Security: Trivy (2 scans)
- Artifact Mgmt: Nexus
- Containerization: Docker
- Registry: DockerHub
- Orchestration: Kubernetes (EKS)
- Monitoring: Prometheus + Grafana + Blackbox
- Notifications: Email

---

## 🎓 Complete Learning Journey

### Phase 1-4: Foundation (16%)
**Learned:**
- Infrastructure as Code (Terraform)
- Configuration Management (Ansible)
- Code Quality tools (SonarQube)
- Artifact repositories (Nexus)

### Phase 5-11: CI Pipeline (44%)
**Learned:**
- Jenkins setup and configuration
- Pipeline as Code
- Maven build lifecycle
- Security scanning
- Artifact versioning

### Phase 12-16: Containerization (64%)
**Learned:**
- Docker image creation
- Container security
- Registry management
- Multi-stage builds

### Phase 17-20: Kubernetes (80%)
**Learned:**
- EKS cluster management
- kubectl/eksctl
- Kubernetes deployments
- Services and LoadBalancers
- Pod management

### Phase 21-25: Production (100%)
**Learned:**
- GitHub webhooks
- Email notifications
- Prometheus monitoring
- Grafana dashboards
- Production best practices

---

## 💡 Key Achievements

✅ **Automation:** Fully automated CI/CD pipeline  
✅ **Security:** Dual security scans (FS + Image)  
✅ **Quality:** Code quality gates integrated  
✅ **Scalability:** Kubernetes with 2 replicas  
✅ **Monitoring:** Real-time application metrics  
✅ **Notifications:** Auto-alerts on build status  
✅ **Containerization:** Docker-based deployment  
✅ **Cloud Native:** AWS EKS production deployment  
✅ **Version Control:** Git + artifact versioning  
✅ **Observability:** Prometheus + Grafana stack  

---

## 🏆 Production-Ready Features

**High Availability:**
- 2 pod replicas in Kubernetes
- LoadBalancer service
- Auto-scaling capable

**Security:**
- File system vulnerability scanning
- Docker image security checks
- AWS IAM roles and policies
- Secure credential management

**Monitoring:**
- Prometheus metrics collection
- Grafana visualization
- Blackbox exporter health checks
- Real-time alerting capability

**Automation:**
- GitHub webhook triggers
- Email notifications
- Automated testing
- Automated deployment

**Reliability:**
- Automated rollback capability
- Version tracking
- Artifact history in Nexus
- Clean workspace after builds

---

## 📚 Technologies Mastered

**Infrastructure & Cloud:**
- AWS EC2, EKS, IAM, Security Groups
- Terraform (Infrastructure as Code)

**Configuration Management:**
- Ansible playbooks
- Inventory management

**CI/CD:**
- Jenkins declarative pipelines
- Pipeline as Code
- Multi-stage pipelines

**Build Tools:**
- Maven (compile, test, package, deploy)
- Java 17 (OpenJDK)

**Quality & Security:**
- SonarQube (code quality)
- Trivy (security scanning)

**Containerization:**
- Docker (build, tag, push)
- DockerHub registry

**Orchestration:**
- Kubernetes (deployments, services)
- AWS EKS
- kubectl, eksctl

**Artifact Management:**
- Nexus Repository Manager
- SNAPSHOT/RELEASE versioning

**Monitoring:**
- Prometheus (metrics)
- Grafana (visualization)
- Blackbox Exporter (HTTP monitoring)

**Version Control:**
- Git
- GitHub
- Webhooks

---

## 🎯 Real-World Applications

This project demonstrates skills for:
- **DevOps Engineer** roles
- **Site Reliability Engineer** positions
- **Cloud Engineer** opportunities
- **CI/CD Specialist** roles
- **Kubernetes Administrator** positions

**Companies using similar stacks:**
- Tech giants (Google, Amazon, Microsoft)
- Startups with cloud-native apps
- Financial institutions
- E-commerce platforms
- SaaS companies

---

## 📊 Project Timeline

**Phase 1-6:** Infrastructure & Setup (Days 1-3)  
**Phase 7-11:** CI Pipeline Development (Days 4-6)  
**Phase 12-16:** Containerization (Days 7-9)  
**Phase 17-20:** Kubernetes Deployment (Days 10-12)  
**Phase 21-25:** Monitoring & Completion (Days 13-15)  

**Total Duration:** 15 days (as a beginner)  
**Final Result:** Production-ready DevOps pipeline!  

---

## 🔧 Active Components

**Jenkins Pipeline:**
- Job: boardgame-cicd-pipeline
- Stages: 13
- Status: Production-ready
- Build triggers: GitHub webhook + manual

**Kubernetes Cluster:**
- Name: boardgame-cluster
- Region: ap-south-1
- Nodes: 2 x t2.medium
- Pods: 2 replicas
- Service: LoadBalancer
- Status: Running

**Docker Images:**
- Registry: DockerHub
- Image: tejas1024/boardgame
- Tags: latest, v1, v2, v3, ...
- Visibility: Public

**Monitoring Stack:**
- Prometheus: :9090
- Grafana: :3000
- Blackbox: :9115
- Status: Active

---

## 🎓 What I Learned as a Complete Beginner

### Technical Skills:
1. ✅ Linux command line proficiency
2. ✅ AWS cloud services
3. ✅ Infrastructure as Code
4. ✅ CI/CD pipeline design
5. ✅ Container technology
6. ✅ Kubernetes orchestration
7. ✅ Monitoring and observability
8. ✅ Security best practices
9. ✅ Version control systems
10. ✅ Automation scripting

### Soft Skills:
1. ✅ Problem-solving
2. ✅ Documentation
3. ✅ Troubleshooting
4. ✅ Project management
5. ✅ Persistence and patience

### Industry Practices:
1. ✅ DevOps methodology
2. ✅ Agile principles
3. ✅ Continuous Integration
4. ✅ Continuous Deployment
5. ✅ Infrastructure automation
6. ✅ Security-first approach
7. ✅ Monitoring and alerting
8. ✅ Documentation standards

---

## 🚀 How to Use This Project

### For Learning:
1. Follow phases sequentially
2. Understand each component
3. Troubleshoot issues
4. Document your journey

### For Portfolio:
1. Showcase on GitHub
2. Add to LinkedIn projects
3. Include in resume
4. Discuss in interviews

### For Production:
1. Modify for your application
2. Adjust resource sizes
3. Configure domain names
4. Set up SSL/TLS
5. Implement backup strategies

---

## 🎉 Project Completion Certificate

**This project demonstrates:**
- ✅ Complete DevOps pipeline implementation
- ✅ AWS cloud expertise
- ✅ Kubernetes proficiency
- ✅ Security-first mindset
- ✅ Monitoring and observability
- ✅ Production-ready deployment
- ✅ Industry best practices

**Completed by:** [Your Name]  
**Date:** [Completion Date]  
**Duration:** 15 days  
**Phases Completed:** 25/25 (100%)  

---

## 🙏 Acknowledgments

**Resources:**
- AWS Documentation
- Kubernetes Documentation
- Jenkins Documentation
- Docker Documentation
- Prometheus Documentation
- Grafana Documentation
- DevOps Community

**Tools:**
- Open Source Community
- GitHub
- DockerHub
- Cloud Providers

---

## 📞 Contact & Links

**GitHub Repository:** https://github.com/Tejas1024/Boardgame  
**DockerHub Image:** https://hub.docker.com/r/tejas1024/boardgame  
**LinkedIn:** [Your LinkedIn]  
**Portfolio:** [Your Portfolio]  

---

## 🎯 Next Steps & Improvements

**Potential Enhancements:**
1. Add Helm charts for Kubernetes
2. Implement GitOps with ArgoCD
3. Add Istio service mesh
4. Implement blue-green deployments
5. Add Vault for secrets management
6. Implement multi-region deployment
7. Add automated testing (Selenium)
8. Implement chaos engineering
9. Add cost optimization
10. Implement disaster recovery

---

 
**Project Status:** ✅ COMPLETE - 100% (25/25 phases)  
**Pipeline Status:** ✅ Production-ready  
**Application Status:** ✅ Live on Kubernetes  
**Monitoring Status:** ✅ Active  

---

*From zero to DevOps hero - One phase at a time!* 🚀  
*Thank you for following this journey!* 🎉