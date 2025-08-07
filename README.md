To set up **Snyk** in **Jenkins**, follow these steps:

### **1. Install the Snyk Plugin in Jenkins**
1. Open **Jenkins** and go to **Manage Jenkins** → **Manage Plugins**.
2. Click on the **Available** tab and search for **Snyk Security**.
3. Select the **Snyk Security** plugin and install it.
4. Restart Jenkins after installation.

### **2. Configure Snyk API Token in Jenkins**
1. Go to **Snyk.io** and log in.
2. On Left side go down click on profile -->Navigate to **Account Settings** → **Auth Token** and copy the token.
3. In **Jenkins**, go to **Manage Jenkins** → **Manage Credentials**.
4. Under **Global credentials (unrestricted)**, click **Add Credentials**.
5. Choose:
   - **Kind**: Secret text
   - **Scope**: Global
   - **Secret**: Paste the **Snyk API Token**
   - **ID**: `SNYK_TOKEN` (or any identifiable name)
   - **Description**: "Snyk API Token"
6. Click **OK** to save.

### **3. Auto Install for Snyk CLI in Jenkins
1. Go to **Jenkins** → **Manage Jenkins** →**Synk Installation**.
2. Scroll down to Snyk and click **Add Snyk**.
3. Provide a name (e.g., SnykCLI).
4. Select **Install automatically** (this downloads the Snyk CLI when required).
5. Save the configuration.

**IF the synk installation is completed in jenkins or else we are facing any version issue we can install synk if no pblm we can ignore below manuall method**

### **Installing Snyk Manually**
Run the following command to check your system's CPU architecture:
```
uname -m
```
Expected Outputs:

    x86_64 → You need the x86_64 (AMD64) version of Snyk.
    arm64 / aarch64 → You need the ARM64 version of Snyk.

```
curl -Lo snyk https://github.com/snyk/snyk/releases/latest/download/snyk-linux
```
```
curl -Lo snyk https://github.com/snyk/snyk/releases/latest/download/snyk-linux-arm64
```
Run following command
```
chmod +x snyk
```
```
sudo mv snyk /usr/local/bin/snyk
```

Check Installation
```
snyk --version
```

---

### **4. Integrate Snyk with Jenkins Pipeline**
#### **Option 1: Freestyle Project**
1. Create a new **Freestyle Project** in Jenkins.
2. Under **Build Environment**, check **Use Snyk Security**.
3. Configure:
   - **Snyk API Token**: Select the **SNYK_TOKEN** credential.
   - **Monitor & Test Options**: Enable **Monitor** or **Test** as needed.
   - **Fail the build on vulnerabilities**: Choose as per your requirement.
4. Save and run the build. <br>
### **OR, we can run using shell command**
5. Map Variable SNYK_TOKEN with credeantials by addding a Binding in **Build Environment** --> **Use Sceret**
6. Add a **Build Step** --> **Execute Shell**
```
/usr/local/bin/snyk auth $
/usr/local/bin/snyk test --severity-threshold=low
/usr/local/bin/snyk monitor
```
Save and run the build.

#### **Option 2: Jenkins Pipeline (Declarative)**
Modify your `Jenkinsfile` to include Snyk security scanning:

```
pipeline {
    agent any
    environment {
        SNYK_TOKEN = credentials('SNYK_TOKEN')  // Fetch token from Jenkins credentials
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-repo.git'
            }
        }
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Snyk Security Scan') {
            steps {
                sh 'snyk test --severity-threshold=high'
            }
        }
    }
}
```
- `snyk test --severity-threshold=high`: Scans dependencies and fails if **high** or **critical** vulnerabilities are found.
- To monitor continuously, add: `sh 'snyk monitor'`.

### **5. Run and Validate**
- Trigger the pipeline and check if Snyk scans the dependencies correctly.
- If vulnerabilities are found, fix them using `snyk wizard` or `snyk fix`.

### **6. View Reports**
After running **Snyk** in Jenkins, you can view the **vulnerability report** in multiple ways depending on your setup:

---

### **1. Console Output in Jenkins Job**
- After running a **Freestyle** or **Pipeline** job with Snyk, go to:
  - **Jenkins Dashboard** → **Your Job** → **Build History** → Click on the **Latest Build**.
  - Click **Console Output** to see the scanned vulnerabilities.
  - Look for lines showing vulnerability details, severity levels, and remediation steps.

---

### **2. Snyk Web Dashboard (Recommended)**
- If you have enabled `snyk monitor`, results will be uploaded to **Snyk's Web Dashboard**.
- To check:
  1. Log in to **[Snyk.io](https://snyk.io)**
  2. Navigate to **Projects**.
  3. Find your **Jenkins project**, and click to see detailed reports, including:
     - Identified vulnerabilities
     - Severity levels (Low, Medium, High, Critical)
     - Suggested fixes

---
