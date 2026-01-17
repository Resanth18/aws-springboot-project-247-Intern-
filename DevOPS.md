# Internship Tasks Documentation – Task 1 & Task 2

> This document is written for **future reference (after ~6 months)**.
> It explains **what was done, why it was done, and what problems were faced** in simple terms, so that the flow is easy to recall without external help.

---

## Task 1: Jenkins Setup and GitHub CI Integration (Backend)

### Task Objective

The goal of this task was to set up **Jenkins locally on Windows**, connect it with **GitHub**, and successfully **build a Spring Boot (Maven) backend project**. This establishes a basic **CI (Continuous Integration)** pipeline.

---

### 1. Jenkins Installation (Windows)

* Downloaded **Jenkins LTS** for Windows from the official Jenkins website.
* Installed Jenkins as a **Windows Service**, so that Jenkins runs automatically in the background.
* During installation, Jenkins asked for a port number.

  * Default port: `8080`
  * Since Spring Boot backend also runs on `8080`, the Jenkins port was changed to **`8081`** to avoid port conflict.

**Reason:**
Two applications cannot listen on the same port at the same time. Changing Jenkins to `8081` avoids runtime issues.

---

### 2. Initial Jenkins Setup

* Jenkins was accessed using:

  ```
  http://localhost:8081
  ```
* Jenkins asked for an initial admin password.
* The password was retrieved from:

  ```
  C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword
  ```
* Installed **suggested plugins** (Git, Credentials, Pipeline, Maven support).
* Created a **dedicated admin user** instead of continuing with the temporary setup user.

**Reason:**
Using a dedicated admin user is safer and closer to real-world Jenkins usage.

---

### 3. GitHub Repository Setup

* A GitHub repository was created for the backend project.
* Backend code (Spring Boot + Maven) was pushed to GitHub.
* A **GitHub Personal Access Token (PAT)** was generated.

  * Scope used: `repo`
  * GitHub password was NOT used (GitHub no longer allows password authentication).

**Reason:**
PATs are the secure and recommended way to allow Jenkins to access GitHub repositories.

---

### 4. Adding GitHub Credentials in Jenkins

* Navigated to:

  ```
  Manage Jenkins → Credentials → Global → Add Credentials
  ```
* Credential type used:

  * Kind: Username with password
  * Username: GitHub username
  * Password: GitHub Personal Access Token
  * ID: `github-creds`

**Reason:**
Jenkins needs credentials to securely clone private GitHub repositories during builds.

---

### 5. Maven Configuration in Jenkins

* Jenkins does NOT automatically know where Maven is installed.
* Maven was configured as a **managed tool** in Jenkins:

  ```
  Manage Jenkins → Tools → Maven Installations
  ```
* Enabled **automatic Maven installation**.

**Reason:**
Jenkins runs builds as a system service and cannot rely on local IDE Maven installations.

---

### 6. Jenkins Job Creation (Backend Build)

* Created a **Freestyle Project** in Jenkins.
* Configured **Git SCM** with:

  * Repository URL (GitHub)
  * Credentials: `github-creds`

#### Maven Build Configuration

* Maven goals used:

  ```
  clean package -Dmaven.test.skip=true
  ```
* Explicit POM path was set:

  ```
  Grouping/pom.xml
  ```

**Why POM path was required:**
The project structure had `pom.xml` inside a subfolder (`Grouping/`). Jenkins always executes builds from the repository root, so the POM path had to be specified manually.

---

### 7. Test Compilation Issue and Fix

* Initial builds failed due to missing test dependencies (`JUnit`, `SpringBootTest`).
* Using `-DskipTests` was not sufficient because Maven still compiles test code.
* Final fix:

  ```
  -Dmaven.test.skip=true
  ```

**Explanation:**

* `-DskipTests` → skips only test execution
* `-Dmaven.test.skip=true` → skips both test compilation and execution

---

### 8. Task 1 Outcome

* Jenkins successfully cloned the GitHub repository.
* Maven build completed successfully.
* Spring Boot JAR was generated.
* CI pipeline verified.

---

## Task 2: Launch Ubuntu EC2 Instance and Connect via Bitvise

### Task Objective

The goal of this task was to **launch an Ubuntu Server on AWS EC2** and establish a **secure SSH connection using Bitvise SSH Client**.

---

### 1. EC2 Instance Launch

* AWS EC2 instance launched with the following configuration:

  * AMI: **Ubuntu Server 22.04 LTS**
  * Instance type: **t2.micro (Free Tier)**
  * Auto-assign Public IP: Enabled

**Reason:**
Ubuntu Server is widely used in industry for backend services and DevOps environments.

---

### 2. Key Pair Configuration

* A new RSA key pair was created during instance launch.
* Key format: `.pem`
* Key file was downloaded and stored securely.

**Reason:**
Ubuntu EC2 instances do not allow password login by default. SSH key-based authentication is mandatory.

---

### 3. Security Group Configuration

Inbound rules configured:

* **SSH (22)** → Source: My IP (secure access)
* **Custom TCP (8080)** → Source: Anywhere (backend access later)

**Note:**
Port `8081` was NOT opened because Jenkins runs locally, not on EC2.

---

### 4. Bitvise SSH Client Setup

* Installed Bitvise SSH Client on local Windows machine.
* Imported the EC2 `.pem` key into Bitvise (converted to `.bpk`).

Connection details used:

* Host: EC2 Public IPv4 address
* Port: 22
* Username: `ubuntu`
* Authentication method: Public key

---

### 5. SSH Connection Verification

* Successfully connected to the Ubuntu EC2 instance.
* Terminal prompt confirmed access:

  ```
  ubuntu@ip-xxx-xxx-xxx-xxx:~$
  ```

---

### 6. Java Environment Verification (Optional but Verified)

* Verified Java installation:

  ```
  java -version
  ```
* Java 21 was available and matched the Jenkins build environment.

**Note:**
This step was verification only. No deployment was performed since the task required only instance launch and connection.

---

## Final Summary

* **Task 1**: Jenkins CI setup completed successfully with GitHub and Maven integration.
* **Task 2**: Ubuntu EC2 instance launched and securely connected using Bitvise SSH.
* No extra actions were performed beyond the assigned scope.

This document serves as a clear reference to understand the complete workflow even after several months.
