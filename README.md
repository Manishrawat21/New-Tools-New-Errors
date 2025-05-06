# New-Tools-New-Errors

# 🛡️ TheHive & Cortex Docker Deployment – Troubleshooting Log

This repository documents a hands-on journey setting up **TheHive** and **Cortex** on **Kali Linux** using **Docker**. The steps below reflect real debugging, trial, and error, culminating in a repeatable troubleshooting reference for others deploying these tools in similar environments.

---

## 📘 Deployment Environment

- **OS:** Kali Linux (Root)
- **Docker Version:** Installed from default repos
- **TheHive Image:** `strangebee/thehive:5.0.23`
- **Cortex Image:** `cortexproject/cortex:latest`

---

## 🪜 Step-by-Step Deployment & Debugging Process

---

### ✅ 1. Pulled TheHive Docker Image

```bash```
docker pull strangebee/thehive:5.0.23

### ✅ 2. Ran TheHive Container

docker run -d --name thehive -p 9000:9000 strangebee/thehive:5.0.23

    Status: Success – Container created and running.

### ❌ 3. Application Crash: AccessDeniedException: /opt/thp/thehive/db/je.properties

**Error Summary:**

org.thp.scalligraph.ScalligraphApplicationImpl$InitialisationFailure: /opt/thp/thehive/db/je.properties
Caused by: java.nio.file.AccessDeniedException

Root Cause: The directory /opt/thp/thehive/db/je.properties did not exist or had improper permissions.

**Investigations:**

ls -l /opt/thp/thehive/db/je.properties  # File/dir did not exist

### 🔧 4. Attempted Ownership Fix

sudo chown -R thehive:thehive /opt/thp

    Error: invalid user: ‘thehive:thehive’

### 🔍 5. Checked Systemd Service

grep '^User=' /etc/systemd/system/thehive.service

    Result: No such file or directory – indicating no native systemd service (expected for Docker).

### 🧹 6. Decided to Delete TheHive Setup

**Action Taken:**

    Deleted TheHive containers

    Tried removing the image:

docker rmi d9d3f61bd07b

    Error: Image still tied to a stopped container.

Fix:

docker ps -a                       # Identify container
docker rm <container-id>           # Remove stopped container
docker rmi d9d3f61bd07b            # Successfully removed image

### 🔁 7. Confirmed TheHive Templates Still Exist

cd /home/kali/Docker-Templates/docker/
ls

    Result: Various TheHive Docker Compose templates were still present (e.g., thehive4-berkleydb, thehive5-minimal, etc.)

### 🛑 8. Verified No Containers Running

**docker ps**

    Output: No running containers.

### ✅ 9. Cortex: Image Pulled, Not Run Yet

docker pull cortexproject/cortex:latest
docker images

    Status: Image present, container not yet launched.

### ✅ Summary: What Was Achieved

    ✅ Docker installed and working

    ✅ TheHive Docker image downloaded and started

    ⚠️ Ran into AccessDeniedException related to BerkeleyDB storage permissions

    ✅ Troubleshooting process explored missing directory, user, permissions, and system service

    ✅ Uninstalled and cleaned up TheHive image and containers

    🕵️ Cortex image pulled and prepared, pending container execution

### 🔜 Next Steps

**Run Cortex and test connectivity**

**Reinstall TheHive using a working Docker Compose template (e.g., thehive5-minimal)**

**Set persistent volume paths correctly for /opt/thp**

    Consider using non-root user in container or adjusting volume mount permissions

### 🤝 Contributions Welcome

This documentation is meant to evolve with practical experience. PRs are welcome if you encounter or fix additional errors with TheHive/Cortex Docker setups.

### Author: Manish Rawat – Security Analyst
### License: MIT


---

### Would you like me to:
- Convert this into a `README.md` and generate a GitHub repo structure?
- Help you run Cortex or reinitialize TheHive with the right volume setup?

Let me know how you'd like to proceed.
