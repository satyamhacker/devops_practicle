# 🎮 Jenkins Quest: The Ultimate 30-Level Hardcore Roadmap

> **Samajh gaya!** 30 Levels, 14 Phases, zero boring stuff, aur full **CTF (Capture The Flag)** mode. Purane "Step 1, Step 2" ko replace kar diya hai **"Missions"** aur **"Flag Verification"** se. Ab Jenkins seekhna ek game jaisa lagega! 🚀

---

## 🏰 PHASE 0 & 1: The Command Center (Infra & OS)

*Goal: Base setup aur JVM tuning.*

### 🛡️ Level 0: Hardening the Gate (OS Hardening)

**💡 The Concept - Kya Seekhoge?**
Tum seekhoge **Linux server ko secure** kaise karte hain Jenkins ke liye. Sirf install karna kaafi nahi, **OS level hardening** zaroori hai.

**🔥 Why & Learning Outcome - Kyun Zaroori Hai?**
**Real Production Scenario:** Tumne Jenkins seedha `root` user pe chala diya. Hacker ne **pure server ka control** le liya.
**Real Production Scenario:** Server ka time sync nahi tha. Jenkins logs aur Git commits ka time match nahi hua, **audit logs corrupt** ho gaye.

**🎯 Mission:**
1. Create dedicated non-root Jenkins OS user (`useradd jenkins`)
2. Update OS packages (`apt update && apt upgrade`)
3. Install Java LTS (JDK 17) (`apt install openjdk-17-jdk`)
4. Install Git, curl, unzip utilities
5. Configure firewall (only SSH + Jenkins port allowed)
6. Disable root SSH login (`PermitRootLogin no`)
7. Disable password SSH login (use keys only)
8. Configure time sync (`chrony` or `systemd-timesyncd`)
9. Verify hostname + DNS resolution (`nslookup`, `hostname`)
10. Verify disk space + inode availability (`df -h`, `df -i`)

**🚩 Flag Verification (Success Proof):**
```bash
# Test 1: Root login fail hona chahiye
ssh root@your-ip  # ❌ Permission denied

# Test 2: Time sync check
timedatectl  # ✅ System clock synchronized: yes

# Test 3: Disk space sufficient
df -h && df -i  # ✅ >20% free space
```

**💀 Real Horror Story:**
Ek company ne root pe Jenkins chalaya. Hacker ne SSH brute-force kiya aur **pure production server ka control** le liya. Sab kuch wipe ho gaya. Backup bhi nahi tha. 😱

---

### ⚡ Level 1: The Immortal Service (Systemd)

**💡 The Concept - Kya Seekhoge?**
Jenkins ko **WAR file double click** karke nahi chalana hai. Tum seekhoge **Systemd service** ke through install karna.

**🔥 Why & Learning Outcome - Kyun Zaroori Hai?**
**Real Production Scenario:** Server restart hua. Jenkins **auto-start nahi hua**. **Deployment block** ho gaya.
**Real Production Scenario:** WAR file manually chalayi thi. Process crash hua aur **koi restart mechanism nahi tha**.

**🎯 Mission:****
1. Install Jenkins via official package repo (not WAR for prod)
2. Run Jenkins under systemd (`systemctl start jenkins`)
3. Enable auto-start on boot (`systemctl enable jenkins`)
4. Verify logs location (`/var/log/jenkins`)
5. Change default port if conflict (`/etc/default/jenkins`)
6. Verify restart behavior (Reboot server and check)
7. Practice stop/start/restart safely (`systemctl restart jenkins`)

**🚩 Flag Verification:**
```bash
# Test 1: Service status check
sudo systemctl status jenkins  # ✅ Active: active (running)

# Test 2: Auto-start test (CRITICAL!)
sudo reboot
# 2 minutes wait karo...
curl http://localhost:8080  # ✅ Jenkins UI load hona chahiye

# Test 3: Logs verify
ls -lh /var/log/jenkins/  # ✅ jenkins.log file dikhni chahiye
```

**💀 Real Horror Story:**
Friday night ko server crash hua. Monday morning tak kisi ko pata nahi chala ki Jenkins down hai kyunki auto-start nahi tha. **3 din ka deployment backlog** ban gaya! 😭

---

### 🧠 Level 2: The Memory Tuner (JVM Heap)

**💡 The Concept - Kya Seekhoge?**
Jenkins Java pe chalta hai. Tum seekhoge **Heap Memory** configure karna taaki Jenkins **OOM (Out Of Memory)** ho kar crash na ho.

**🔥 Why & Learning Outcome - Kyun Zaroori Hai?**
**Real Production Scenario:** Bade builds chalne lage. Jenkins ne saari RAM kha li. **Server hang** ho gaya.
**Real Production Scenario:** GC logs nahi the. Performance slow thi par **pata nahi chala kyun**.

**🎯 Mission:****
1. Configure heap memory limits (`-Xmx`, `-Xms`)
2. Set min/max heap in `/etc/default/jenkins`
3. Enable GC logging (`-XX:+PrintGCDetails`)
4. Learn how to capture thread dumps (`kill -3 <pid>`)
5. Observe memory usage during builds (`htop`, `jstat`)
6. Learn where OOM errors appear (Logs & System logs)

**🚩 Flag Verification:**
```bash
# Test 1: Custom heap limits verify
jps  # Jenkins PID nikalo
jstat -gc <jenkins-pid>  # ✅ Custom heap size dikhni chahiye

# Test 2: GC logs check
cat /var/log/jenkins/gc.log  # ✅ GC activity dikhni chahiye

# Test 3: Thread dump capture
kill -3 <jenkins-pid>
cat /var/log/jenkins/jenkins.log | grep "Full thread dump"  # ✅ Dump dikhna chahiye
```

**💀 Real Horror Story:**
Ek badi company mein Jenkins ko default heap (512MB) pe chhod diya. Jab 50 parallel builds chale, **OOM error** se Jenkins crash ho gaya. Sab builds fail. Team ne 4 ghante debug kiya! 🔥

---

## 🔐 PHASE 2: The Vault (Security & Governance)

*Goal: No leaks, no unauthorized access.*

### 👥 Level 3: RBAC Strategy (Roles)

**💡 The Concept - Kya Seekhoge?**
Jenkins ko public internet pe nanga nahi chhod sakte. Tum seekhoge **RBAC (Role Based Access Control)** aur **Anonymous access** band karna.

**🔥 Why & Learning Outcome - Kyun Zaroori Hai?**
**Real Production Scenario:** Kisi ne bina login kiye Jenkins khola aur **Production Delete Job** chala diya.
**Real Production Scenario:** Developer ko Admin access mil gaya galati se. Usne **global credentials chura liye**.

**🎯 Mission:****
1. Enable security realm (local users)
2. Disable anonymous access
3. Force login required
4. Install Folders plugin (manually from GitHub if needed)
5. Install Matrix Authorization plugin
6. Create role-based access users (Admin, Dev, Viewer)
7. Create folder-based isolation
8. Remove all permissions from anonymous
9. Disable controller executors (set to zero)

**🔧 Pro Tip - Manage Users Nahi Dikh Raha?**

**External Security Realms Explained:**
- **LDAP (Lightweight Directory Access Protocol):** Corporate directory service for centralized user management
- **Active Directory (AD):** Microsoft's directory service for Windows domain networks
- **SSO (Single Sign-On):** One login for multiple applications (e.g., OAuth, SAML)

Agar Jenkins external security realm (LDAP/AD/SSO) pe configured hai, to **Manage Users** hide ho jaata hai kyunki users external system se manage hote hain.

**Fix Steps:**
```
1. Manage Jenkins → Configure Global Security
2. Direct URL: http://<your-jenkins-server>/configureSecurity/
3. Security Realm = "Jenkins' own user database"
4. ✅ Allow users to sign up (optional)
5. Save → Restart Jenkins
6. Ab "Manage Users" option visible hoga!
```

**🚩 Flag Verification:**
```bash
# Test 1: Anonymous access blocked
curl http://localhost:8080  # ✅ Login page dikhna chahiye

# Test 2: Dev user limited access
# Dev user se login karo
# "Manage Jenkins" button ❌ gayab hona chahiye

# Test 3: Master executors disabled
# Dashboard pe "Build Executor Status" check karo
# Master: 0 executors ✅ dikhna chahiye
```

**💀 Real Horror Story:**
Ek startup ne anonymous access enable chhod diya. Kisi ne Google se Jenkins URL dhundha aur **Production database delete job** chala diya. Company ko $2M ka loss hua! 💸

---

### 🎭 Level 4: The Masked Secret (Credentials)

**💡 The Concept - Kya Seekhoge?**
Password ko script mein likhna mana hai. Tum seekhoge **Jenkins Credentials Store** use karna aur secrets ko **mask** karna.

**🔥 Why & Learning Outcome - Kyun Zaroori Hai?**
**Real Production Scenario:** Tumne password Jenkinsfile mein hardcode kiya. Code GitHub pe push ho gaya. **Secrets leak** ho gaye.
**Real Production Scenario:** Console output mein password **plain text** mein print ho gaya.

**🎯 Mission:****
1. Add SSH keys credentials
2. Add username/password credentials
3. Add secret text credentials
4. Add secret file credentials
5. Test masking behavior (Print secret in console)
6. Practice credential scoping (global vs folder)
7. Practice credential rotation (Update secret & test)
8. Test broken credential behavior (Wrong key & verify fail)

**🚩 Flag Verification:**
```groovy
// Pipeline mein secret print karo
pipeline {
    agent any
    environment {
        SECRET = credentials('my-secret-id')
    }
    stages {
        stage('Test') {
            steps {
                sh 'echo $SECRET'  // Console mein **** dikhna chahiye ✅
            }
        }
    }
}
```

**Test Results:**
- Console output: `****` ✅ (secret masked)
- Wrong credential ID: Build fail ✅
- Folder-scoped credential: Dusre folder se access nahi hona chahiye ✅

**💀 Real Horror Story:**
Ek developer ne AWS keys Jenkinsfile mein hardcode kar diye. GitHub pe push ho gaya. **2 ghante mein $50,000 ka AWS bill** ban gaya (crypto mining)! 😱

---

### 🧩 Level 5: Plugin Lifecycle

**💡 The Concept - Kya Seekhoge?**
Plugins Jenkins ki taqat hain par vulnerability ka source bhi. Tum seekhoge **LTS version** use karna aur plugins ko **safely update** karna.

**🔥 Why & Learning Outcome - Kyun Zaroori Hai?**
**Real Production Scenario:** Tumne latest weekly version liya. Ek plugin incompatible tha. **Jenkins start hi nahi hua**.
**Real Production Scenario:** Update kiya aur backup nahi tha. **Configuration corrupt** ho gayi.

**🎯 Mission:****
1. Learn LTS vs weekly Jenkins (Verify version)
2. List installed plugins
3. Check dependency tree (Plugin Manager)
4. Practice safe plugin upgrade
5. Take backup before upgrade
6. Upgrade one plugin only
7. Test rollback using backup
8. Remove unused plugins
9. Learn plugin pinning concept

**🚩 Flag Verification:**
```bash
# Test 1: Plugin backup verify
ls -lh /var/lib/jenkins/plugins/*.bak  # ✅ Backup files dikhni chahiye

# Test 2: Plugin update safe
# Plugin Manager → Update → Restart
# Jenkins successfully start hona chahiye ✅

# Test 3: Rollback test
# Purani .bak file restore karo
sudo systemctl restart jenkins
# Jenkins healthy start hona chahiye ✅
```

**💀 Real Horror Story:**
Ek company ne Friday evening ko 20 plugins ek saath update kar diye (bina backup ke). Jenkins start nahi hua. **Pure weekend team ne debug kiya**. Monday morning tak fix hua! 🔥

---

## 🐋 PHASE 3: The Legion (Agents Architecture)

*Goal: Master ko free rakho. Builds agents pe chalein.*

### 🛠️ Level 6: Static SSH Agents (Foundation)

**💡 The Concept - Kya Seekhoge?**
Builds Master pe nahi chalenge. Tum seekhoge **alag VM (Agent)** ko SSH se connect karna aur wahan build run karna.

**🔥 Why & Learning Outcome - Kyun Zaroori Hai?**
**Real Production Scenario:** Master pe build chalne se CPU full ho gaya. **Jenkins UI slow** ho gayi.
**Real Production Scenario:** Agent pe build chala to Master **free raha** next job pick karne ke liye.

**🎯 Mission:****
1. Create separate Linux agent VM
2. Install Java on agent
3. Create agent user
4. Configure SSH key auth (Master to Agent)
5. Connect agent via SSH launcher
6. Label agent with capability labels
7. Run test pipeline on agent
8. Verify build never runs on controller

**🚩 Flag Verification:**
```bash
# Test 1: Agent connection
# Jenkins UI → Manage Nodes → Agent Status
# "Agent successfully connected" ✅ dikhna chahiye

# Test 2: Build on agent
# Job configuration mein agent label lagao
# Build agent pe chalna chahiye, master pe nahi ✅

# Test 3: Agent log verify
cat /var/log/jenkins/agent.log
# Connection success message dikhna chahiye ✅
```

**💀 Real Horror Story:**
Ek company ne master pe hi sab builds chalaye. CPU 100% ho gaya. **Jenkins UI itna slow** ho gaya ki koi job trigger nahi kar pa raha tha. 2 ghante downtime! 😱

---

### 🛰️ Level 6B: The Ghost Agent (Docker Cloud)

**💡 The Concept - Kya Seekhoge?**
Static agents manage karna mushkil hai. Tum seekhoge **Docker Containers** ko temporary agent banana jo build ke baad automatically delete ho jayein.

**🔥 Why & Learning Outcome - Kyun Zaroori Hai?**
**Real Production Scenario:** Agent pe purani libraries thin. **Conflict hua**.
**Real Production Scenario:** Build khatam hua, **container delete** ho gaya. Saaf sutra environment.

**🎯 Mission:****
1. Install Docker Plugin
2. Jenkins user ko `docker` group mein dalo
3. Cloud configure karo using `/var/run/docker.sock`
4. Docker templates banao (Java, Node, Python images)
5. Ephemeral agents test karo
6. Verify cleanup after build

**🚩 Flag Verification:**
```bash
# Test 1: Docker access verify
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins

# Test 2: Naya job chalao
# Host pe docker ps maro
docker ps  # ✅ Temporary agent container dikhna chahiye

# Test 3: Job complete hone ke baad
docker ps -a | grep jenkins  # ✅ Container delete ho jana chahiye
```

**💀 Real Horror Story:**
Ek team ne static agents use kiye. 100 builds ke baad disk full ho gaya kyunki **cleanup nahi tha**. Production deploy 6 ghante late hua! 😤

---

### 🔒 Level 7: Agent Security Model

**🎯 Mission:**
1. Enable agent-to-controller security
2. Verify agent cannot read controller secrets
3. Learn inbound vs outbound agents
4. Restrict agent filesystem access
5. Test agent disconnect handling

**🚩 Flag Verification:**
```groovy
// Pipeline se controller file read karne ki koshish karo
pipeline {
    agent { label 'docker' }
    stages {
        stage('Hack Attempt') {
            steps {
                sh 'cat /var/lib/jenkins/config.xml'  // ❌ Access Denied milna chahiye
            }
        }
    }
}
```

**Test Results:**
- File access attempt fail ✅
- Security logs mein unauthorized attempt record ✅
- Agent disconnect pe job queue mein wait kare ✅

---

### 🏷️ Level 8: Node Labeling & Routing Strategy

**🎯 Mission:**
1. Create capability-based labels (not names)
2. Route jobs via labels
3. Practice multi-label selection
4. Build label strategy per tech stack
5. Avoid hard-binding to nodes

**🚩 Flag Verification:**
```groovy
// Wrong label test
pipeline {
    agent { label 'non-existent-label' }
    stages {
        stage('Test') {
            steps {
                echo 'This should never run'
            }
        }
    }
}
```

**Test Results:**
- Job queue mein atak jana chahiye ✅ (Offline status)
- Java job sirf Java agent pe chale ✅
- Node job sirf Node agent pe chale ✅

---

## 🔗 PHASE 4 & 5: The Teleport & The Scroll (Git & Pipelines)

*Goal: Code to Build automation.*

### 🪝 Level 9: The Webhook Ritual

**🎯 Mission:**
1. Use SSH Git auth (not HTTPS passwords)
2. Store Git credentials in Jenkins
3. Use Jenkinsfile from SCM
4. Disable polling
5. Configure webhooks (GitHub/GitLab)
6. Validate webhook delivery logs
7. Simulate webhook failure (Block port & test)

**🚩 Flag Verification:**
```bash
# Test 1: Git push karo
git commit -m "test" && git push

# Test 2: Jenkins logs check karo (5 seconds ke andar)
# Build automatically start hona chahiye ✅

# Test 3: Webhook delivery
# GitHub → Settings → Webhooks → Recent Deliveries
# Green checkmark dikhna chahiye ✅
```

**💀 Real Horror Story:**
Ek team polling use kar rahi thi (every 1 min). Git server pe **10,000 requests/day** ja rahe the. Git provider ne account suspend kar diya! 🚫

---

### 🌿 Level 10: Multibranch Mastery

**🎯 Mission:**
1. Create multibranch job
2. Enable branch discovery
3. Enable PR discovery
4. Build feature branches automatically
5. Test branch deletion cleanup
6. Validate PR validation builds

**🚩 Flag Verification:**
```bash
# Test 1: Nayi branch banao
git checkout -b feature/test-123
git push origin feature/test-123

# Test 2: Jenkins dashboard check karo
# Automatically naya folder/job banna chahiye ✅

# Test 3: Branch delete karo
git push origin --delete feature/test-123
# Kuch din baad Jenkins job auto-delete ho jana chahiye ✅
```

---

### 📜 Level 11A & 11B: Declarative & Notifications

**🎯 Mission (11A - Declarative Pipeline):**
1. Use options block
2. Use environment block
3. Use post conditions (always, success, failure)
4. Use timeouts
5. Use build discarder
6. Test timeout abort
7. Test build retention

**🎯 Mission (11B - Notifications & Reporting):**
1. Email Extension Plugin install karo
2. SMTP settings configure karo (Gmail SMTP - App Password use karna)
3. Slack Plugin install karo aur Slack App create karo
4. Token Jenkins credentials mein add karo
5. Pipeline mein post block mein failure condition par email bhejo
6. Pipeline mein post block mein failure condition par slack message bhejo
7. Email ke liye `emailext` step use karo
8. Slack ke liye `slackSend` step use karo
9. Test karo ki failure par notification aata hai
10. Unstable condition par alag notification bhejo
11. Test reports (JUnit) publish karo

**🚩 Flag Verification:**
```groovy
pipeline {
    agent any
    options {
        timeout(time: 10, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }
    stages {
        stage('Test') {
            steps {
                sh 'exit 1'  // Intentional failure
            }
        }
    }
    post {
        failure {
            slackSend(channel: '#builds', message: "Build Failed!")
        }
    }
}
```

**Test Results:**
- Build fail karo aur Slack/Email pe notification check karo ✅
- 10 min se zyada chalne pe build abort ho ✅
- Sirf last 5 builds retain hon ✅
- Console logs mein secrets masked hon ✅
- Test reports (JUnit) publish ho kar UI mein dikhein ✅

---

### 🎛️ Level 12: Parameterized Quest

**🎯 Mission:**
1. Add choice parameters
2. Add boolean parameters
3. Add string parameters
4. Drive environment selection via parameter
5. Test wrong input handling

**🚩 Flag Verification:**
```groovy
pipeline {
    agent any
    parameters {
        choice(name: 'ENVIRONMENT', choices: ['Dev', 'Stage', 'Prod'], description: 'Select Environment')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests?')
    }
    stages {
        stage('Deploy') {
            steps {
                echo "Deploying to ${params.ENVIRONMENT}"
            }
        }
    }
}
```

**Test Results:**
- Build button ki jagah "Build with Parameters" aana chahiye ✅
- Console log mein selected value print ho ✅

---

### 🧹 Level 13: Workspace Isolation

**🎯 Mission:**
1. Learn workspace layout
2. Use custom workspace
3. Avoid workspace collisions
4. Enable workspace cleanup
5. Test leftover file issues
6. Practice wipe workspace policies

**🚩 Flag Verification:**
```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'touch test-file.txt'
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
```

**Test Results:**
- Build khatam hone ke baad `/var/lib/jenkins/workspace` folder khali hona chahiye ✅
- Sirf last 5 builds ka history retain ho ✅

---

## ⚡ PHASE 6: The Overclock (Optimization)

*Goal: Speed and DRY principles.*

### ⏩ Level 14: Parallel Execution

**🎯 Mission:**
1. Create parallel stages
2. Measure time difference
3. Handle parallel failure behavior
4. Combine with agents

**🚩 Flag Verification:**
```groovy
pipeline {
    agent any
    stages {
        stage('Parallel Tests') {
            parallel {
                stage('Unit Tests') {
                    steps { sh 'echo "Unit tests running"' }
                }
                stage('Integration Tests') {
                    steps { sh 'echo "Integration tests running"' }
                }
                stage('Security Scan') {
                    steps { sh 'echo "Security scan running"' }
                }
            }
        }
    }
}
```

**Test Results:**
- Pipeline Stage View mein multiple bars ek saath green honi chahiye ✅
- Total build duration sequential se kam ho ✅

---

### 📚 Level 15: Shared Libraries (DRY Pipelines)

**🎯 Mission:**
1. Create shared library repo
2. Register global library
3. Load library in pipeline
4. Update library without job change
5. Version library usage

**🚩 Flag Verification:**
```groovy
@Library('my-shared-library@main') _

pipeline {
    agent any
    stages {
        stage('Use Library') {
            steps {
                script {
                    myCustomFunction()  // Library se call
                }
            }
        }
    }
}
```

**Test Results:**
- `Jenkinsfile` sirf 10 line ka hona chahiye, baaki sab library se call ho ✅
- Library update karne pe bina job change kiye naya logic chale ✅

---

### 📦 Level 16A & 16B: Controller as Docker & Failure Handling

**🎯 Mission (16A - Jenkins Controller as Docker):**
1. Docker install karo
2. `jenkins/jenkins:lts-jdk17` image pull karo
3. Volume create karo: `docker volume create jenkins_home`
4. Container run karo with volume mount and port mapping
5. Initial password retrieve karo aur login karo
6. Container restart karo aur verify karo ki data safe hai
7. Backup/restore volume ka practice karo

**🎯 Mission (16B - Failure Handling):**
1. Use retry strategy
2. Mark unstable builds
3. Catch and continue patterns
4. Differentiate infra vs test failures
5. Practice controlled failure flows

**🚩 Flag Verification:**
```groovy
pipeline {
    agent any
    stages {
        stage('Flaky Test') {
            steps {
                retry(3) {
                    sh 'curl https://flaky-api.com/test'
                }
            }
        }
    }
}
```

**Test Results:**
- Network fail karke dekho, build 3 baar retry hona chahiye ✅
- Container restart karo aur verify karo ki data safe hai ✅

---

### 🚦 Level 17: Throttling & Queue Control

**🎯 Mission:**
1. Install throttling plugin
2. Limit concurrent builds
3. Configure quiet period
4. Prevent queue storms
5. Test burst pushes

**🚩 Flag Verification:**
```bash
# Test: 5 jobs trigger karo ek saath
# Sirf 2 chalne chahiye, 3 queue mein wait karne chahiye ✅
```

**Test Results:**
- Queue mein jobs wait kar rahi hon ✅
- Executor usage controlled ho ✅

---

## 🚢 PHASE 7 & 8: Release & Artifacts

*Goal: Deploy and Store.*

### 🛑 Level 18: Manual Approval Gates

**🎯 Mission:**
1. Add manual approval gates
2. Build → Stage → Prod flow
3. Add environment promotion
4. Test approval rejection path

**🚩 Flag Verification:**
```groovy
pipeline {
    agent any
    stages {
        stage('Deploy to Prod') {
            steps {
                input message: 'Deploy to Production?', ok: 'Proceed'
                echo 'Deploying to Prod...'
            }
        }
    }
}
```

**Test Results:**
- Pipeline pause honi chahiye jab tak tum "Proceed" nahi dabate ✅
- Reject karne pe build abort ho ✅

---

### 🔐 Level 19: Locking & Resource Control

**🎯 Mission:**
1. Configure lockable resources
2. Prevent concurrent deploy
3. Test queue waiting behavior

**🚩 Flag Verification:**
```groovy
pipeline {
    agent any
    stages {
        stage('Deploy') {
            options {
                lock(resource: 'production-db')
            }
            steps {
                echo 'Deploying to DB...'
                sleep 30
            }
        }
    }
}
```

**Test Results:**
- Do jobs ek saath start karo, ek "Waiting for Resource" dikhayega ✅

---

### 🏺 Level 20: Artifact Strategy

**🎯 Mission:**
1. Archive artifacts
2. Configure retention
3. External artifact storage (e.g., S3/Nexus)
4. Stash/unstash across stages
5. Test artifact reuse

**🚩 Flag Verification:**
```groovy
pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'echo "Hello" > artifact.txt'
                archiveArtifacts artifacts: 'artifact.txt', fingerprint: true
            }
        }
    }
}
```

**Test Results:**
- Build page pe JAR/WAR file downloadable honi chahiye ✅
- External storage mein artifact upload ho ✅

---

## 👁️ PHASE 9: The Oracle (Observability)

*Goal: Monitoring and Debugging.*

### 🔄 Level 21: Replay & Debug

**🎯 Mission:**
1. Use replay feature
2. Read console logs deeply
3. Read system logs
4. Capture thread dump
5. Analyze failed pipeline stages

**🚩 Flag Verification:**
```bash
# Test: Pipeline fail hui
# Jenkins UI → Build → Replay
# Code change karo bina Git push kiye
# Build success hona chahiye ✅
```

**Test Results:**
- Bina Git commit kiye pipeline fix ho kar chal jaye ✅
- Error ka root cause log mein mil jaye ✅

---

### 📊 Level 22: Prometheus Metrics

**🎯 Mission:**
1. Enable Prometheus metrics
2. Monitor queue size
3. Monitor executor usage
4. Monitor memory
5. Monitor build duration trends

**🚩 Flag Verification:**
```bash
# Test: Metrics endpoint access karo
curl http://localhost:8080/prometheus/

# Output mein metrics dikhne chahiye:
# jenkins_queue_size_value
# jenkins_executor_count_value
# jenkins_job_duration_milliseconds_summary ✅
```

**Test Results:**
- Dashboard pe "Queue Length" aur "Build Duration" ka graph dikhna chahiye ✅

---

### 📂 Level 23: Audit & Compliance

**🎯 Mission:**
1. Install audit trail plugin
2. Track config changes
3. Track user actions
4. Review audit logs

**🚩 Flag Verification:**
```bash
# Test: User create karo ya job delete karo
# Audit logs check karo
cat /var/log/jenkins/audit.log

# Entry dikhni chahiye:
# [timestamp] User 'admin' created job 'test-job' ✅
```

**Test Results:**
- Trace karo kisne job delete kiya ✅
- Audit logs mein user ka naam aur timestamp milna chahiye ✅

---

## 🌋 PHASE 10 & 11: The Resurrection & Invisibility (DR & SSL)

*Goal: Safety and Privacy.*

### 💾 Level 24: Time Machine (Backup)

**🎯 Mission:**
1. Backup JENKINS_HOME
2. Stop before backup (Consistency)
3. Practice restore
4. Validate credential recovery
5. Test job recovery

**🚩 Flag Verification:**
```bash
# Test 1: Backup create karo
sudo systemctl stop jenkins
tar -czf jenkins-backup.tar.gz /var/lib/jenkins/
sudo systemctl start jenkins

# Test 2: Disaster simulation
sudo rm -rf /var/lib/jenkins/*

# Test 3: Restore
tar -xzf jenkins-backup.tar.gz -C /
sudo systemctl restart jenkins

# Jenkins fully functional hona chahiye ✅
```

**Test Results:**
- Poora `/var/lib/jenkins` uda do, backup se restore karo ✅
- Saare jobs aur credentials wapas aa jayein ✅

---

### 💥 Level 25: Chaos Testing

**🎯 Mission:**
1. Fill disk (Test space handling)
2. Kill agent (Test reconnect)
3. Break credential (Test auth failure)
4. Break plugin (Test stability)
5. Recover system

**🚩 Flag Verification:**
```bash
# Test 1: Disk full simulation
dd if=/dev/zero of=/tmp/fillfile bs=1M count=10000

# Test 2: Build ke beech mein Agent kill karo
docker kill <agent-container-id>

# Jenkins "Aborted" dikhata hai ya "Retry" karta hai? ✅
```

**Test Results:**
- Dekho Jenkins "Aborted" dikhata hai ya "Retry" karta hai ✅
- Jenkins crash na ho (graceful degradation) ✅

---

### 🧥 Level 26: Nginx Reverse Proxy (SSL)

**🎯 Mission:**
1. Install nginx
2. Configure reverse proxy
3. Enable SSL (Self-signed ok)
4. Force HTTPS
5. Update Jenkins URL (System Config)
6. Test redirect behavior

**🚩 Flag Verification:**
```bash
# Test 1: HTTPS access
curl https://jenkins.yourdomain.com

# Test 2: HTTP redirect
curl -I http://jenkins.yourdomain.com
# Location: https://jenkins.yourdomain.com ✅

# Test 3: Direct port blocked
curl http://localhost:8080  # Connection refused ✅
```

**Test Results:**
- Jenkins ko `https://jenkins.yourdomain.com` pe setup karo ✅
- Browser mein Green Lock icon dikhna chahiye ✅

---

## 🏗️ PHASE 12, 13 & 14: The Architect's End Game (IaC)

*Goal: Fully automated Jenkins infrastructure.*

### 🔨 Level 27 & 28: Docker Dynamic Agents & Build Pipelines

**🎯 Mission (Level 27 - Docker Agents Dynamic):**
1. Install docker plugin
2. Configure docker cloud
3. Create templates
4. Run ephemeral agents
5. Verify cleanup

**🎯 Mission (Level 28 - Docker Build Pipelines):**
1. Build images in pipeline
2. Tag images (Build ID/Version)
3. Push to registry
4. Use credential auth
5. Verify registry push

**🚩 Flag Verification:**
```groovy
pipeline {
    agent any
    stages {
        stage('Build Image') {
            steps {
                script {
                    def img = docker.build("myapp:${BUILD_NUMBER}")
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-creds') {
                        img.push()
                    }
                }
            }
        }
    }
}
```

**Test Results:**
- Docker Hub pe naya tag `build-${BUILD_NUMBER}` dikhna chahiye ✅

---

### 📜 Level 29: JCasC (Config as Code)

**🎯 Mission:**
1. Install JCasC plugin
2. Export config yaml
3. Recreate Jenkins from yaml
4. Store config in Git
5. Practice rebuild from scratch

**🚩 Flag Verification:**
```yaml
# jenkins.yaml
jenkins:
  securityRealm:
    local:
      allowsSignup: false
      users:
       - id: "admin"
         password: "admin123"
  authorizationStrategy:
    globalMatrix:
      permissions:
        - "Overall/Administer:admin"
```

**Test Results:**
- Naya Jenkins container start karo us YAML ke saath—sab kuch pre-configured milna chahiye ✅
- Manual UI changes overwrite ho jayein YAML ke according ✅

---

### 🧙 Level 30: The Automation Wizard (Ansible)

**🎯 Mission:**
1. Provision Jenkins via Ansible
2. Install plugins via Ansible
3. Configure users via Ansible
4. Configure agents via Ansible
5. Rebuild infra automatically

**🚩 Flag Verification:**
```bash
# Test: Fresh VM pe playbook run karo
ansible-playbook -i inventory setup-jenkins.yml

# 10 minutes ke andar:
# - Jenkins installed ✅
# - Plugins installed ✅
# - Users created ✅
# - Agents configured ✅
```

**Test Results:**
- Zero manual steps. Fresh VM se fully working Jenkins 10 minute mein ✅
- Koi manual step na bacha ho ✅

---

## 🛡️ Your Final Quest Item:

Har phase ke baad **Definition of Done (DoD)** check karte rehna. Kyunki tum Docker agents use kar rahe ho, **Level 6** tumhara sabse bada turning point hoga.

**🎮 Game Rules:**
1. Har level ko sequence mein complete karo
2. Flag verification pass kiye bina aage mat badho
3. Horror stories se seekho (real production failures hain!)
4. Har level ka screenshot/log save karo (proof of completion)
5. Level 30 complete karne ke baad tum **Jenkins Architect** ban jaoge! 🏆

**Kya tum Level 0 (Hardening) se start karne ke liye ready ho?** 🚀

**Pro Tip:** Ek notebook maintain karo jisme har level ke commands aur learnings note karo. Ye tumhara personal Jenkins playbook banega! 📝
