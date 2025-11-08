


#  Jenkins Multibranch Pipeline Deployment on Apache

This project demonstrates how to **set up a Jenkins Multibranch Pipeline** to automatically build and deploy branch-specific HTML applications on an **Apache web server** hosted on an **AWS EC2 Ubuntu instance**.

---

##  Overview

The pipeline automatically:

* Detects branches (`main` and `feature`) in a GitHub repository.
* Builds and tests the HTML app from each branch.
* Deploys each branch’s output to a separate Apache directory:

  * `main` branch → `/var/www/html/main`
  * `feature` branch → `/var/www/html/feature`

Each branch is accessible via:

* **Main branch:** `http://<server-ip>/main/`
* **Feature branch:** `http://<server-ip>/feature/`

---

##  Step 1: Create an EC2 Instance

Launch an **Ubuntu-based EC2 instance** on AWS to host **Jenkins** and **Apache**.

---

##  Step 2: Install Java

Jenkins requires Java. Install it using:

```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
```

---

##  Step 3: Install Jenkins

Add the Jenkins repository and install Jenkins:

```bash
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
```

Start and enable Jenkins:

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

Access Jenkins in the browser at:

```
http://<your-server-ip>:8080
```

---

##  Step 4: Create GitHub Repository & Branches

Create a new **GitHub repository** with two branches:

* **main**
* **feature**

### Files in each branch:

#### `Jenkinsfile`

```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                echo "Building HTML app from ${env.BRANCH_NAME} branch"
            }
        }
        stage('Test') {
            steps {
                echo "Simulating tests for ${env.BRANCH_NAME} branch"
            }
        }
        stage('Deploy') {
            steps {
                echo "Deploying HTML app from ${env.BRANCH_NAME} branch"
                sh '''
                if [ "${BRANCH_NAME}" = "main" ]; then
                    cp index.html /var/www/html/main/index.html
                elif [ "${BRANCH_NAME}" = "feature" ]; then
                    cp index.html /var/www/html/feature/index.html
                fi
                '''
            }
        }
    }
}
```

#### `index.html`

* **Main branch content:**

  ```html
  <h1>Hello from the MAIN branch!</h1>
  ```

* **Feature branch content:**

  ```html
  <h1>Hello from the FEATURE branch!</h1>
  ```

---

##  Step 5: Create Jenkins Multibranch Pipeline

1. Open Jenkins → **New Item** → select **Multibranch Pipeline**.
2. Under **Branch Sources**, add your **GitHub repository URL**.
3. Jenkins automatically detects the `main` and `feature` branches.
4. Each branch runs independently based on the Jenkinsfile defined in that branch.

---

## 🔗 Step 6: Configure Git Repository in Jenkins

Ensure Jenkins can access your GitHub repository:

* For public repos → no credentials needed.
* For private repos → add GitHub credentials under *Manage Jenkins → Credentials*.

---

##  Step 7: Install Apache Web Server

Install and start Apache:

```bash
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2
```

Check if Apache is running:

```
http://<your-server-ip>/
```

---

##  Step 8: Create Branch-Specific Directories

Create directories for each branch deployment:

```bash
sudo mkdir -p /var/www/html/main
sudo mkdir -p /var/www/html/feature
```

Give Jenkins ownership so it can deploy files without sudo:

```bash
sudo chown -R jenkins:jenkins /var/www/html/
sudo chmod -R 755 /var/www/html/
```

---

##  Step 9: Access Applications via Apache

After a successful Jenkins build, access your deployed apps:

* **Main branch:**
   `http://<your-server-ip>/main/`

* **Feature branch:**
   `http://<your-server-ip>/feature/`

---

##  Expected Output

* Jenkins automatically builds both branches when code is pushed.
* Each branch deploys to its respective Apache directory.
* The browser displays:

  * “Hello from the MAIN branch!” at `/main/`
  * “Hello from the FEATURE branch!” at `/feature/`

---

##  Summary

| Component           | Description              |
| ------------------- | ------------------------ |
| **Platform**        | AWS EC2 (Ubuntu)         |
| **CI/CD Tool**      | Jenkins                  |
| **Web Server**      | Apache2                  |
| **Source Control**  | GitHub (Multibranch)     |
| **Automation**      | Jenkinsfile Pipeline     |
| **Deployment Path** | `/var/www/html/{branch}` |

---

##  Demo Workflow

1. Push code to either `main` or `feature` branch.
2. Jenkins detects branch changes → runs pipeline automatically.
3. Build/Test/Deploy stages execute.
4. Apache serves updated HTML for that branch.

---
