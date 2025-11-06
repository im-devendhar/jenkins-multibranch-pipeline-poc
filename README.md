

# Jenkins Multibranch Pipeline Deployment on Apache


---

##  Steps Followed

### Step 1: Created an EC2 Instance
- Launched an Ubuntu-based EC2 instance to host Jenkins and Apache.

### Step 2: Installed Java
```bash
sudo apt update
sudo apt install openjdk-11-jdk -y
````

### Step 3: Installed Jenkins

```bash
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
```

### Step 4: Created GitHub Repository

*   Added the following files:
    *   `Jenkinsfile` with pipeline stages: Checkout, Build, Test, Deploy.
    *   `index.html` with static content: “Hello from the MAIN branch!”

### Step 5: Created Jenkins Job (Multibranch Pipeline)

*   Configured Jenkins to scan the GitHub repository and detect branches (`main`, `feature`).
*   Jenkins automatically created jobs for each branch.

### Step 6: Configured Git Repository in Jenkins

*   Connected GitHub repository using credentials or webhook.
*   Enabled Git polling or webhook for automatic builds.

### Step 7: Installed Apache Web Server

```bash
sudo apt install apache2 -y
```

### Step 8: Created Branch-Specific Directories

```bash
sudo mkdir -p /var/www/html/main
sudo mkdir -p /var/www/html/feature
```

### Step 9: Updated Jenkinsfile to Deploy Based on Branch

```groovy
stage('Deploy') {
    steps {
        echo "Deploying HTML app from ${env.BRANCH_NAME} branch"
        sh '''
        if [ "${BRANCH_NAME}" = "main" ]; then
            sudo cp index.html /var/www/html/main/index.html
        elif [ "${BRANCH_NAME}" = "feature" ]; then
            sudo cp index.html /var/www/html/feature/index.html
        fi
        '''
    }
}
```

### Step 10: Accessed Applications via Apache

*   **Main branch**: `http://<your-server-ip>/main/`
*   **Feature branch**: `http://<your-server-ip>/feature/`

***

## Outcome

*   Successfully deployed static HTML apps for both `main` and `feature` branches.
*   Accessible via separate URLs using Apache’s default configuration.

```

```
