# Jenkins + Ansible CI/CD Pipeline (Install Nginx on Remote Server)

This project demonstrates a simple DevOps pipeline where Jenkins triggers an Ansible playbook to install Nginx on a remote worker server.

## Architecture

### CI/CD Flow

```
Developer
   |
   |  git push
   v
GitHub Repository
   |
   |  Webhook / Poll
   v
Jenkins Pipeline
   |
   |  Executes
   v
Ansible Playbook
   |
   |  SSH
   v
Worker Server
   |
   |  Installs
   v
Nginx Web Server
```

### Infrastructure Diagram

```
+-----------------------+
|      Jenkins EC2      |
|-----------------------|
| Jenkins               |
| Ansible               |
| Git                   |
| SSH Key (jenkins)     |
+-----------+-----------+
            |
            | SSH (Port 22)
            v
+-----------------------+
|      Worker EC2       |
|-----------------------|
| ansible user          |
| Nginx installed       |
| Web Server Running    |
+-----------------------+
```

Pipeline Flow:

1. Developer pushes code to GitHub
2. Jenkins pulls repository
3. Jenkins runs Ansible playbook
4. Ansible connects to worker server using SSH
5. Nginx gets installed automatically

---

# Prerequisites

* 2 Ubuntu EC2 instances
* Jenkins installed on the first server
* Ansible installed on Jenkins server
* Git installed
* SSH access between servers

Example:

Jenkins Server: 172.31.5.105
Worker Server: 172.31.5.95

---

## Project Folder Structure

```
Ansible-Jenkins/
│
├── Jenkinsfile          # CI/CD pipeline definition
├── inventory            # Ansible inventory file
├── nginx.yml            # Ansible playbook to install nginx
└── README.md            # Project documentation
```

Explanation:

**Jenkinsfile**

Defines the CI/CD pipeline stages executed by Jenkins.

**inventory**

Contains the list of target servers where Ansible will run tasks.

**nginx.yml**

Ansible playbook that installs and configures Nginx.

**README.md**

Documentation explaining the setup and pipeline workflow.

---

# Step 1: Install Jenkins

```
sudo apt update
sudo apt install openjdk-17-jdk -y
```

install Jenkins

```
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins
```

Open in browser:

```
http://<jenkins-server-ip>:8080
copy the password
cat password
paste the password in browser
```

---

# Step 2: Install Ansible on Jenkins Server

```
sudo apt update
sudo apt install ansible -y
```

Verify:

```
ansible --version
```

---

# Step 3: Create SSH Key for Jenkins User

Switch to Jenkins user

```
sudo su - jenkins
```

Generate key

```
ssh-keygen -t ed25519
```

Key location

```
/var/lib/jenkins/.ssh/id_ed25519
```

---

# Step 4: Copy Jenkins Public Key to Worker Server

Display key

```
cat /var/lib/jenkins/.ssh/id_ed25519.pub
```

On worker server:

```
sudo su - ansible
nano ~/.ssh/authorized_keys
```

Paste Jenkins public key.

Set permissions

```
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Test connection

```
sudo -u jenkins ssh -i /var/lib/jenkins/.ssh/id_ed25519 ansible@<worker-ip>
```

---

# Step 5: Create Ansible Inventory

inventory

```
[dev]
172.31.5.95 ansible_user=ansible ansible_ssh_private_key_file=/var/lib/jenkins/.ssh/id_ed25519
```

---

# Step 6: Create Ansible Playbook

nginx.yml

```
---
- hosts: dev
  become: true
  gather_facts: no

  tasks:

    - name: installing nginx
      apt:
        name: nginx
        state: present
        update_cache: yes
```

---

# Step 7: Create Jenkinsfile

```
pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/<your-repo>.git'
            }
        }

        stage('Verify Files') {
            steps {
                sh 'ls -l'
            }
        }

        stage('Validate Ansible Playbook') {
            steps {
                sh 'ansible-playbook -i inventory nginx.yml --syntax-check'
            }
        }

        stage('Run Ansible Playbook') {
            steps {
                sh 'ansible-playbook -i inventory nginx.yml'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }

        failure {
            echo 'Pipeline failed'
        }
    }
}
```

---

# Step 8: Push Code to GitHub

```
git init
git add .
git commit -m "initial commit"
git branch -M main
git remote add origin <repo-url>
git push -u origin main
```

---

# Step 9: Create Jenkins Pipeline Job

1. Open Jenkins Dashboard
2. Click **New Item**
3. Select **Pipeline**
4. Configure:

Pipeline script from SCM

SCM: Git

Repository URL:

```
https://github.com/<your-repo>.git
```

Branch:

```
main
```

Script Path:

```
Jenkinsfile
```

---

# Step 10: Run Pipeline

Click **Build Now**.

Jenkins will:

1. Pull code from GitHub
2. Validate Ansible playbook
3. Connect to worker server
4. Install Nginx

---

# Verify Deployment

Open browser

```
http://<worker-server-ip>
```

You should see the **Nginx Welcome Page**.

---

# Result

CI/CD pipeline successfully deployed Nginx using:

* Jenkins
* Ansible
* GitHub
* SSH

This demonstrates a basic infrastructure automation workflow used in DevOps environments.
