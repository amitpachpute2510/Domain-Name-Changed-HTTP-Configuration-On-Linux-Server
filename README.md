# 🌐 Domain Name & HTTPS Configuration on Linux Server

This repository contains the step-by-step documentation for configuring a **custom domain name and HTTPS/SSL** for a Java web application running on **Ubuntu Linux Server and Apache Tomcat**.

---

## 📄 Complete Documentation

For the complete step-by-step configuration, refer to the PDF documentation:

👉 [📘 Domain Name Changed & HTTPS Configuration On Linux Server](https://github.com/amitpachpute2510/Domain-Name-Changed-HTTP-Configuration-On-Linux-Server/blob/main/Domain%20Name%20Changed%20%26%20Https%20Configuration%20On%20Linux%20Server%20.pdf)

The PDF contains the complete configuration procedure, including:

* Domain configuration
* `/etc/hosts` configuration
* SSL certificate generation
* Tomcat HTTPS configuration
* Frontend configuration
* Backend configuration
* MySQL database configuration
* SSL enablement
* Tomcat startup and log verification

---

# 📸 Application Screenshots

## 🖥️ Frontend Page

![Frontend Page](https://raw.githubusercontent.com/amitpachpute2510/Domain-Name-Changed-HTTP-Configuration-On-Linux-Server/refs/heads/main/FrontEnd_Page.png)

---

## ⚙️ Backend Page

![Backend Page](https://raw.githubusercontent.com/amitpachpute2510/Domain-Name-Changed-HTTP-Configuration-On-Linux-Server/refs/heads/main/BackEnd_Page.png)

---

# 📌 Project Details

| Component          | Details               |
| ------------------ | --------------------- |
| Operating System   | Ubuntu Linux          |
| Java               | OpenJDK 21            |
| Application Server | Apache Tomcat 10.1.55 |
| Database           | MySQL                 |
| Domain             | `amit.pachpute.in`    |
| Server IP          | `192.168.0.104`       |
| HTTPS Port         | `8686`                |
| Certificate Format | PKCS12                |
| Frontend           | `TfrsUm_V2`           |
| Backend            | `TfrsUmApp_V2`        |

---

# 🔧 Configuration Overview

## 1. Domain Configuration

Check the server IP:

```bash
hostname -I
```

Example:

```text
192.168.0.104
```

Edit the hosts file:

```bash
sudo nano /etc/hosts
```

Add:

```text
192.168.0.104 amit.pachpute.in
```

Verify:

```bash
getent hosts amit.pachpute.in
```

Test:

```bash
ping -c 3 amit.pachpute.in
```

---

## 2. Create SSL Certificate Directory

```bash
sudo mkdir -p /opt/certificates
sudo chmod 700 /opt/certificates
```

---

## 3. Generate SSL Certificate

Generate a PKCS12 certificate using Java Keytool:

```bash
/usr/lib/jvm/java-21-openjdk-amd64/bin/keytool -genkeypair \
-alias tomcat \
-keyalg RSA \
-keysize 2048 \
-validity 3650 \
-storetype PKCS12 \
-keystore /opt/certificates/tomcat.p12 \
-ext SAN=dns:amit.pachpute.in,dns:localhost,ip:192.168.0.104,ip:127.0.0.1
```

Secure the certificate:

```bash
chmod 600 /opt/certificates/tomcat.p12
```

---

# 🔐 4. Configure HTTPS in Tomcat

Open:

```bash
cd /opt/apache-tomcat-10.1.55_Linux_Server/conf
nano server.xml
```

Configure the HTTPS connector:

```xml
<Connector
    port="8686"
    protocol="org.apache.coyote.http11.Http11NioProtocol"
    maxThreads="150"
    SSLEnabled="true"
    scheme="https"
    secure="true">

    <SSLHostConfig>
        <Certificate
            certificateKeystoreFile="/opt/certificates/tomcat.p12"
            certificateKeystorePassword="YOUR_KEYSTORE_PASSWORD"
            certificateKeystoreType="PKCS12"
            type="RSA" />
    </SSLHostConfig>
</Connector>
```

> **Security:** Never upload the actual keystore password to GitHub.

---

## 5. Verify Tomcat Configuration

Check HTTPS port:

```bash
grep -n "8686" /opt/apache-tomcat-10.1.55_Linux_Server/conf/server.xml
```

Check certificate configuration:

```bash
grep -n "certificateKeystore" /opt/apache-tomcat-10.1.55_Linux_Server/conf/server.xml
```

---

# ⚙️ 6. Update Frontend Configuration

Navigate to:

```bash
cd /opt/apache-tomcat-10.1.55_Linux_Server/webapps/TfrsUm_V2/assets/data
```

Open:

```bash
nano configSetting.json
```

Change the old URL:

```text
http://localhost:8686/TfrsUmApp_V2/
```

to:

```text
https://amit.pachpute.in:8686/TfrsUmApp_V2/
```

---

# 🗄️ 7. Update Database Configuration

Create a backup before modifying the constants:

```sql
CREATE TABLE TFRS_CONSTANTS_27092026 AS
SELECT * FROM sbm_tfrsum.tfrs_constants;
```

Check the Common URI:

```sql
SELECT *
FROM sbm_tfrsum.tfrs_constants
WHERE CONSTANT_NAME LIKE '%commonUri%';
```

### Old URL

```text
http://localhost:8686/TfrsUmApp_V2/
```

### New URL

```text
https://amit.pachpute.in:8686/TfrsUmApp_V2/
```

Check SSL configuration:

```sql
SELECT *
FROM sbm_tfrsum.tfrs_constants
WHERE CONSTANT_NAME LIKE '%isSSLEnable%';
```

Change:

```text
N → Y
```

---

# 🚀 8. Start Tomcat

```bash
cd /opt/apache-tomcat-10.1.55_Linux_Server
./bin/startup.sh
```

Expected:

```text
Tomcat started.
```

---

# 📋 9. Monitor Tomcat Logs

```bash
tail -f /opt/apache-tomcat-10.1.55_Linux_Server/logs/catalina.out
```

Check for:

* Tomcat startup errors
* SSL certificate errors
* Database connection errors
* Application deployment errors
* Backend errors

---

# 🌐 Application URLs

### Frontend

```text
https://amit.pachpute.in:8686/TfrsUm_V2/
```

### Backend

```text
https://amit.pachpute.in:8686/TfrsUmApp_V2/
```

---

# ✅ Verification Checklist

* [x] Domain mapped to server IP
* [x] Domain resolution verified
* [x] SSL certificate generated
* [x] PKCS12 certificate configured
* [x] Tomcat HTTPS configured
* [x] Frontend URL updated
* [x] Backend URL updated
* [x] Database URI updated
* [x] SSL enabled
* [x] Tomcat restarted
* [x] Tomcat logs verified
* [x] Frontend tested
* [x] Backend tested

---

# 🔒 Security

Do **not** upload sensitive credentials or certificates to GitHub.

Do not upload:

```text
*.p12
*.jks
*.key
*.pem
.env
Database passwords
Keystore passwords
Private keys
API keys
```

Example `.gitignore`:

```gitignore
*.p12
*.jks
*.key
*.pem
.env
```

Use placeholders:

```text
YOUR_KEYSTORE_PASSWORD
YOUR_DB_PASSWORD
YOUR_SERVER_IP
```

---

# 👨‍💻 Author

**Amit Pachpute**

**Data Engineer | L1 Cyber Analyst | Application Support Intern**

Mumbai, India
