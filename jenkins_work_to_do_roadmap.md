# 🟢 PHASE 0 — Infrastructure & OS Readiness

## 🔍 Level 0 — Linux Host Preparation & Hardening

### 1. The Concept - Kya Seekhoge?
Tum seekhoge **Linux server ko secure** kaise karte hain Jenkins ke liye. Sirf install karna kaafi nahi, **OS level hardening** zaroori hai.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Tumne Jenkins seedha `root` user pe chala diya. Hacker ne **pure server ka control** le liya.
**Real Production Scenario:** Server ka time sync nahi tha. Jenkins logs aur Git commits ka time match nahi hua, **audit logs corrupt** ho gaye.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Create dedicated non-root Jenkins OS user (`useradd jenkins`).
2.  Update OS packages (`apt update && apt upgrade`).
3.  Install Java LTS (JDK 17) (`apt install openjdk-17-jdk`).
4.  Install Git, curl, unzip utilities.
5.  Configure firewall (only SSH + Jenkins port allowed).
6.  Disable root SSH login (`PermitRootLogin no`).
7.  Disable password SSH login (use keys only).
8.  Configure time sync (`chrony` or `systemd-timesyncd`).
9.  Verify hostname + DNS resolution (`nslookup`, `hostname`).
10. Verify disk space + inode availability (`df -h`, `df -i`).

### 4. The Definition of Done - Success Kaise Check Karein?
✅ `root` user se SSH login attempt fail hona chahiye.
✅ Server time internet time se match karna chahiye (`timedatectl`).
✅ Disk space aur Inodes sufficient hone chahiye.

---

# 🟢 PHASE 1 — Jenkins Controller (Production Install)

## 🔍 Level 1 — Jenkins Install + Service Management

### 1. The Concept - Kya Seekhoge?
Jenkins ko **WAR file double click** karke nahi chalana hai. Tum seekhoge **Systemd service** ke through install karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Server restart hua. Jenkins **auto-start nahi hua**. **Deployment block** ho gaya.
**Real Production Scenario:** WAR file manually chalayi thi. Process crash hua aur **koi restart mechanism nahi tha**.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Install Jenkins via official package repo (not WAR for prod).
2.  Run Jenkins under systemd (`systemctl start jenkins`).
3.  Enable auto-start on boot (`systemctl enable jenkins`).
4.  Verify logs location (`/var/log/jenkins`).
5.  Change default port if conflict (`/etc/default/jenkins`).
6.  Verify restart behavior (Reboot server and check).
7.  Practice stop/start/restart safely (`systemctl restart jenkins`).

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Server reboot ke baad Jenkins automatically up aa jaye.
✅ `systemctl status jenkins` active (running) dikhaye.
✅ Logs specified location mein generate ho rahe hon.

## 🔍 Level 2 — JVM & Controller Performance Baseline

### 1. The Concept - Kya Seekhoge?
Jenkins Java pe chalta hai. Tum seekhoge **Heap Memory** configure karna taaki Jenkins **OOM (Out Of Memory)** ho kar crash na ho.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Bade builds chalne lage. Jenkins ne saari RAM kha li. **Server hang** ho gaya.
**Real Production Scenario:** GC logs nahi the. Performance slow thi par **pata nahi chala kyun**.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Configure heap memory limits (`-Xmx`, `-Xms`).
2.  Set min/max heap in `/etc/default/jenkins`.
3.  Enable GC logging (`-XX:+PrintGCDetails`).
4.  Learn how to capture thread dumps (`kill -3 <pid>`).
5.  Observe memory usage during builds (`htop`, `jstat`).
6.  Learn where OOM errors appear (Logs & System logs).

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Jenkins process defined memory limit se zyada consume na kare.
✅ GC logs aur Thread dumps generate kar paayein.
✅ OOM errors ko logs mein identify kar paayein.

---

# 🟢 PHASE 2 — Security Foundation (Must Before Pipelines)

## 🔍 Level 3 — Core Security Hardening

### 1. The Concept - Kya Seekhoge?
Jenkins ko public internet pe nanga nahi chhod sakte. Tum seekhoge **RBAC (Role Based Access Control)** aur **Anonymous access** band karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Kisi ne bina login kiye Jenkins khola aur **Production Delete Job** chala diya.
**Real Production Scenario:** Developer ko Admin access mil gaya galati se. Usne **global credentials chura liye**.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Enable security realm (local users).
2.  Disable anonymous access.
3.  Force login required.
4.  Install Folders plugin.
manually install plugins from github
5.  Install Matrix Authorization plugin.
6.  Create role-based access users (Admin, Dev, Viewer).
    --plugin install hone ke baad hee woh manage jenkins mai show karega to use that plugins

7.  Create folder-based isolation.
8.  Remove all permissions from anonymous.
9.  Disable controller executors (set to zero).

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Bina login kiye Jenkins URL open karne pe "Access Denied" aaye.
✅ Developer user Admin settings access na kar paaye.
✅ "Build Executor Status" mein Master pe 0 executors dikhne chahiye.

## 🔍 Level 4 — Credentials & Secret Governance

### 1. The Concept - Kya Seekhoge?
Password ko script mein likhna mana hai. Tum seekhoge **Jenkins Credentials Store** use karna aur secrets ko **mask** karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Tumne password Jenkinsfile mein hardcode kiya. Code GitHub pe push ho gaya. **Secrets leak** ho gaye.
**Real Production Scenario:** Console output mein password **plain text** mein print ho gaya.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Add SSH keys credentials.
2.  Add username/password credentials.
3.  Add secret text credentials.
4.  Add secret file credentials.
5.  Test masking behavior (Print secret in console).
6.  Practice credential scoping (global vs folder).
7.  Practice credential rotation (Update secret & test).
8.  Test broken credential behavior (Wrong key & verify fail).

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Console output mein secret values `****` dikhni chahiye.
✅ Credential sirf usi folder/job mein use ho jiske liye scope diya tha.
✅ Wrong credential use karne pe build fail ho.

## 🔍 Level 5 — Plugin Governance & Lifecycle (Often Ignored)

### 1. The Concept - Kya Seekhoge?
Plugins Jenkins ki taqat hain par vulnerability ka source bhi. Tum seekhoge **LTS version** use karna aur plugins ko **safely update** karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Tumne latest weekly version liya. Ek plugin incompatible tha. **Jenkins start hi nahi hua**.
**Real Production Scenario:** Update kiya aur backup nahi tha. **Configuration corrupt** ho gayi.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Learn LTS vs weekly Jenkins (Verify version).
2.  List installed plugins.
3.  Check dependency tree (Plugin Manager).
4.  Practice safe plugin upgrade.
5.  Take backup before upgrade.
6.  Upgrade one plugin only.
7.  Test rollback using backup.
8.  Remove unused plugins.
9.  Learn plugin pinning concept.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Sirf zaroori plugins install hon.
✅ Update ke baad Jenkins healthy rahe.
✅ Backup restore process successfully test ho chuka ho.

---

# 🟢 PHASE 3 — Controller–Agent Architecture

## 🔍 Level 6 — Static SSH Agents

### 1. The Concept - Kya Seekhoge?
Builds Master pe nahi chalenge. Tum seekhoge **alag VM (Agent)** ko SSH se connect karna aur wahan build run karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Master pe build chalne se CPU full ho gaya. **Jenkins UI slow** ho gayi.
**Real Production Scenario:** Agent pe build chala to Master **free raha** next job pick karne ke liye.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Create separate Linux agent VM.
2.  Install Java on agent.
3.  Create agent user.
4.  Configure SSH key auth (Master to Agent).
5.  Connect agent via SSH launcher.
6.  Label agent with capability labels.
7.  Run test pipeline on agent.
8.  Verify build never runs on controller.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Job configuration mein agent label lagane pe build Agent pe chale.
✅ Master pe koi build execute na ho.
✅ Agent log mein connection success dikhna chahiye.

## 🔍 Level 7 — Agent Security Model

### 1. The Concept - Kya Seekhoge?
Agent ko Master ka access nahi hona chahiye. Tum seekhoge **Agent-to-Controller Security** enforce karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Agent compromise ho gaya. Hacker ne Agent se Master pe **credentials read** kar liye.
**Real Production Scenario:** Agent ne Master ki file system access kar liya galati se.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Enable agent-to-controller security.
2.  Verify agent cannot read controller secrets.
3.  Learn inbound vs outbound agents.
4.  Restrict agent filesystem access.
5.  Test agent disconnect handling.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Agent se Master ki file access attempt fail ho.
✅ Security logs mein unauthorized access attempt record ho.
✅ Agent disconnect hone pe queue behavior observe ho.

## 🔍 Level 8 — Node Labeling & Routing Strategy

### 1. The Concept - Kya Seekhoge?
Sab agents same nahi hote. Tum seekhoge **Labels** (e.g., `linux`, `docker`) use karke jobs ko sahi jagah bhejna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Windows build Linux agent pe bhej diya. **Build fail** ho gaya.
**Real Production Scenario:** Heavy build chote agent pe chala. **OOM Kill** ho gaya.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Create capability-based labels (not names).
2.  Route jobs via labels.
3.  Practice multi-label selection.
4.  Build label strategy per tech stack.
5.  Avoid hard-binding to nodes.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Java job sirf Java agent pe chale.
✅ Node job sirf Node agent pe chale.
✅ Queue mein job atke nahi (sahi agent available ho).

---

# 🟡 PHASE 4 — Git & Triggering (Industry Setup)

## 🔍 Level 9 — Git Integration (Professional Setup)

### 1. The Concept - Kya Seekhoge?
Polling se load padta hai. Tum seekhoge **Webhooks** use karna taaki Git push hote hi Jenkins trigger ho.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Polling har minute check kar raha tha. Git server pe **unnecessary load** pada.
**Real Production Scenario:** Push hua par Jenkins ko 15 min baad pata chala. **Deployment delay** ho gaya.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Use SSH Git auth (not HTTPS passwords).
2.  Store Git credentials in Jenkins.
3.  Use Jenkinsfile from SCM.
4.  Disable polling.
5.  Configure webhooks (GitHub/GitLab).
6.  Validate webhook delivery logs.
7.  Simulate webhook failure (Block port & test).

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Git push karte hi 5 seconds mein Jenkins build start ho.
✅ Jenkins logs mein webhook delivery confirm ho.
✅ Webhook fail hone pe appropriate error log mile.

## 🔍 Level 10 — Multibranch Pipeline (Industry Standard)

### 1. The Concept - Kya Seekhoge?
Har branch ke liye alag job mat banao. Tum seekhoge **Multibranch Pipeline** jisse har branch apna pipeline automate kare.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** 50 feature branches hain. Tumne 50 jobs manually banaye. **Manage karna namumkin** ho gaya.
**Real Production Scenario:** Branch delete hui par Jenkins job wahi raha. **Cleanup nahi hua**.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Create multibranch job.
2.  Enable branch discovery.
3.  Enable PR discovery.
4.  Build feature branches automatically.
5.  Test branch deletion cleanup.
6.  Validate PR validation builds.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Nayi branch banate hi Jenkins pe automatically job dikhe.
✅ Branch delete karne pe kuch din baad Jenkins job auto-delete ho.
✅ PR raise karne pe validation build chale.

---

# 🟡 PHASE 5 — Pipeline Foundations

## 🔍 Level 11 — Declarative Pipeline Structure Mastery

### 1. The Concept - Kya Seekhoge?
Scripted pipeline purana ho gaya. Tum seekhoge **Declarative Pipeline** (`Jenkinsfile`) ka standard structure.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Pipeline complex thi. Kisi aur engineer ko **samajh nahi aayi**.
**Real Production Scenario:** Build hang ho gaya kyunki **timeout nahi tha**.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Use options block.
2.  Use environment block.
3.  Use post conditions (always, success, failure).
4.  Use timeouts.
5.  Use build discarder.
6.  Test timeout abort.
7.  Test build retention.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Pipeline syntax error free ho.
✅ 10 minute se zyada chalne pe build abort ho jaye.
✅ Build success/fail hone pe post actions execute hon.


### Level 11-B – Notifications & Reporting (Email/Slack)
Concept:
Build ke result ko team tak pahunchana – email aur Slack ke through.

Real-World Scenario:

Prod build fail hua, par kisi ko pata nahi chala. Bug live ho gaya.

Notification aate hi team ne fix kiya aur downtime kam hui.

Practical Tasks:

Email Extension Plugin install karo.

SMTP settings configure karo (Gmail SMTP example – App Password use karna).

Slack Plugin install karo aur Slack App create karo, token Jenkins credentials mein add karo.

Pipeline mein post block mein failure condition par email aur slack message bhejo.

Email ke liye emailext step use karo.

Slack ke liye slackSend step use karo.

Test karo ki failure par notification aata hai ya nahi.

unstable condition par alag notification bhejo.

Definition of Done:
✅ Build fail hone par Slack channel mein message aaye.
✅ Team ke email ID par failure mail aaye.
✅ Console logs mein secrets masked hon.
✅ Test reports (JUnit) publish ho kar UI mein dikhein.


## 🔍 Level 12 — Parameterized Pipelines

### 1. The Concept - Kya Seekhoge?
Hardcoded values hatao. Tum seekhoge **Parameters** (Environment, Version) leena taaki same pipeline alag alag jagah chale.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Dev aur Prod ke liye alag pipeline banayi. **Double maintenance** ho raha hai.
**Real Production Scenario:** Galati se Prod pe Dev config chala gaya. **Outage aa gaya**.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Add choice parameters.
2.  Add boolean parameters.
3.  Add string parameters.
4.  Drive environment selection via parameter.
5.  Test wrong input handling.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Build start karte waqt Environment select karne ka option aaye.
✅ Console log mein selected value print ho.
✅ Wrong input pe build gracefully fail ho ya warn kare.

## 🔍 Level 13 — Workspace & Build Isolation

### 1. The Concept - Kya Seekhoge?
Files idhar udhar failni nahi chahiye. Tum seekhoge **Workspace** manage karna aur purani files clean karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Disk full ho gayi kyunki **purane build artifacts** delete nahi huye.
**Real Production Scenario:** Ek build ki file dusre build ko affect kar rahi thi (**Collision**).

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Learn workspace layout.
2.  Use custom workspace.
3.  Avoid workspace collisions.
4.  Enable workspace cleanup.
5.  Test leftover file issues.
6.  Practice wipe workspace policies.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Build ke baad workspace folder empty ho jaye.
✅ Sirf last 5 builds ka history retain ho, purana delete ho.
✅ Leftover files se next build affect na ho.

---

# 🟡 PHASE 6 — Pipeline Optimization

## 🔍 Level 14 — Parallel Execution

### 1. The Concept - Kya Seekhoge?
Sequential builds slow hote hain. Tum seekhoge **Parallel Stages** chalana taaki build time kam ho.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Build 1 ghanta le raha tha. Developers **ghanto wait** kar rahe the.
**Real Production Scenario:** Tests parallel chale aur time **15 min** ho gaya.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Create parallel stages.
2.  Measure time difference.
3.  Handle parallel failure behavior.
4.  Combine with agents.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Stage view mein dono stages ek saath start hote dikhne chahiye.
✅ Total build duration sequential se kam ho.
✅ Ek parallel stage fail hone pe baaki ka behavior define ho.

## 🔍 Level 15 — Shared Libraries (DRY Pipelines)

### 1. The Concept - Kya Seekhoge?
Code repetition mat karo. Tum seekhoge **Shared Libraries** banao taaki common logic ek jagah rahe.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** 100 jobs hain. Security patch lagaana tha. **100 Jenkinsfile edit** karne pade.
**Real Production Scenario:** Shared library update ki, **saare jobs automatically secure** ho gaye.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Create shared library repo.
2.  Register global library.
3.  Load library in pipeline.
4.  Update library without job change.
5.  Version library usage.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Pipeline chote ho jayein (logic library mein ho).
✅ Library update karne pe bina job change kiye naya logic chale.
✅ Library versioning se stability maintain ho.


### Level 16-A – Running Jenkins Controller as Docker Container

Concept: Jenkins controller ko Docker container mein run karo, persistent volume ke saath, taaki easily upgrade/migrate kar sake.

Practical Tasks:

Docker install karo.

jenkins/jenkins:lts-jdk17 image pull karo.

Volume create karo: docker volume create jenkins_home

Container run karo with volume mount and port mapping.

Initial password retrieve karo aur login karo.

Container restart karo aur verify karo ki data safe hai.

Backup/restore volume ka practice karo.


## 🔍 Level 16B — Failure Handling & Classification

### 1. The Concept - Kya Seekhoge?
Har failure same nahi hoti. Tum seekhoge **Retry** karna aur **Unstable** mark karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Network glitch se build fail hua. **False alarm** gaya team ko.
**Real Production Scenario:** Test fail hua par build green raha. **Bug production chala gaya**.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Use retry strategy.
2.  Mark unstable builds.
3.  Catch and continue patterns.
4.  Differentiate infra vs test failures.
5.  Practice controlled failure flows.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Network error pe build automatically retry ho.
✅ Test fail hone pe build Yellow (Unstable) ho, Red (Failed) nahi.
✅ Infra failure vs Test failure alag treat ho.

## 🔍 Level 17 — Throttling & Queue Control

### 1. The Concept - Kya Seekhoge?
Ek saath 100 build na chalein. Tum seekhoge **Concurrency Limit** lagana taaki server overload na ho.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Sabne push kiya. 50 builds ek saath start huye. **Jenkins crash** ho gaya.
**Real Production Scenario:** Critical job queue mein atak gaya kyunki non-critical jobs chal rahi thin.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Install throttling plugin.
2.  Limit concurrent builds.
3.  Configure quiet period.
4.  Prevent queue storms.
5.  Test burst pushes.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ 10 trigger karne pe sirf 2 ek saath chalein, baaki queue mein hon.
✅ Quick successive pushes pe sirf ek build execute ho.
✅ Queue storm na bane.

---

# 🟠 PHASE 7 — Release Flow & Promotion

## 🔍 Level 18 — Promotion & Approval Gates

### 1. The Concept - Kya Seekhoge?
Prod deploy automatic nahi hota. Tum seekhoge **Manual Approval** gate lagana.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Friday shaam ko automatic deploy hua. **Site down** ho gayi. Koi approve karne wala nahi tha.
**Real Production Scenario:** Manager ne pipeline mein **approve button dabaya**, tabhi deploy hua.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Add manual approval gates.
2.  Build → stage → prod flow.
3.  Add environment promotion.
4.  Test approval rejection path.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Build 'Deploy-Prod' stage pe ruk jaye.
✅ Jenkins UI pe "Proceed" button aaye.
✅ Reject karne pe build abort ho jaye.

## 🔍 Level 19 — Locking & Resource Control

### 1. The Concept - Kya Seekhoge?
Do deploy ek saath na hon. Tum seekhoge **Lockable Resources** use karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Do teams ne ek saath deploy kiya. **Database lock conflict** hua. Data corrupt hua.
**Real Production Scenario:** Pehla build complete hua, dusra queue mein wait kiya. **Safe deploy** hua.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Configure lockable resources.
2.  Prevent concurrent deploy.
3.  Test queue waiting behavior.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Do jobs ek saath trigger karne pe ek wait kare jab tak dusra lock release na kare.
✅ Console log mein "Waiting for lock" message dikhe.

---

# 🟠 PHASE 8 — Artifacts & Storage

## 🔍 Level 20 — Artifact Strategy

### 1. The Concept - Kya Seekhoge?
Build files ko save kaise rakhein. Tum seekhoge **Archive Artifacts** aur **Stash/Unstash**.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Build pass hua par **JAR file gayab** thi deploy ke waqt.
**Real Production Scenario:** Ek stage mein bana file dusre stage mein **access nahi ho pa raha** tha (different agent).

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Archive artifacts.
2.  Configure retention.
3.  External artifact storage (e.g., S3/Nexus).
4.  Stash/unstash across stages.
5.  Test artifact reuse.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Build page pe "Artifacts" section mein file downloadable ho.
✅ Stage A ki file Stage B mein successfully use ho.
✅ External storage mein artifact upload ho.

---

# 🟠 PHASE 9 — Observability & Debugging

## 🔍 Level 21 — Debugging & Replay

### 1. The Concept - Kya Seekhoge?
Pipeline fail hui to kya karoge? Tum seekhoge **Replay Feature** aur Logs analyze karna bina code push kiye.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Choti si typo thi. Fix karne ke liye **Git push karna pada**. 10 baar try kiya, 10 push huye. **Git history kharab**.
**Real Production Scenario:** "Replay" se change kiya, test kiya, **phir commit kiya**. Clean history raha.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Use replay feature.
2.  Read console logs deeply.
3.  Read system logs.
4.  Capture thread dump.
5.  Analyze failed pipeline stages.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Bina Git commit kiye pipeline fix ho kar chal jaye.
✅ Error ka root cause log mein mil jaye.
✅ Thread dump analyze kar paayein.

## 🔍 Level 22 — Monitoring & Metrics

### 1. The Concept - Kya Seekhoge?
Jenkins healthy hai ya nahi? Tum seekhoge **Prometheus Metrics** enable karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Jenkins slow tha par pata nahi chala kyunki **monitoring nahi thi**.
**Real Production Scenario:** Queue size badh raha tha. Alert mila aur **pehle hi agent add kar liye**.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Enable Prometheus metrics.
2.  Monitor queue size.
3.  Monitor executor usage.
4.  Monitor memory.
5.  Monitor build duration trends.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Metrics endpoint se data fetch ho (queue size, job duration).
✅ Build time trends observe ho sakein.
✅ Executor usage graph dikhe.

## 🔍 Level 23 — Audit & Compliance

### 1. The Concept - Kya Seekhoge?
Kiske ne kya change kiya? Tum seekhoge **Audit Trail** maintain karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Config change hui. Pata nahi **kisne ki**. Security compliance fail ho gaya.
**Real Production Scenario:** Audit log mein saara record tha. **Forensics** easy hua.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Install audit trail plugin.
2.  Track config changes.
3.  Track user actions.
4.  Review audit logs.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Log file mein user creation ka entry ho (Who, When, What).
✅ Config change ka record mile.
✅ User actions track ho rahe hon.

---

# 🔴 PHASE 10 — Backup & Disaster Recovery

## 🔍 Level 24 — Backup Strategy

### 1. The Concept - Kya Seekhoge?
Jenkins mar sakta hai. Tum seekhoge **JENKINS_HOME** ka backup lena aur restore karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Server crash hua. **Backup nahi tha**. 2 saal ki configuration gayab. **Naya setup karne mein 1 hafta laga**.
**Real Production Scenario:** Backup tha. **1 ghante mein wapas aa gaya**.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Backup JENKINS_HOME.
2.  Stop before backup (Consistency).
3.  Practice restore.
4.  Validate credential recovery.
5.  Test job recovery.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Restore ke baad saare jobs aur credentials wapas aa jayein.
✅ Backup file corrupt na ho.
✅ Credentials decrypt ho kar chal rahe hon.

## 🔍 Level 25 — Chaos Testing

### 1. The Concept - Kya Seekhoge?
System todna seekho taaki bachana aa jaye. Tum seekhoge **Intentional Failures** create karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Disk full hui. Jenkins ne **behave kaise kiya**? Pata nahi kyunki kabhi test nahi kiya.
**Real Production Scenario:** Agent network gaya. Jenkins ne **retry kiya ya hang hua**?

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Fill disk (Test space handling).
2.  Kill agent (Test reconnect).
3.  Break credential (Test auth failure).
4.  Break plugin (Test stability).
5.  Recover system.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Jenkins crash na ho (graceful degradation).
✅ Logs mein clear error message ho.
✅ Recovery process documented ho.

---

# 🔴 PHASE 11 — Reverse Proxy & SSL

## 🔍 Level 26 — Nginx Reverse Proxy

### 1. The Concept - Kya Seekhoge?
Jenkins ko direct expose mat karo. Tum seekhoge **Nginx** ke peeche chupana aur **SSL (HTTPS)** lagana.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** HTTP pe password bheja. **Network sniffing** se password chura liya gaya.
**Real Production Scenario:** Nginx ne load balance kiya aur **SSL terminate** kiya. Jenkins secure raha.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Install nginx.
2.  Configure reverse proxy.
3.  Enable SSL (Self-signed ok).
4.  Force HTTPS.
5.  Update Jenkins URL (System Config).
6.  Test redirect behavior.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Browser mein `http://` access karne se `https://` pe redirect ho.
✅ Lock icon (Secure) dikhe.
✅ Jenkins direct port (8081) firewall se block ho, sirf Nginx (443) khula ho.

---

# 🟣 PHASE 12 — Docker Integration (Industry CI)

## 🔍 Level 27 — Docker Agents (Dynamic)

### 1. The Concept - Kya Seekhoge?
Static agents manage karna mushkil hai. Tum seekhoge **Docker Containers** ko temporary agent banana.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Agent pe purani libraries thin. **Conflict hua**.
**Real Production Scenario:** Build khatam hua, **container delete** ho gaya. Saaf sutra environment.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Install docker plugin.
2.  Configure docker cloud.
3.  Create templates.
4.  Run ephemeral agents.
5.  Verify cleanup.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Build start hote hi naya container create ho.
✅ Build end hote hi container remove ho jaye.
✅ Templates se sahi image pull ho.

## 🔍 Level 28 — Docker Build Pipelines

### 1. The Concept - Kya Seekhoge?
Sirf code build nahi, **Image Build** bhi automate karna hai.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Manual image build ki. **Tag galat laga**. Prod pe purani image chali gayi.
**Real Production Scenario:** Pipeline ne image banayi, scan kiya, **registry pe push kiya**.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Build images in pipeline.
2.  Tag images (Build ID/Version).
3.  Push to registry.
4.  Use credential auth.
5.  Verify registry push.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Docker Registry pe nayi image dikhe.
✅ Image tag build number se match kare.
✅ Credentials securely use huye hon.

---

# 🟣 PHASE 13 — Jenkins Config as Code (Industry Infra-as-Code)

## 🔍 Level 29 — JCasC (Jenkins Configuration as Code)

### 1. The Concept - Kya Seekhoge?
UI pe click karke config karna bhool jao. Tum seekhoge **YAML file** se Jenkins configure karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Jenkins server gaya. **Manual setup kiya**. 2 din lage aur **config miss ho gayi**.
**Real Production Scenario:** YAML file thi. **Naya Jenkins 10 min mein khada ho gaya**. Exact same config.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Install JCasC plugin.
2.  Export config yaml.
3.  Recreate Jenkins from yaml.
4.  Store config in Git.
5.  Practice rebuild from scratch.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Jenkins restart ke baad YAML se config load ho.
✅ Manual UI changes overwrite ho jayein YAML ke according.
✅ Fresh install pe YAML se full setup ho jaye.

---

# ⚫ PHASE 14 — Automation Around Jenkins (LAST)

## 🔍 Level 30 — Ansible for Jenkins (Only After Mastery)

### 1. The Concept - Kya Seekhoge?
Ab jab Jenkins samajh aa gaya, to install automate karo. Tum seekhoge **Ansible Playbook** se Jenkins setup karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** 10 Jenkins agents chahiye the. **Manual install kiya**. 10 ghante lage.
**Real Production Scenario:** Ansible play book chalaya. **10 min mein 10 agents ready**.

### 3. The Practical Task - Kya Karna Hai? (v2 Verified)
1.  Provision Jenkins via Ansible.
2.  Install plugins via Ansible.
3.  Configure users via Ansible.
4.  Configure agents via Ansible.
5.  Rebuild infra automatically.

### 4. The Definition of Done - Success Kaise Check Karein?
✅ Fresh VM pe playbook run karne se fully functional Jenkins aa jaye.
✅ Koi manual step na bacha ho.
✅ Users aur Agents automatically configure hon.

---

# ✅ Final Verification Complete

Pawan, maine **Roadmap v2** ke har ek bullet point ko is detailed format mein map kar diya hai.
*   **Total Levels:** 30
*   **Total Phases:** 14
*   **Format:** Concept, Why, Task, DoD (Hinglish)
*   **Coverage:** 100% Aligned with v2 Outline.

Ab tumhare paas **Industry-Grade Execution Blueprint** hai. Koi step miss nahi hua hai.

all remembered