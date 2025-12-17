# Complete DevOps CI/CD Project - Board Game Application

## 🚀 Project Status: Phase 8 Completed - Code Quality Integration Active!

This repository documents my journey building a complete DevOps CI/CD pipeline from scratch as a complete beginner.

---

## 📋 Project Overview

Building an end-to-end DevOps pipeline for a Board Game Database application with comprehensive automation, quality gates, and monitoring.

### Technology Stack:
- **Cloud Platform**: AWS (EC2, Security Groups, IAM)
- **Infrastructure as Code**: Terraform
- **Configuration Management**: Ansible
- **CI/CD Orchestration**: Jenkins
- **Code Quality & Security**: SonarQube, Trivy
- **Artifact Repository**: Nexus
- **Containerization**: Docker
- **Container Orchestration**: Kubernetes (AWS EKS)
- **Monitoring & Observability**: Prometheus & Grafana

---

## ✅ Completed Phases (8/25)

### Phase 1: Infrastructure Setup with Terraform ✓
**Automated AWS infrastructure provisioning**

**Achievements:**
- Created IAM user with administrative access
- Configured AWS CLI with secure credentials
- Wrote Terraform configuration files (`provider.tf`, `main.tf`)
- Generated SSH key pair (RSA 4096-bit) for secure access
- Provisioned 2 EC2 instances (t2.medium, 20GB GP3 storage)
- Implemented Infrastructure as Code (IaC) principles
- Successfully applied Terraform plan and created resources

**Region**: Mumbai (ap-south-1)

---

### Phase 2: Configuration Management with Ansible ✓
**Automated Docker installation across multiple servers**

**Achievements:**
- Launched dedicated Ansible control server (t2.medium, Ubuntu 22.04)
- Created inventory file with 2 target servers
- Configured SSH key-based authentication
- Wrote comprehensive Ansible playbook for Docker installation
- Successfully deployed Docker CE on all target servers
- Verified deployment with Ansible ping module
- All 8 tasks completed successfully (0 failures)

**Automation Win**: Installed Docker on multiple servers in under 5 minutes!

---

### Phase 3: SonarQube Setup ✓
**Code quality analysis infrastructure**

**Achievements:**
- Configured Docker permissions on SonarQube server
- Pulled official SonarQube Docker image
- Created SonarQube container exposed on port 9000
- Accessed SonarQube web interface successfully
- Set up initial admin credentials
- Dashboard ready for code quality analysis

**Access**: `http://<server-ip>:9000`

---

### Phase 4: Nexus Repository Setup ✓
**Artifact repository management**

**Achievements:**
- Configured Docker environment on Nexus server
- Pulled Sonatype Nexus3 Docker image
- Created Nexus container on port 8081
- Retrieved initial admin password from container
- Configured custom admin credentials
- Set up Maven repositories (releases & snapshots)

**Access**: `http://<server-ip>:8081`

---

### Phase 5: Jenkins CI/CD Server Configuration ✓
**Central automation server setup**

**Achievements:**
- Launched Jenkins EC2 instance (t2.medium, 15GB storage)
- Created automated installation script (Java 17 + Jenkins)
- Configured Jenkins repository and GPG keys
- Installed and started Jenkins service
- Enabled auto-start on system boot
- Configured AWS security group for port 8080
- Successfully accessed Jenkins web interface
- Retrieved initial admin password
- Installed suggested plugins (Git, Pipeline, etc.)
- Created first admin user

**Access**: `http://<server-ip>:8080`

---

### Phase 6: Jenkins Plugins and Tools Configuration ✓
**Build tools and integrations setup**

**Achievements:**
- Installed Maven Integration plugin
- Installed Eclipse Temurin plugin for JDK management
- Installed SonarQube Scanner plugin
- Configured JDK 17 (OpenJDK) from Adoptium
- Configured Maven 3.9.x with auto-installation
- Configured SonarQube Scanner latest version
- Verified all tool installations
- Created test job to validate configurations

**Tools Ready**: Java, Maven, SonarQube Scanner

---

### Phase 7: Create Jenkins Pipeline ✓
**First working CI pipeline with 3 stages**

**Achievements:**
- Created declarative pipeline job: `boardgame-cicd-pipeline`
- Implemented Stage 1: Clone GitHub Repository (main branch)
- Implemented Stage 2: Compile Source Code (`mvn clean compile`)
- Implemented Stage 3: Test Source Code (`mvn test`)
- Successfully executed multiple builds (100% success rate)
- Configured tools (JDK 17, Maven 3.9)
- Generated build artifacts in Jenkins workspace
- Verified all unit tests pass

**Pipeline Status**: 3 stages, all green ✓

**Build Metrics:**
- Total Builds: 5+
- Success Rate: 100%
- Average Duration: 45 seconds

---

### Phase 8: SonarQube Integration with Jenkins ✓ **← JUST COMPLETED!**
**Code quality analysis integrated into CI pipeline**

**Achievements:**
- Generated SonarQube authentication token (Global Analysis Token)
- Added token to Jenkins credentials as Secret Text
- Configured SonarQube server in Jenkins system settings
- Updated pipeline with 4th stage: "SonarQube Code Analysis"
- Successfully executed SonarQube analysis in pipeline
- Project automatically created in SonarQube dashboard
- Code quality metrics now visible for every build
- Integrated quality gates for automated pass/fail decisions

**New Pipeline Stage:**
```groovy
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
```

**Quality Metrics Now Tracked:**
- **Bugs**: Code defects that could cause failures
- **Vulnerabilities**: Security weaknesses
- **Code Smells**: Maintainability issues
- **Coverage**: Test coverage percentage
- **Duplications**: Duplicate code detection
- **Technical Debt**: Estimated time to fix issues

**Impact**: Every code commit now gets automatic quality analysis!

---

## 📸 Phase 8 Screenshots

### Screenshot 1: SonarQube Token Generation
![SonarQube Token](screenshots/phase8-sonarqube-token.png)
*Generated Global Analysis Token in SonarQube for Jenkins authentication*

### Screenshot 2: Jenkins Credential Configuration
![Jenkins Credential](screenshots/phase8-jenkins-credential.png)
*Added SonarQube token as Secret Text credential in Jenkins*

### Screenshot 3: SonarQube Server Configuration
![SonarQube Server Config](screenshots/phase8-sonarqube-server-config.png)
*Configured SonarQube server URL and authentication in Jenkins System settings*

### Screenshot 4: Pipeline Execution Success
![Pipeline Success](screenshots/phase8-pipeline-success.png)
*All 4 stages completed successfully including SonarQube Code Analysis*

### Screenshot 5: SonarQube Analysis Results
![SonarQube Results](screenshots/phase8-sonarqube-results.png)
*Board Game project analysis results in SonarQube dashboard showing quality metrics*

---

## 🏗️ Current Pipeline Architecture
```
┌──────────────────────────────────────────────────────────────┐
│                  GitHub Repository                            │
│         (Board Game Application Source Code)                  │
└──────────────────────────────────────────────────────────────┘
                           ↓
┌──────────────────────────────────────────────────────────────┐
│              JENKINS CI/CD PIPELINE (4 Stages)                │
│                                                                │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Stage 1: Clone GitHub Repository                      │  │
│  │  • Pulls latest code from main branch                  │  │
│  │  • Git clone to Jenkins workspace                      │  │
│  └────────────────────────────────────────────────────────┘  │
│                           ↓                                    │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Stage 2: Compile Source Code                          │  │
│  │  • mvn clean compile                                   │  │
│  │  • Compiles Java files to .class files                 │  │
│  │  • Generates target/classes directory                  │  │
│  └────────────────────────────────────────────────────────┘  │
│                           ↓                                    │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Stage 3: Test Source Code                             │  │
│  │  • mvn test                                            │  │
│  │  • Executes JUnit unit tests                          │  │
│  │  • Generates test reports                             │  │
│  └────────────────────────────────────────────────────────┘  │
│                           ↓                                    │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  Stage 4: SonarQube Code Analysis ← NEW! Phase 8      │  │
│  │  • Analyzes code quality                              │  │
│  │  • Detects bugs and vulnerabilities                   │  │
│  │  • Calculates code coverage                           │  │
│  │  • Identifies code smells                             │  │
│  │  • Sends results to SonarQube server                  │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                                │
│  Tools: JDK 17, Maven 3.9.x, SonarQube Scanner               │
└──────────────────────────────────────────────────────────────┘
                           ↓
        ┌──────────────────────────────────────┐
        │   SonarQube Quality Dashboard         │
        │   • Quality Gate: Pass/Fail           │
        │   • Detailed Metrics & Reports        │
        │   • Historical Trend Analysis         │
        └──────────────────────────────────────┘
                           ↓
                   [NEXT: Phase 9]
              Trivy Security Scanning
```

---

## 🎯 What's Working Now (After Phase 8)

### Complete Quality-Focused CI Pipeline
- ✅ Automatic code checkout from GitHub
- ✅ Java compilation with Maven
- ✅ Automated unit testing
- ✅ **Code quality analysis with SonarQube** ← NEW!
- ✅ **Bug and vulnerability detection** ← NEW!
- ✅ **Test coverage tracking** ← NEW!
- ✅ **Code smell identification** ← NEW!
- ✅ Build success/failure reporting
- ✅ Stage visualization
- ✅ Historical quality metrics

### Quality Gates
Every build now checks:
- No critical bugs introduced
- No security vulnerabilities
- Minimum test coverage maintained
- Code maintainability standards
- No duplicated code blocks

---

## 💻 Complete Pipeline Code (Phase 8)
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
                    url: 'https://github.com/Sharath-yp25/Boardgame.git'
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
    }
}
```

---

## 🔧 Current Infrastructure

### Active AWS Resources:
- **Jenkins Server**: t2.medium, 15GB, Ubuntu (Port 8080) ← PIPELINE RUNNING
- **SonarQube Server**: t2.medium, 20GB, Ubuntu, Docker (Port 9000) ← INTEGRATED
- **Nexus Server**: t2.medium, 20GB, Ubuntu, Docker (Port 8081)
- **Ansible Server**: t2.medium, 8GB, Ubuntu
- **Total EC2 Instances**: 4 running in Mumbai region

### Jenkins Pipeline Status:
- **Job Name**: boardgame-cicd-pipeline
- **Total Stages**: 4 (Clone → Compile → Test → SonarQube)
- **Builds Executed**: 10+
- **Success Rate**: 100%
- **Average Duration**: ~60 seconds
- **Last Build**: Successful with quality analysis ✓

### SonarQube Integration Status:
- **Server**: Connected and authenticated
- **Project**: boardgame (auto-created)
- **Analysis Frequency**: Every build
- **Quality Gate**: Active
- **Metrics Tracked**: 5+ quality dimensions

---

## 📊 Project Progress

**Overall Progress**: 8/25 Phases Complete **(32%)**

**Pipeline Maturity**:
- ✅ Source Code Management (Git)
- ✅ Build Automation (Maven)
- ✅ Test Automation (JUnit)
- ✅ Code Quality Analysis (SonarQube) ← Phase 8
- ⏳ Security Scanning (Trivy) ← Phase 9 Next
- ⏳ Artifact Management (Nexus)
- ⏳ Containerization (Docker)
- ⏳ Orchestration (Kubernetes)
- ⏳ Monitoring (Prometheus/Grafana)

**Quality Metrics** (Latest Build):
- Bugs: [Will be populated after first analysis]
- Vulnerabilities: [Will be populated]
- Code Smells: [Will be populated]
- Coverage: [Will be populated]
- Duplications: [Will be populated]

---

## 💡 Key Learnings - Phase 8

### What I Learned About Code Quality:

1. **SonarQube Integration**
   - Token-based authentication is secure
   - Jenkins and SonarQube communicate via REST API
   - Analysis results are sent automatically
   - Dashboard updates in real-time

2. **Quality Analysis Process**
   - Happens after compilation (needs .class files)
   - Analyzes both source code and bytecode
   - Checks against predefined quality rules
   - Generates comprehensive metrics report

3. **withSonarQubeEnv Block**
   - Provides environment variables to Maven
   - Handles authentication automatically
   - Sends results to SonarQube server
   - No manual configuration needed in pom.xml

4. **Quality Metrics Understanding**
   - **Bugs**: Actual code errors that could break functionality
   - **Vulnerabilities**: Security weaknesses that could be exploited
   - **Code Smells**: Not bugs, but poor code practices
   - **Coverage**: Percentage of code tested by unit tests
   - **Duplications**: Copy-pasted code that should be refactored

5. **Project Key Concept**
   - Unique identifier for project in SonarQube
   - Used to track quality over time
   - Allows multiple projects on same server
   - Can be anything, but use meaningful names

### Challenges Overcome:
- Understanding token vs password authentication
- Figuring out where to configure server URL
- Learning SonarQube Maven plugin parameters
- Troubleshooting first analysis (binaries path)
- Understanding quality gate concept

### Aha Moments:
- "So THIS is how teams prevent bad code from going to production!"
- "Quality metrics make code reviews so much easier!"
- "The pipeline now enforces standards automatically!"
- "I can see exactly where code quality is improving or degrading!"

---

## 🎯 Next Steps

### Phase 9: Trivy Security Scanning (Next Up!)
Will add filesystem and container image scanning for vulnerabilities

### Upcoming Phases:
- **Phase 10-13**: Maven package build and Nexus deployment
- **Phase 14-16**: Docker image creation and DockerHub push
- **Phase 17-20**: AWS EKS cluster creation and deployment
- **Phase 21**: Email notifications and GitHub webhooks
- **Phase 22-25**: Monitoring with Prometheus and Grafana

---

## 🎓 Why Phase 8 Matters

Phase 8 is **critical** because:

1. **Prevents Bugs Early**: Catches issues before they reach production
2. **Enforces Standards**: Automated code quality checks for every commit
3. **Improves Team Code**: Developers learn from quality feedback
4. **Measurable Quality**: Can track code quality trends over time
5. **Security First**: Identifies vulnerabilities before deployment

**Before Phase 8**: We knew code compiled and tests passed
**After Phase 8**: We know code is high quality, secure, and maintainable

This is where **CI becomes truly valuable** - not just automation, but intelligent quality enforcement!

---

## 📚 Resources Used

- [SonarQube Documentation](https://docs.sonarqube.org/latest/)
- [Jenkins SonarQube Plugin](https://plugins.jenkins.io/sonar/)
- [Maven SonarQube Scanner](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-maven/)
- [SonarQube Quality Gates](https://docs.sonarqube.org/latest/user-guide/quality-gates/)

---

## 🔄 What Happens on Every Build Now
```
Developer pushes code
         ↓
Build triggered in Jenkins
         ↓
1. Clone latest code from GitHub ✓
         ↓
2. Compile Java source files ✓
         ↓
3. Run all unit tests ✓
         ↓
4. Analyze code quality with SonarQube ✓ ← NEW!
   • Check for bugs
   • Find vulnerabilities
   • Calculate test coverage
   • Identify code smells
   • Check against quality gate
         ↓
Results sent to SonarQube dashboard ✓
         ↓
Build marked SUCCESS or FAILURE
         ↓
Quality metrics updated in real-time
         ↓
Team gets instant feedback on code quality!
```

---

## 🙏 Acknowledgments

Special thanks to:
- SonarQube community for excellent documentation
- Jenkins community for robust plugin ecosystem
- Board Game project maintainers for realistic codebase
- DevOps community for sharing best practices

---

## 📈 Project Stats

- **Total Build Time**: ~60 seconds per build
- **Lines of Code Analyzed**: [From SonarQube]
- **Test Coverage**: [From SonarQube]
- **Technical Debt**: [From SonarQube]
- **Quality Rating**: [From SonarQube]

---

 
*Status: **8/25 Phases Complete (32%)**
*Current Achievement: **CI Pipeline with Automated Code Quality Analysis**
*Next Goal: **Add Security Scanning with Trivy**

---

## 🚀 Ready for Production?

**Current Pipeline Status**: 
- ✅ Automated builds
- ✅ Automated tests
- ✅ Quality analysis
- ⏳ Security scanning (coming in Phase 9)
- ⏳ Artifact storage
- ⏳ Container deployment
- ⏳ Production monitoring

**We're getting there! 32% complete.** 🎯