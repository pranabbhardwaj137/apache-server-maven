# DevOps Lab - Jenkins & SonarQube

## Aim

Install Jenkins, install SonarQube Server, connect them, and run a pipeline that scans Python code.

---

# Prerequisites

- Windows 10/11
- Internet connection
- JDK 21 (install during setup if missing)

---

# Part 1: Install Jenkins

## Step 1: Download & Install

1. Download Jenkins `.msi` from https://www.jenkins.io/download/
2. Run `jenkins.msi` → Next → Next
3. Select **Run service as LocalSystem** → Next
4. Port: **8081** → Test Port → Next

## Step 2: Install Java JDK 21 (if not detected)

1. Download JDK 21 x64 MSI from https://www.oracle.com/in/java/technologies/downloads/#jdk21-windows
2. Run installer → Next → note installation path → Install → Close
3. Back in Jenkins installer: browse to `C:\Program Files\Java\jdk-21` → Next → Next → Install → Finish

## Step 3: Unlock Jenkins

1. Open http://localhost:8081
2. Get initial password:

```text
notepad.exe C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword
```

3. Paste password → Continue → **Install suggested plugins**

## Step 4: Create Admin User

| Field    | Value   |
| -------- | ------- |
| Username | `admin` |
| Password | `admin` |

Keep default Jenkins URL → **Start using Jenkins**

---

# Part 2: Install SonarQube Scanner Plugin (Jenkins)

1. **Manage Jenkins** → **Plugins**
2. **Available plugins** → search `SonarQube Scanner` → check → Install
3. When status shows **Success** → check **Restart** → log back in (`admin` / `admin`)

---

# Part 3: Install SonarQube Server

## Step 1: Download & Start

1. Download Community Build from https://www.sonarsource.com/products/sonarqube/downloads/
   - Community Build → Download for free → Download only (`.zip`)
2. Unzip to a folder, e.g. `sonarqube-25.5.0.107428`
3. Run:

```text
sonarqube-<version>\bin\windows-x86-64\StartSonar.bat
```

4. If Windows SmartScreen appears → **More info** → **Run anyway**
5. Allow firewall access when prompted

## Step 2: Login & Change Password

1. Open http://localhost:9000
2. Login: `admin` / `admin`
3. Set new password: `Admin@123.456`

## Step 3: Generate Token for Jenkins

1. **Administration** → **Security** → **Users** → **Update tokens**
2. Token name: `jenkins` | Expiration: **Never** → **Generate**
3. Copy token immediately (shown once) → save in Notepad

---

# Part 4: Connect Jenkins to SonarQube

## Step 1: Add SonarQube Scanner Tool

1. http://localhost:8081 → **Manage Jenkins** → **Tools**
2. **Add SonarQube Scanner** → Name: `sonar` → **Save**

## Step 2: Add SonarQube Server

1. **Manage Jenkins** → **System**
2. Under **SonarQube servers** → check **Environment variables** → **Add SonarQube**

| Field      | Value                   |
| ---------- | ----------------------- |
| Name       | `sonar`                 |
| Server URL | `http://localhost:9000` |

3. **Server authentication token** → **+ Add** → **Jenkins**:

| Field  | Value                |
| ------ | -------------------- |
| Kind   | Secret text          |
| Secret | `<paste squ_ token>` |
| ID     | `sonar`              |

4. **Add** → select `sonar` from dropdown → **Save**

---

# Part 5: Jenkins Pipeline

## Step 1: Create Pipeline Job

1. **New Item** → Name: `DO-P4` → **Pipeline** → OK

## Step 2: Pipeline Script

**Update the `xcopy` path to your `helloworld.py` location.**

```groovy
pipeline {
    agent any
    stages {
        stage('Prepare Workspace') {
            steps {
                bat 'xcopy /s /e /y "C:\\Users\\bhard\\Desktop\\helloworld.py" .'
            }
        }
        stage('Code Analysis') {
            environment {
                scannerHome = tool 'sonar'
            }
            steps {
                script {
                    withSonarQubeEnv('sonar') {
                        bat """
                            ${scannerHome}\\bin\\sonar-scanner.bat ^
                            -Dsonar.projectKey=your_project_key ^
                            -Dsonar.projectName=your_project_name ^
                            -Dsonar.projectVersion=1.0 ^
                            -Dsonar.sources=.
                        """
                    }
                }
            }
        }
    }
}
```

Click **Save**

## Step 3: Sample Code — `helloworld.py`

Create on Desktop (or path used in pipeline):

```python
print("Hello, World!")
```

## Step 4: Run Pipeline

1. **Build Now**
2. When done, right-click **SonarQube** link in build → open in new tab
3. View code analysis report at http://localhost:9000

---

# Part 6: Insecure Code Demo (SonarQube findings)

Replace `helloworld.py` with:

```python
def DoSomething():
    password = "123456"
    print("Hi")
    eval("print('Insecure')")
```

Save → **Build Now** again → open SonarQube report → compare issues found.

---

# Quick Reference

| Service   | URL                   | Credentials               |
| --------- | --------------------- | ------------------------- |
| Jenkins   | http://localhost:8081 | `admin` / `admin`         |
| SonarQube | http://localhost:9000 | `admin` / `Admin@123.456` |

| Item                  | Value     |
| --------------------- | --------- |
| Jenkins port          | 8081      |
| SonarQube port        | 9000      |
| Scanner tool name     | `sonar`   |
| SonarQube server name | `sonar`   |
| Token name            | `jenkins` |
| Pipeline job name     | `DO-P4`   |
