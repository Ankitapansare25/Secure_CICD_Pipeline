# 📘 Secure CI/CD Pipeline using Jenkins, AWS EC2 & IAM

---

## 1. 📌 Project Title

Secure CI/CD Pipeline using Jenkins with Multi-Environment Deployment and IAM-based Access Control

---

## 2. 🎯 Objective

To design and implement a secure CI/CD pipeline that automates deployment across Development, Staging, and Production environments using Jenkins. The pipeline ensures controlled production deployment through manual approval and role-based access control.

---

## 3. 🧰 Tools & Technologies

* Jenkins (CI/CD automation)
* AWS EC2 (cloud servers)
* AWS IAM (role-based access control concept)
* GitHub (source code repository)
* SSH & SCP (secure communication)
* Nginx (web server)
* Amazon Linux

---

## 4. 🏗️ Architecture diagram

Developer → GitHub → Jenkins → Dev Server → Staging Server → Manual Approval → Production Server

---

## 5. 🚀 Implementation Steps

---

### Step 1: Launch EC2 Instances

Create 3 EC2 instances:

* Dev Server
* Staging Server
* Production Server

Configuration:

* OS: Amazon Linux
* Instance type: t2.micro
* Same key pair for all

---

### Step 2: Install Nginx

Login to each EC2:

```bash
ssh -i key.pem ec2-user@<IP>
```

Install and start Nginx:

```bash
sudo yum update -y
sudo yum install nginx -y
sudo systemctl start nginx
sudo systemctl enable nginx
```

---

### Step 3: Set Permissions

```bash
sudo chmod -R 777 /usr/share/nginx/html/
```

(Note: Used only for demo/testing purpose)

---

### Step 4: Setup Jenkins Server

Launch EC2 for Jenkins and install:

```bash
sudo yum install java-17-amazon-corretto -y
```

```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
sudo yum install jenkins -y
```

Start Jenkins:

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

Get password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Access Jenkins:

```
http://<JENKINS-IP>:8080
```

---

### Step 5: Install Plugins

Install:

* Pipeline
* Git
* SSH Agent Plugin
* Role Strategy Plugin

---

### Step 6: Configure SSH

On Jenkins server:

```bash
ssh-keygen
cat ~/.ssh/id_rsa.pub
```

Copy public key.

On all EC2 servers:

```bash
nano ~/.ssh/authorized_keys
```

Paste key.

---

### Step 7: Add Credentials in Jenkins

Go to Credentials → Add

* Type: SSH Username with private key
* Username: ec2-user
* Private key: paste key
* ID: new-key

---

### Step 8: GitHub Setup

* Create repository
* Add `index.html`
* Push code

---

### Step 9: Create Jenkins Pipeline

* New Item → Pipeline
* Pipeline from SCM
* Add GitHub repo

---

### Step 10: Jenkinsfile

```groovy
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo "Building application..."
            }
        }

        stage('Deploy to Dev') {
            steps {
                sshagent(['new-key']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no index.html ec2-user@DEV-IP:/home/ec2-user/
                    ssh ec2-user@DEV-IP "sudo mv /home/ec2-user/index.html /usr/share/nginx/html/"
                    '''
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                sshagent(['new-key']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no index.html ec2-user@STAGING-IP:/home/ec2-user/
                    ssh ec2-user@STAGING-IP "sudo mv /home/ec2-user/index.html /usr/share/nginx/html/"
                    '''
                }
            }
        }

        stage('Manual Approval') {
            steps {
                script {
                    input message: "Approve production deployment?", ok: "Deploy"
                }
            }
        }

        stage('Deploy to Production') {
            steps {
                sshagent(['new-key']) {
                    sh '''
                    scp -o StrictHostKeyChecking=no index.html ec2-user@PROD-IP:/home/ec2-user/
                    ssh ec2-user@PROD-IP "sudo mv /home/ec2-user/index.html /usr/share/nginx/html/"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
```

---

### Step 11: Run Pipeline

* Click Build Now
* Pipeline executes all stages

---

### Step 12: Verify Output

Open browser:

```
http://<DEV-IP>
http://<STAGING-IP>
http://<PROD-IP>
```

---

## 6. 🔐 IAM Role-Based Access Control

IAM concept implemented using Jenkins Role Strategy Plugin:

* Developer Role:

  * Can trigger build
  * Cannot approve production

* Admin Role:

  * Can approve production deployment

This ensures secure and controlled deployment.

---

## 7. ⚠️ Challenges

* SSH permission denied error
* Credential not found issue
* Pipeline stuck at approval
* Permission issues on Nginx folder

---

## 8. ✅ Solutions

* Configured SSH keys properly
* Used ssh-agent in pipeline
* Fixed permission using chmod
* Used manual approval stage correctly

---

## 9. 📊 Result

* CI/CD pipeline successfully implemented
* Automatic deployment to Dev & Staging
* Secure Production deployment with approval

---

## 10. 🚀 Future Enhancements

* Docker integration
* Kubernetes deployment
* Monitoring tools (Prometheus, Grafana)
* Auto rollback

---

## 11. 👩‍💻 Author

Ankita Pansare

---
