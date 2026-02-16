# 🚀 Docker Production Mastery Roadmap (Final Version)

## 🧩 Module 1: The Consumer (Basics & Lifecycle)

### 1. The Concept
Container lifecycle management (Pull, Run, Stop, Start, Remove) aur flags ka sahi use.

### 2. Why & Learning Outcome (Real-World Scenario)
*   **Kyun Zaroori Hai?** Production mein servers restart hote hain, containers crash hote hain. Tumhe pata hona chahiye ki container ko bina delete kiye kaise restart karein aur temporary containers ko kaise clean karein.
*   **Learning:** Tumhe `docker ps`, `docker stop`, `docker start` aur `--rm` flag ka difference samajh aayega.
*   **Agar Nahi Kiya:** Tum har baar naya container banaoge, purane containers disk space khayenge, aur server slow ho jayega.
*   **Problem Solved:** Resource wastage kam karna aur container state ko manage karna.

### 3. Practical Tasks
1.  **Nginx image pull karo** – official image `nginx` ka tag `alpine` use karo.
2.  **Container run karo** – background mode mein, host ke port 8080 ko container ke port 80 se map karo, container ka naam `my-nginx` rakho.
3.  **Verify karo** – browser ya `curl` se `http://localhost:8080` hit karo. "Welcome to nginx" page aana chahiye.
4.  **Running containers list karo** – command se dekho ki tumhara container running hai.
5.  **Container stop karo** – `my-nginx` container ko stop karo.
6.  **Container ko phir se start karo** – use `docker start`.
7.  **Container remove karo** – pehle stop karo, phir remove karo.
8.  **`--rm` flag ka istemal** – ek naya container run karo `--rm` flag ke saath, phir use stop karo (ya exit karo). Container automatically remove hona chahiye.
9.  **Container Changes dekho** – `docker diff` command ka use karke dekho ki container ke andar kya files change hui hain run time pe.

### 4. Definition of Done
*   `docker ps -a` mein saare containers ke status sahi hain.
*   `--rm` wala container stop/exit karne ke baad `docker ps -a` mein nahi dikhna chahiye.
*   `docker diff` se file changes detect kar paaye.

----======Done upto above=========
## 🧩 Module 2: The Builder (Custom Images, Tagging & Registry) [UPDATED]
1. The Concept
Custom Docker images banana, optimize karna, aur Remote Registry par manage karna.
2. Why & Learning Outcome (Real-World Scenario)
Kyun Zaroori Hai? Company ka code private hota hai. Images ko version karna zaroori hai taaki pata chale kaunsa code deploy hua hai.
Learning: Dockerfile best practices (COPY vs ADD, ARG vs ENV, Exec vs Shell form).
Agar Nahi Kiya: ADD use karne se security risk ho sakta hai. ENV mein password dalne se wo leak ho sakta hai. Shell form use karne se signals handle nahi honge.
Problem Solved: Secure aur reproducible builds.
3. Practical Tasks
Project folder banayein – my-app naam se.
Ek simple web application likhein – Python (Flask) ya Node.js (Express) mein. Ye "Hello Docker" return kare.
.dockerignore file banayein – usme node_modules, .git, .env add karo.
Dockerfile likhein – base image ke liye Alpine version use karo.
COPY vs ADD – Files copy karne ke liye hamesha COPY instruction use karo, ADD nahi (jab tak tar extract na karna ho).
ARG vs ENV – Build time variables ke liye ARG use karo, runtime ke liye ENV. Sensitive data (password) ko ARG mein bhi mat dalna agar image public hai.
WORKDIR set karo – WORKDIR instruction ka use karo, cd command ka nahi.
ENTRYPOINT vs CMD (Exec Form) – Dockerfile mein ENTRYPOINT aur CMD ko hamesha Exec Form (["command", "param"]) mein likho, Shell form (command param) mein nahi. Ye PID 1 aur signals ke liye zaroori hai.
Image build karo – tag do my-app:v1.
Container run karo – port mapping ke saath, aur check karo ki app sahi chal rahi hai.
Image layers inspect karo – docker history se dekho ki har layer ka size kya hai.
Semantic Versioning Apply karo – Image ko latest ki jagah specific version (e.g., v1.0.0) aur commit hash ke saath tag karo.
Registry Push karo – Docker Hub ya GitHub Container Registry par login karo aur apni image ko push karo.
Verify Remote Image – Browser se registry par jakar verify karo ki image upload hui hai.
4. Definition of Done
Build successful ho.
COPY use hua hai ADD ki jagah.
CMD/ENTRYPOINT Exec Form ([]) mein hai.
Image remote registry par visible ho aur tag mein version number ho.

---===============Done upto above ===========

## 🧩 Module 3: The Data Manager (Persistence & Backup)

### 1. The Concept - Kya Seekhoge?
Tum seekhoge ki **container ka data permanently kaise save** karte hain (Volumes/Bind Mounts use karke) aur **disaster recovery** kaise karte hain (Backup aur Restore strategy).

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Containers **temporary** hote hain - matlab jab container delete hota hai, uska **saara data bhi delete** ho jata hai. Agar tumhara **database container** crash ho gaya aur **volume nahi tha**, toh **saara customer data permanently loss** ho jayega.

**Seekhoge:**
- **Bind Mount:** Development ke liye - jab tumhe **live code changes** dekhne hain
- **Named Volume:** Production databases ke liye - **permanent storage**
- **Backup/Restore:** Disaster se bachne ke liye - **data recovery strategy**

**Agar ye nahi kiya:** Production mein database crash hua toh **company ka saara data kho jayega** - ye **career-ending mistake** ho sakti hai.

### 3. Practical Tasks - Step by Step Kya Karna Hai

#### Bind Mount (Dev Mode)
**Task 1: Bind Mount Ke Saath Container Chalana**
- Tumhe apne **laptop/PC ki current directory** ko container ke **andar ek folder** se **connect** karna hai
- Ye **real-time sync** hota hai - **host pe change karo**, container mein **turant reflect** hoga
- Container ke **andar `/app` folder** (ya jahan tumhara code hai) ko **host directory se map** karna hai
- Ye **development ke liye perfect** hai kyunki **code edit karke turant test** kar sakte ho

**Task 2: Live Code Change Test Karna**
- Apne **code editor** mein file open karo
- **"Hello Docker" message** ko kuch aur mein **change** karo (jaise "Hello World Modified")
- File **save** karo

**Task 3: Change Verify Karna**
- **Browser refresh** karo ya container **restart** karo
- Tumhe **naya message** dikhna chahiye
- Ye **prove** karta hai ki **host aur container** ke beech **live connection** hai

#### Named Volume (Database)
**Task 4: Named Volume Create Karna**
- Docker mein ek **special storage area** banana hai
- Iska naam **`mysql-data`** rakhna hai
- Ye **container se independent** hota hai - container delete ho jaye, **volume safe** rahega

**Task 5: MySQL Container Volume Ke Saath Run Karna**
- **MySQL 8.0 image** use karni hai
- Volume ko MySQL ke **data directory** (`/var/lib/mysql`) se **attach** karna hai
- **Root password** set karna hai environment variable se
- Ab MySQL ka **saara data** is volume mein **save** hoga

**Task 6: Database Mein Data Dalna**
- Container ke **andar MySQL client** se **connect** karna hai
- Ek **database** banana hai (jaise `testdb`)
- Ek **table** banana hai (jaise `users`)
- Kuch **sample data insert** karna hai (2-3 rows)

**Task 7: Container Ko Force Delete Karna**
- Container ko **forcefully remove** karna hai
- Normally ye **data loss** ka reason hota hai
- Lekin **volume hai**, toh data **safe** rahega

**Task 8: Same Volume Ke Saath Naya Container Start Karna**
- **Bilkul naya container** start karna hai
- Lekin **same volume attach** karna hai
- Ye **production scenario** simulate karta hai jab container **crash** hoke **restart** hota hai

**Task 9: Data Persistence Verify Karna**
- Naye container mein **MySQL client** se connect karo
- **Database list** dekho - `testdb` **exist** karna chahiye
- **Table query** karo - tumhara **purana data** wapas **mil jayega**
- Ye **proof** hai ki data **persistent** hai

#### Backup & Restore (Critical)
**Task 10: Volume Ka Backup Lena**
- Ek **temporary container** start karna hai jo **volume ko mount** kare
- Volume ke **saare contents** ko ek **`.tar` file** mein **compress** karna hai
- Ye **tar file** tumhare **host machine** par save hogi
- Ye **production backup strategy** hai

**Task 11: Original Volume Delete Karna**
- **Disaster simulation** ke liye original volume ko **permanently remove** karna hai
- Normally ye **panic situation** hoti hai
- Lekin tumhare paas **backup** hai

**Task 12: Backup Se Data Restore Karna**
- **Naya volume** create karna hai
- **Backup tar file** se data ko **extract** karke **naye volume** mein dalna hai
- Ye **disaster recovery process** hai

**Task 13: Restore Verify Karna**
- Naye volume ke saath **fresh container** start karo
- MySQL mein **login** karo
- **Database aur table** check karo
- **Saara purana data** wapas aa gaya hona chahiye
- Ye **successful disaster recovery** hai

### 4. Definition of Done - Kaise Pata Chalega Success Hua?
- **Bind mount** mein code change **instantly reflect** hua
- **Volume delete** ke baad bhi data **safe** raha (persistence verified)
- **Backup file successfully** bani aur **restore** karne par **complete data recovery** hui

---

## 🧩 Module 4: The Network Engineer (Communication)

### 1. The Concept - Kya Seekhoge?
Tum seekhoge ki **containers ek dusre se kaise communicate** karte hain, **DNS resolution** kaise kaam karta hai, aur **service discovery** kya hoti hai (containers ko **naam se** kaise dhundhe).

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Modern applications **microservices** hote hain - **frontend, backend, database, cache** sab **alag containers** mein chalte hain. Inhe **ek dusre se baat** karni padti hai. Lekin **IP addresses dynamic** hote hain - **container restart** hone par **IP change** ho jata hai.

**Problem:** Agar tumne code mein **IP address hardcode** kar diya (jaise `192.168.1.5`), aur wo container **restart** hua, toh **naya IP** mil jayega aur tumhara **app crash** ho jayega.

**Solution:** Docker **automatic DNS** provide karta hai - tum **container name** use kar sakte ho (jaise `redis-cache`), aur Docker **automatically IP resolve** kar dega.

**Agar ye nahi kiya:** Production mein **database restart** hua toh **saari services crash** ho jayengi kyunki **IP change** ho gaya.

### 3. Practical Tasks - Step by Step Kya Karna Hai

**Task 1: Custom Bridge Network Banana**
- Docker mein ek **isolated network** create karna hai
- Naam **`my-net`** rakhna hai
- Ye **private network** hoga jisme sirf **tumhare containers** honge
- **Default bridge** se better hai kyunki **automatic DNS** milta hai

**Task 2: Redis Container Network Mein Run Karna**
- **Redis** (in-memory cache/database) container start karna hai
- Use **`my-net` network** mein run karna hai
- Container ka naam **`redis-cache`** rakhna hai
- Ye naam hi **hostname** ban jayega (DNS ke liye)

**Task 3: Application Mein Redis Client Add Karna**
- Apne **Python/Node.js app** mein **Redis client library** install karna hai
- Code mein **Redis se connect** karne ka logic likhna hai
- **Important:** Connection string mein **IP nahi**, **hostname** use karo: **`redis-cache`**
- Dockerfile update karke **naya image build** karo: **`my-app:v2`**

**Task 4: App Container Same Network Mein Run Karna**
- Apni app ka container **start** karna hai
- **Same network** (`my-net`) mein run karna hai
- Ab **dono containers** (app aur Redis) **same network** mein hain

**Task 5: Connection Logs Verify Karna**
- App container ke **logs** dekhne hain
- Logs mein **"Connected to Redis"** ya similar message **dikhna** chahiye
- Agar **error** hai toh **connection fail** hua

**Task 6: Network Details Inspect Karna**
- Network ko **inspect** karne se **details** milti hain
- Tumhe **dono containers ke IP addresses** dikhenge
- Ye **educational** hai - samajhne ke liye ki **internally kya ho raha** hai

**Task 7: Container Se Container Ko Ping Karna**
- App container ke **andar shell** open karna hai
- Usme se **`redis-cache`** ko **ping** karna hai (hostname se)
- Ping **successful** hona chahiye
- Ye **prove** karta hai ki **DNS resolution** kaam kar raha hai

**Task 8: Network Aliases Test Karna**
- Container ko **multiple names** (aliases) de sakte ho
- Network create karte waqt **alias** add karo
- Verify karo ki **alias se bhi** connect ho raha hai
- Ye **flexibility** deta hai - ek service ko **multiple names** se access karo

**Task 9: External Network Use Karna**
- Pehle se **existing network** ko compose file mein **reference** karna hai
- `external: true` flag use karna hai
- Ye **production scenario** hai jab **shared network** use karte hain

### 4. Definition of Done - Kaise Pata Chalega Success Hua?
- App **Redis se successfully connect** hua **bina IP hardcode** kiye (hostname se)
- **Ping successful** raha (DNS working)
- **Network alias** se bhi resolution ho raha hai (flexibility verified)

---

## 🧩 Module 5: The Orchestrator (Compose & Overrides)

### 1. The Concept - Kya Seekhoge?
Tum seekhoge ki **multiple containers** ko ek saath kaise manage karte hain (**Docker Compose** use karke) aur **different environments** (Dev, Staging, Production) ke liye **alag-alag configurations** kaise maintain karte hain.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Real applications mein **10-20 containers** hote hain - **frontend, backend, database, cache, message queue, monitoring** sab alag. Inhe **manually ek-ek karke** run karna **impossible** hai aur **error-prone** bhi.

**Problem 1:** Har container ke liye **alag command** yaad rakhna aur **sahi order** mein run karna
**Problem 2:** **Development** mein alag settings chahiye (debug mode, local ports), **Production** mein alag (security, resource limits)

**Solution:** Docker Compose - ek **YAML file** mein **saari services define** karo, aur **ek command** se sab kuch **start/stop** karo.

**Agar ye nahi kiya:** 
- **Manual deployment** mein **human error** hoga (koi container bhool gaye)
- **Dev config** production mein chali jayegi (**security risk** - debug ports open rahenge)

### 3. Practical Tasks - Step by Step Kya Karna Hai

**Task 1: Docker Compose File Banana**
- Project folder mein **`docker-compose.yml`** file create karni hai
- **Version 3.8** specify karna hai (latest stable)
- Ye file **blueprint** hai - isme **saari services** define hongi

**Task 2: Services Define Karna**
- **Redis service:** Ready-made image use karo (`redis:alpine`)
- **App service:** Tumhari custom image build hogi (Dockerfile se)
- **Ports expose** karo (jaise 8080:5000)
- **Environment variables** set karo (`REDIS_HOST=redis-cache`)
- Compose **automatically network** bana dega - **DNS** bhi kaam karega

**Task 3: Healthcheck Dependency Add Karna**
- **`depends_on`** mein **condition** add karna hai: `service_healthy`
- Matlab **app tab start** hoga jab **Redis ready** ho
- Ye **production-grade** hai - **premature connections** se bachata hai
- Redis mein **healthcheck** define karna padega

**Task 4: .env File Banana**
- **Sensitive data** (passwords, API keys) ko **code mein nahi** rakhna
- **`.env` file** mein rakhna hai
- Compose file mein **reference** karna hai: `${DB_PASSWORD}`
- Ye **security best practice** hai

**Task 5: App Code Mein Environment Variable Use Karna**
- Code mein **hardcoded values** nahi
- **Environment variable** se read karo (Python: `os.getenv('REDIS_HOST')`)
- Ye **flexibility** deta hai - **config change** karne ke liye **code change** nahi karna padega

**Task 6: Stack Start Karna**
- **Ek command** se **saari services** start hongi
- **Background mode** mein run hoga
- **Network, volumes** sab **automatically** create honge

**Task 7: Logs Dekhna**
- **Specific service** ke logs dekh sakte ho
- **Real-time** logs stream hote hain
- **Debugging** ke liye useful

**Task 8: Stack Stop Karna**
- **Ek command** se **saari services** stop hongi
- **Containers remove** honge
- **Volumes safe** rahenge (data loss nahi hoga)

**Task 9: Volume Compose Mein Add Karna**
- Redis ke liye **named volume** define karna hai
- **Data persistence** ke liye
- Compose file mein **volumes section** mein declare karo

**Task 10: Development Override File Banana**
- **`docker-compose.override.yml`** file create karo
- Ye **automatically merge** hoti hai main file ke saath
- Isme **dev-specific** config rakho (debug ports, volume mounts for live reload)

**Task 11: Production Config Alag Rakhna**
- **`docker-compose.prod.yml`** file banao
- Isme **production settings** rakho (resource limits, restart policies, security)
- **Explicitly specify** karna padega run karte waqt

**Task 12: Profiles Use Karna**
- **Optional services** (monitoring, debugging tools) ko **profile** assign karo
- **Selective start** kar sakte ho - sirf **needed services**
- **Resource efficient**

### 4. Definition of Done - Kaise Pata Chalega Success Hua?
- **Ek command** se puri stack start ho rahi hai
- App **Redis se connect** ho raha hai (healthcheck ke baad hi start hua)
- **Down** karne par containers remove hote hain, **volume nahi** (data safe)
- **Dev aur Prod** ke liye **alag configuration** successfully load ho rahi hai
- **`.env` file** se variables properly load ho rahe hainaaki app tab start ho jab Redis ready ho.
4.  **.env File Banayein** – Sensitive variables (passwords) aur config ko `.env` file mein rakho aur compose mein refer karo.
5.  **App code mein Redis host environment variable se lo** – `os.getenv('REDIS_HOST')` ya equivalent.
6.  **Compose se stack start karo** – `docker-compose up -d`.
7.  **Logs dekho** – `docker-compose logs app`.
8.  **Stack stop karo** – `docker-compose down`.
9.  **Volume add karo compose mein** – Redis ke liye named volume, taaki data persist ho.
10. **Compose Overrides Setup karo** – `docker-compose.override.yml` banao jo sirf Development ke liye ho (e.g., debug ports open karna).
11. **Prod Config alag rakho** – `docker-compose.prod.yml` banao jisme resource limits aur security config ho.
12. **Profiles Use karo** – Optional services (jaise monitoring tools) ko profiles ke through control karo.

### 4. Definition of Done
*   Stack ek command se start ho.
*   App Redis se connect ho (healthcheck ke baad).
*   Down karne par containers remove hon, volume nahi.
*   Dev aur Prod ke liye alag configuration load ho rahi hai.
*   `.env` file se variables load ho rahe hain.

---
## 🧩 Module 6: The Optimizer (Production Grade Builds & Multi-Arch) [UPDATED]
1. The Concept
Image size reduction, Build Speed, aur Cross-Platform Compatibility.
2. Why & Learning Outcome (Real-World Scenario)
Kyun Zaroori Hai? Badi images deploy hone mein time leti hain. Agar tumhara laptop M1 hai aur server Linux hai, toh normal build fail ho sakta hai.
Learning: Multi-stage builds, BuildKit, Layer Caching, Docker Buildx.
Agar Nahi Kiya: Deployment slow hoga, architecture mismatch ayega.
Problem Solved: Faster CI/CD pipelines aur portable images.
3. Practical Tasks
BuildKit Enable karo – Build shuru karne se pehle export DOCKER_BUILDKIT=1 set karo taaki advanced caching features use kar sako.
Current image size check karo – my-app:v1 ka size dekho.
Multi-stage Dockerfile likho – Stage 1 (builder) mein dependencies install karo, Stage 2 (final) mein sirf built artifacts copy karo, aur chhoti base image (alpine ya distroless) use karo.
Nayi image build karo – tag my-app:optimized.
Size compare karo – purani aur nayi image ka size.
Layer caching optimize karo – Dockerfile mein pehle sirf dependency files copy karo, phir RUN install, phir baaki code copy karo.
(Advanced) Distroless image use karo – final stage ko gcr.io/distroless/python3 se replace karo. Build karo aur run karo.
Multi-Architecture Build karo – docker buildx ka use karke image ko linux/amd64 aur linux/arm64 dono ke liye build karo.
Push Multi-arch Image – Is image ko registry par push karo aur verify karo ki manifest list create hua hai.
4. Definition of Done
Image size noticeably reduced.
BuildKit enable tha build ke waqt.
Multi-stage build successful.
Image alag-alag architecture (Mac vs Linux) par bina error ke run ho rahi hai.

---

## 🧩 Module 7: The Security Guard (Non‑Root & Read-Only)

### 1. The Concept
Container Security Hardening aur Privilege Restriction.

### 2. Why & Learning Outcome (Real-World Scenario)
*   **Kyun Zaroori Hai?** Default containers `root` user pe chalte hain. Agar hacker container compromise karle, toh usse host machine ka root access mil sakta hai.
*   **Learning:** Non-root users, Read-only filesystems, Tmpfs.
*   **Agar Nahi Kiya:** Security breach ki situation mein attacker poora server hack kar sakta hai.
*   **Problem Solved:** Attack surface kam karna aur Privilege Escalation rokna.

### 3. Practical Tasks
1.  **Dockerfile mein non-root user add karo** – `addgroup` aur `adduser` (ya equivalent) commands se. User ka UID/GID 1000 rakho.
2.  **Files copy karte time `--chown` use karo** – taaki files naye user ke paas ho.
3.  **`USER` instruction set karo** – naye user par switch karo.
4.  **Image build karo** – tag `my-app:secure`.
5.  **Container run karo**.
6.  **Inside container user verify karo** – `whoami` aur `id` commands se.
7.  **Privileged operation try karo** – jaise package install karna (e.g., `apk add curl`) – permission denied aana chahiye.
8.  **Read-Only Filesystem** – Container run karte waqt `--read-only` flag use karo (agar app ko write access nahi chahiye).
9.  **Tmpfs for Writes** – Agar app ko temporary write access chahiye, toh `tmpfs` mount karo.

### 4. Definition of Done
*   Container non-root user se chal raha hai.
*   User root nahi hai, isliye package install nahi kar sakta.
*   Container filesystem read-only hai (security hardening).

---

## 🩺 Module 8: The Operator (Healthchecks, Logs & Pruning)

### 1. The Concept
Reliability, Observability, aur Maintenance.

### 2. Why & Learning Outcome (Real-World Scenario)
*   **Kyun Zaroori Hai?** Application hang ho sakti hai bina crash huye. Logs disk full kar sakte hain. Timezone mismatch se debugging mushkil hoti hai.
*   **Learning:** Healthchecks, Log Rotation, Timezone Management, System Pruning.
*   **Agar Nahi Kiya:** Downed service detect nahi hogi. Server disk full hokar crash hoga. Logs ka time samajh nahi aayega.
*   **Problem Solved:** Self-healing systems aur maintainable infrastructure.

### 3. Practical Tasks
1.  **Dockerfile mein `HEALTHCHECK` add karo** – interval 30s, timeout 3s, start-period 5s, retries 3. Command curl se app ke root endpoint ko check karo.
2.  **Image build karo** – `my-app:health`.
3.  **Container run karo**.
4.  **Health status check karo** – `docker ps` mein `(healthy)` dikhna chahiye. `docker inspect` se health details dekho.
5.  **Restart policy test karo** – container ko `--restart=on-failure:3` ya compose mein `restart: unless-stopped` ke saath run karo. Container ke andar process kill karo. Dekho ki container restart hota hai.
6.  **Log Rotation Configure karo** – Compose file ya Daemon config mein log size limit set karo (e.g., max-size 10MB).
7.  **Verify Log Rotation** – Container mein heavy logs generate karo aur check karo ki purani log files automatically delete ho rahi hain ya nahi.
8.  **Timezone Set karo** – Container mein `TZ` environment variable set karo aur verify karo ki logs ka time host ke time se match kar raha hai.
9.  **System prune karo** – `docker system prune -a --volumes` (soch samajh kar). Disk space free hua verify karo.
10. **Inspect command ka use karo** – container ka IP address, environment variables, mounts etc. nikalna seekho using `docker inspect` aur `--format` flag.
11. **Disk Usage Check** – `docker system df` command se disk usage analyze karo.

### 4. Definition of Done
*   Healthcheck working hai (status healthy).
*   Crash karne par container restart ho raha hai.
*   Logs automatic rotate ho rahe hain (disk full nahi ho raha).
*   Logs ka timestamp sahi hai (timezone fix).
*   System prune aur disk usage samajh aa gaya.

---

## 🧩 Module 9: Production Hardening (Limits, Signals, Secrets & Capabilities) [UPDATED]
1. The Concept
Resource Governance, Graceful Shutdowns, aur Advanced Security.
2. Why & Learning Outcome (Real-World Scenario)
Kyun Zaroori Hai? Ek buggy service pure server ki RAM kha sakti hai. abrupt stop se data corrupt ho sakta hai.
Learning: CPU/RAM Limits, SIGTERM handling (Exec Form), Secrets Management, Linux Capabilities.
Agar Nahi Kiya: Server OOM hoga. Data loss hoga.
Problem Solved: Stability aur Data Integrity.
3. Practical Tasks
Resource Limits
Container run karo with memory limit – --memory="256m" aur --cpus="0.5".
Container ke andar stress test karo – memory 300MB allocate karne ki koshish karo. Container OOM kill hona chahiye.
Stats check karo – docker stats se limit ke andar usage dikhna chahiye.
Compose mein limits define karo – deploy.resources section mein.
Signal Handling & PID 1
App code mein SIGTERM handler add karo – graceful shutdown ka message print karo.
Image build karo – my-app:signals.
Verify Exec Form – Ensure karo ki Dockerfile mein CMD Exec Form (["python", "app.py"]) mein hai.
Container run karo without --init – docker stop karo aur dekho ki graceful shutdown message aata hai ya nahi (Exec form mein aana chahiye).
Container run karo with --init – docker stop karo, ab message aana chahiye (aur zyada reliable hoga).
Compose mein init: true add karo aur test karo.
Stop Grace Period – Compose mein stop_grace_period set karo aur test karo ki container ko shutdown ke liye kitna time mil raha hai.
Secrets Management
Docker Swarm initialize karo (temporary) – docker swarm init.
Secret create karo – echo "password" | docker secret create db_pass -.
Compose file (v3.8+) likho jo secret ko service mein mount kare.
App code mein file se password read karo.
Stack deploy karo – docker stack deploy -c docker-compose.yml myapp.
Verify karo – container mein /run/secrets/db_pass file exist karti hai.
Swarm clean up – stack remove karo, swarm leave karo.
Non‑Swarm alternative – host par secret file banao, compose mein volume mount karo as read-only.
Capability Dropping (Advanced Security)
Drop Capabilities – Compose ya run command mein cap_drop: ALL use karo.
Add Specific Caps – Agar app ko network access chahiye toh sirf NET_BIND_SERVICE add karo.
4. Definition of Done
Resource limits enforce hote hain (OOM kill).
CMD Exec Form mein hai aur signals handle ho rahe hain.
Secrets file se read hote hain, env variable mein nahi.
Container ke paas root capabilities nahi hain (cap_drop working).

---

## 🧩 Module 10: Advanced Debugging & Image Hardening (Linting, Scanning, Permissions)

### 1. The Concept
CI/CD Integration, Vulnerability Management, aur Forensic Debugging.

### 2. Why & Learning Outcome (Real-World Scenario)
*   **Kyun Zaroori Hai?** Production mein image push karne se pehle usse scan karna zaroori hai. Crashed containers se logs nikalna aana chahiye.
*   **Learning:** Hadolint, Trivy, File Permissions, Crash Analysis.
*   **Agar Nahi Kiya:** Vulnerable images production mein chali jayengi. Crash ka reason pata nahi chalega.
*   **Problem Solved:** Secure Supply Chain aur Faster Mean Time To Resolution (MTTR).

### 3. Practical Tasks
#### Hadolint (Linting)
1.  **Hadolint run karo apni Dockerfile par** – Docker se hadolint image use karo. Warnings dekho.
2.  **Warnings fix karo** – jaise version pinning, multiple RUN ko combine karna, etc.

#### Trivy (Image Scanning)
3.  **Trivy se apni image scan karo** – `aquasec/trivy` image use karo.
4.  **Vulnerabilities review karo** – CRITICAL, HIGH.
5.  **Fix try karo** – base image change karo, dependencies update karo, phir rescan.

#### File Permissions
6.  **Dockerfile mein COPY ke saath `--chown` aur `--chmod` use karo** – sensitive files ke liye 640, executable ke liye 755.
7.  **Build karo aur verify** – container mein jaake permissions check karo.

#### Debugging Crashed Containers
8.  **Ek container run karo jo turant exit kare** (e.g., `alpine sh -c "exit 1"`).
9.  **Exit code check karo** – `docker ps -a` aur `docker inspect`.
10. **Logs dekho** – `docker logs`.
11. **Crashed container se files copy karo** – `docker cp` ya `--volumes-from` use karke.
12. **Kisi running container mein exec karo** – `docker exec -it` se interactive shell lo.

### 4. Definition of Done
*   Hadolint warnings fix ho gayi (ya known warnings acceptable hain).
*   Trivy scan mein critical vulnerabilities nahi hain (ya mitigated).
*   File permissions inside container correct hain.
*   Crashed container se logs aur files retrieve kar liye.
*   Running container mein exec karke debugging kar liya.

---

## ✅ Final Confirmation from Senior DevOps

Maine **3 baar audit kiya hai**.
1.  **Original Prompt Constraints:** Sab follow huye (No K8s, Docker Engine/Compose only).
2.  **Missing Links:** Security, Optimization, Reliability, Debugging, Best Practices, Networking, Signals – Sab cover huye.
3.  **Production Gaps:** Log Rotation, Backup, Registry, Multi-arch, Overrides, Cap Drop, Timezone, Workdir, Entrypoint, Health Dependencies – Sab integrate huye.
4.  **New Requirement:** Har module mein **"Why & Learning Outcome"** section add kiya gaya hai jo real-world problem solve karta hai.

Ab ye roadmap **100% Complete** hai. Isme se kuch bhi miss nahi hua hai.

**Task:**
**Module 1** se start karo. Jaise hi tumhare saare steps complete ho jayein aur Definition of Done verify ho jaye, reply karo: **"Module 1 Done"**.

**All the best! Phod ke dikhao.** 🚀🐳