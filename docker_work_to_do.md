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

---=======Done above all ===========

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
- Ye **security best practice**hai                                                                                                                          
Without any import kaise access karega?
Docker Compose ka apna built-in feature hai ki wo .env file automatically read karta hai agar wo same folder hierarchy me ho jahan docker-compose.yml rakha hai.
Isliye tumhe manually import karne ki zarurat nahi hai.

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
Agar application code me print ya console.log nahi hai to logs kyun aayenge?
Docker Compose ke logs command container ke stdout/stderr output ko capture karta hai. Matlab:
- Agar tumhare app code me console.log, print, ya koi logging framework (winston, log4j, etc.) use nahi hua → application-level logs nahi aayenge.
- Lekin container ke andar chalne wale base process (jaise MySQL, Redis, Nginx) apne khud ke logs likhte hain stdout/stderr pe → wo logs tumhe docker compose logs me dikhenge.
- Agar ek container completely silent hai (kuch bhi stdout/stderr pe nahi likh raha) → docker compose logs me kuch bhi nahi dikhega.

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

## 🧩 Module 6: The Optimizer (Production Grade Builds & Multi-Arch)

### 1. The Concept - Kya Seekhoge?
Tum seekhoge ki **Docker images ko optimize** kaise karte hain (size kam karna, build speed badhana) aur **cross-platform images** kaise banate hain jo **different architectures** (Intel, ARM, M1 Mac) par chale.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Ek **500MB image** ko production server par deploy karne mein **5-10 minutes** lagte hain. Agar tumhare paas **100 servers** hain, toh ye **massive time waste** hai. Plus, agar tumne **M1 Mac** par image build ki aur **Linux server** par deploy kari, toh **architecture mismatch** se container **crash** ho jayega.

**Seekhoge:**
- **Multi-stage builds:** Build dependencies ko final image se **alag** rakhna
- **Layer caching:** Rebuild time **90% kam** karna
- **BuildKit:** Advanced caching aur **parallel builds**
- **Multi-arch images:** Ek image **sabhi platforms** par chale

**Agar ye nahi kiya:** 
- **Deployment slow** hoga - CI/CD pipeline mein **hours waste** honge
- **Architecture mismatch** se production mein **runtime errors** aayenge
- **Bandwidth waste** hoga - badi images **network** par load dalti hain

### 3. Practical Tasks - Step by Step Kya Karna Hai

#### BuildKit & Size Optimization
**Task 1: BuildKit Enable Karna**
- Docker ke **advanced build engine** ko enable karna hai
- Ye **parallel builds** aur **better caching** provide karta hai
- Environment variable set karna hai: `DOCKER_BUILDKIT=1`
- Ye **build speed 2-3x faster** kar deta hai

**Task 2: Current Image Size Check Karna**
- Apni **existing image** (`my-app:v1`) ka size dekhna hai
- `docker images` command use karni hai
- Ye **baseline** hai - optimization ke baad **compare** karenge
- Normally Python/Node apps **300-500MB** hoti hain

#### Multi-Stage Build
**Task 3: Multi-Stage Dockerfile Likhna**
- **Stage 1 (Builder):** Dependencies install karna, code compile karna
- **Stage 2 (Final):** Sirf **runtime files** copy karna
- **Build tools** (gcc, npm, pip) final image mein **nahi** aayenge
- Base image **alpine** ya **slim** variant use karna

**Task 4: Optimized Image Build Karna**
- Nayi Dockerfile se image build karni hai
- Tag **`my-app:optimized`** dena hai
- BuildKit **automatically** best practices apply karega
- Build time mein **progress** dikhega (BuildKit feature)

**Task 5: Size Comparison Karna**
- **Purani** aur **nayi** image ka size compare karna hai
- Normally **50-70% size reduction** milta hai
- Example: 500MB → 150MB
- Ye **deployment time** ko **drastically reduce** karta hai

#### Layer Caching Optimization
**Task 6: Dockerfile Layer Order Optimize Karna**
- **Pehle:** Dependency files copy karo (`requirements.txt`, `package.json`)
- **Phir:** Dependencies install karo (`RUN pip install`)
- **Last:** Application code copy karo
- Agar **code change** hua, toh **dependencies re-install nahi** hongi

#### Advanced: Distroless Images
**Task 7: Distroless Image Use Karna**
- **Google's distroless** images use karni hain
- Isme **shell, package manager** nahi hote - **ultra secure**
- Final stage: `FROM gcr.io/distroless/python3`
- Image size **aur bhi kam** hoga, security **maximum**

**Task 8: Distroless Image Test Karna**
- Build karke container run karna hai
- **Shell access nahi** milega (security feature)
- App **normally** chalegi
- Ye **production-grade security** hai

#### Multi-Architecture Support
**Task 9: Docker Buildx Setup Karna**
- **Buildx** Docker ka **multi-platform builder** hai
- Builder instance create karna hai
- Ye **emulation** use karke **different architectures** ke liye build karta hai
- Ek baar setup, **hamesha** use kar sakte ho

**Task 10: Multi-Arch Image Build Karna**
- **`--platform`** flag use karna hai
- `linux/amd64` (Intel/AMD servers) aur `linux/arm64` (ARM, M1 Mac) dono ke liye build
- Ek hi command se **dono images** ban jayengi
- Ye **universal compatibility** deta hai

**Task 11: Multi-Arch Image Registry Par Push Karna**
- Image ko **Docker Hub** ya **GitHub Container Registry** par push karna hai
- Docker **manifest list** automatically create karega
- Manifest list mein **dono architectures** ki info hogi
- User jis platform se pull karega, **sahi image** automatically download hogi

**Task 12: Multi-Arch Verify Karna**
- Registry par jaake **manifest** check karna hai
- `docker manifest inspect` command use karni hai
- Dono architectures **listed** honi chahiye
- Ye **proof** hai ki image **portable** hai

### 4. Definition of Done - Kaise Pata Chalega Success Hua?
- Image size **50%+ reduce** hua (original se compare karke)
- **BuildKit enabled** tha aur build **faster** hua
- **Multi-stage build** successfully kaam kar rahi hai
- Image **different architectures** (amd64, arm64) par **bina error** run ho rahi hai
- Registry par **manifest list** visible hai

---

# Module 7-10 Expanded Content

## 🔒 Module 7: The Security Guard (Non‑Root & Read-Only)

### 1. The Concept - Kya Seekhoge?
Tum seekhoge ki **containers ko secure** kaise banate hain - **root user se bachna**, **filesystem ko read-only** rakhna, aur **minimum privileges** ke saath run karna.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Default mein **containers root user** se chalte hain. Agar koi **hacker** tumhari application mein **vulnerability** dhundh le (SQL injection, code execution), toh wo **container ke andar root access** pa jayega. Agar container **host ke saath resources share** kar raha hai, toh hacker **pura server compromise** kar sakta hai.

**Real Example:** 2019 mein **Tesla** ke Kubernetes cluster ko hack kiya gaya kyunki containers **root** se chal rahe the aur **cryptocurrency mining** ke liye use kiye gaye.

**Seekhoge:**
- **Non-root user:** Container ko **limited permissions** wale user se chalana
- **Read-only filesystem:** Container **files modify nahi** kar sakta
- **Tmpfs:** Temporary writes ke liye **memory-based storage**

**Agar ye nahi kiya:** 
- **Security breach** mein attacker ko **full control** mil jayega
- **Privilege escalation** se **host machine** bhi compromise ho sakta hai
- **Compliance failures** (SOC2, ISO 27001) - audit fail hoga

### 3. Practical Tasks - Step by Step Kya Karna Hai

#### Non-Root User Setup
**Task 1: Dockerfile Mein Non-Root User Create Karna**
- Dockerfile mein **dedicated user** banana hai
- **UID/GID 1000** set karna hai (standard non-root)
- Alpine: `addgroup -g 1000 appuser && adduser -D -u 1000 -G appuser appuser`
- Debian: `groupadd -g 1000 appuser && useradd -u 1000 -g appuser appuser`

**Task 2: File Ownership Set Karna**
- Files copy karte waqt **`--chown`** flag use karna hai
- `COPY --chown=appuser:appuser . /app`
- Ye ensure karta hai ki **files user ke paas** hain, root ke paas nahi
- Bina iske app **permission errors** dega

**Task 3: USER Instruction Set Karna**
- Dockerfile mein **`USER appuser`** add karna hai
- Ye instruction ke **baad** saare commands **non-root** se run honge
- Ye **last** mein karna hai (dependencies install ke baad)
- Production mein ye **mandatory** hai

**Task 4: Secure Image Build Karna**
- Nayi Dockerfile se image build karni hai
- Tag **`my-app:secure`** dena hai
- Build **successfully** hona chahiye
- Koi permission error **nahi** aana chahiye

**Task 5: Container Run Karna**
- Image se container start karna hai
- Normal command se run karo
- App **properly** chalani chahiye
- Functionality **same** rahegi, security **better** hogi

#### Security Verification
**Task 6: Container Ke Andar User Verify Karna**
- Container mein **shell** open karna hai: `docker exec -it <container> sh`
- **`whoami`** command run karni hai - **`appuser`** dikhna chahiye (root nahi)
- **`id`** command run karni hai - **UID 1000** dikhna chahiye
- Ye **proof** hai ki container **non-root** se chal raha hai

**Task 7: Privileged Operation Test Karna**
- Container ke andar **package install** try karna hai
- Alpine: `apk add curl` ya Debian: `apt-get install curl`
- **Permission denied** error aana chahiye
- Ye **good sign** hai - attacker bhi **tools install nahi** kar payega

#### Read-Only Filesystem
**Task 8: Read-Only Container Run Karna**
- Container ko **`--read-only`** flag ke saath run karna hai
- Filesystem **completely locked** ho jayega
- Koi bhi file **write/modify nahi** ho sakti
- Ye **maximum security** hai

**Task 9: Write Operation Test Karna**
- Container mein exec karke **file create** try karna hai
- `touch /app/test.txt` - **Read-only file system** error aana chahiye
- Ye **verify** karta hai ki filesystem **protected** hai
- Attacker **malware download nahi** kar payega

#### Tmpfs for Temporary Writes
**Task 10: Tmpfs Mount Karna**
- Agar app ko **temporary files** likhni hain (logs, cache), toh **tmpfs** use karna hai
- `--tmpfs /tmp:rw,noexec,nosuid,size=100m`
- Ye **memory-based** storage hai - **disk pe nahi** likha jayega
- Container stop hone par **automatically clean** ho jayega

**Task 11: Tmpfs Verify Karna**
- Container mein `/tmp` folder mein **file create** karna hai
- `echo "test" > /tmp/test.txt` - **successful** hona chahiye
- Baaki filesystem **read-only** rahega
- Ye **balance** hai - security + functionality

**Task 12: Compose Mein Security Settings Add Karna**
- `docker-compose.yml` mein **security options** add karne hain
- `read_only: true` aur `tmpfs: [/tmp]`
- `user: "1000:1000"` specify karna hai
- Ye **production-ready** configuration hai

### 4. Definition of Done - Kaise Pata Chalega Success Hua?
- Container **non-root user** se chal raha hai (`whoami` se verify)
- User **privileged operations nahi** kar sakta (package install fail)
- Filesystem **read-only** hai (file write fail, except tmpfs)
- Tmpfs mein **temporary writes** kaam kar rahi hain

---

## 🩺 Module 8: The Operator (Healthchecks, Logs & Pruning)

### 1. The Concept - Kya Seekhoge?
Tum seekhoge ki **containers ko monitor** kaise karte hain, **automatic recovery** kaise setup karte hain, aur **system maintenance** kaise karte hain (logs, disk space).

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario:** Tumhari application **hang** ho sakti hai bina crash huye - process chal rahi hai lekin **requests handle nahi** kar rahi. Docker ko lagta hai container **healthy** hai, lekin actually **down** hai. Aise mein **customers ko errors** milte rahenge aur tumhe **pata bhi nahi** chalega.

**Problem 2:** Logs **disk full** kar dete hain. Ek production server par **100GB logs** accumulate ho sakte hain 1 month mein. Disk full hone par **saari services crash** ho jayengi.

**Problem 3:** Logs ka **timezone wrong** hai - debugging karte waqt **time match nahi** hota, confusion hota hai.

**Seekhoge:**
- **Healthchecks:** Application ki **actual health** check karna
- **Restart policies:** Crash hone par **automatic recovery**
- **Log rotation:** Disk space **manage** karna
- **Timezone management:** Logs ka time **correct** rakhna

**Agar ye nahi kiya:**
- **Downtime detect nahi** hoga - customers suffer karenge
- **Disk full** se server crash hoga
- **Debugging impossible** hoga (wrong timestamps)

### 3. Practical Tasks - Step by Step Kya Karna Hai

#### Healthcheck Setup
**Task 1: Dockerfile Mein HEALTHCHECK Add Karna**
- Dockerfile mein **HEALTHCHECK instruction** add karni hai
- **Interval:** 30 seconds (har 30s mein check)
- **Timeout:** 3 seconds (3s se zyada lage toh fail)
- **Start-period:** 5 seconds (startup ke liye grace period)
- **Retries:** 3 (3 baar fail hone par unhealthy)
- Command: `curl -f http://localhost:5000/ || exit 1`
- Dockerfile mein HEALTHCHECK add karna
- Interval: 30s
- Timeout: 3s
- Start-period: 5s
- Retries: 3
- Command: curl -f http://localhost:5000/ || exit 1
- Tumne poocha: Ye command ka output kaun run karega?
- Docker daemon run karta hai container ke andar, har interval pe.
- Tumne analogy banayi: Matlab ek cron job jaisa hai jo container me chal raha hai… aur agar return 0 hua to kya email trigger hoga?
- Answer: Output sirf health metadata me store hota hai, email/alerts ke liye external monitoring tools chahiye.
- Tumne poocha: Ye command ka output kahaan jaata hai?
- Answer: Normal logs me nahi, health metadata me store hota hai. Tumhe docker inspect se check karna hoga.

**Task 2: Health Image Build Karna**
- Nayi Dockerfile se image build karni hai
- Tag **`my-app:health`** dena hai
- Healthcheck **embedded** ho jayega image mein
- Har container automatically **monitor** hoga

**Task 3: Container Run Karna**
- Image se container start karna hai
- Container **starting** state mein hoga initially
- **Start-period** ke baad healthcheck **activate** hoga
- Logs mein healthcheck **output** dikhega

**Task 4: Health Status Verify Karna**
- `docker ps` command run karni hai
- Container ke status mein **`(healthy)`** dikhna chahiye
- `docker inspect <container>` se **detailed health info** milegi
- **Last 5 healthcheck results** dikhenge

#### Restart Policies
**Task 5: Restart Policy Test Karna**
- Container ko **restart policy** ke saath run karna hai
- `--restart=on-failure:3` (3 baar retry karega)
- Ya compose mein: `restart: unless-stopped`
- Ye **production essential** hai

**Task 6: Crash Simulation Karna**
- Container ke **andar process kill** karna hai
- `docker exec <container> kill 1` (PID 1 ko kill karo)
- Container **automatically restart** hona chahiye
- Logs mein **restart event** dikhega

**Task 7: Restart Count Verify Karna**
- `docker inspect` se **restart count** dekhna hai
- Kitni baar restart hua ye **track** hota hai
- Agar **repeatedly restart** ho raha hai, toh **problem** hai
- Ye **monitoring alert** ka basis hai

#### Log Management
**Task 8: Log Rotation Configure Karna**
- Compose file mein **logging options** add karne hain
- `max-size: "10m"` (10MB se zyada nahi)
- `max-file: "3"` (maximum 3 files)
- Ya daemon.json mein **globally** set karo

**Task 9: Heavy Logs Generate Karna**
- Container mein **loop** chalake **heavy logs** generate karna hai
- `while true; do echo "test log"; done`
- Kuch time baad **old logs rotate** ho jayenge
- Disk space **controlled** rahega

**Task 10: Log Rotation Verify Karna**
- Host machine par **log files** check karni hain
- `/var/lib/docker/containers/<id>/` mein logs hote hain
- **Multiple log files** dikhni chahiye (rotated)
- **Total size limit** ke andar hona chahiye

#### Timezone & Maintenance
**Task 11: Timezone Set Karna**
- Container mein **`TZ` environment variable** set karna hai
- `TZ=Asia/Kolkata` (ya tumhara timezone)
- Compose mein: `environment: - TZ=Asia/Kolkata`
- Logs ka **timestamp correct** ho jayega

**Task 12: Timezone Verify Karna**
- Container ke **logs** dekhne hain
- Timestamp **host ke time** se match karna chahiye
- `date` command container mein run karke **verify** karo
- Ye **debugging** ko **easy** banata hai

**Task 13: System Prune Karna**
- **Unused resources** clean karne hain
- `docker system prune -a --volumes` (careful!)
- **Stopped containers, unused images, volumes** delete honge
- **Disk space free** hoga

**Task 14: Disk Usage Analyze Karna**
- `docker system df` command run karni hai
- **Images, containers, volumes** ka size dikhega
- **Reclaimable space** bhi dikhega
- Regular **monitoring** ke liye useful

**Task 15: Inspect Command Master Karna**
- `docker inspect <container>` se **complete info** milti hai
- **IP address:** `.NetworkSettings.IPAddress`
- **Environment variables:** `.Config.Env`
- **Mounts:** `.Mounts`
- `--format` flag se **specific field** extract karo

### 4. Definition of Done - Kaise Pata Chalega Success Hua?
- Healthcheck **working** hai (status `healthy` dikhta hai)
- Container crash hone par **automatically restart** ho raha hai
- Logs **rotate** ho rahe hain (disk full nahi ho raha)
- Logs ka **timestamp correct** hai (timezone set hai)
- **System prune** aur **disk usage** commands samajh aa gayi

---

## 🛡️ Module 9: Production Hardening (Limits, Signals, Secrets & Capabilities)

### 1. The Concept - Kya Seekhoge?
Tum seekhoge ki **production-grade containers** kaise banate hain - **resource limits**, **graceful shutdowns**, **secrets management**, aur **security capabilities**.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario 1:** Ek **buggy service** mein **memory leak** hai. Wo **slowly-slowly** saari RAM consume kar rahi hai. Eventually **pura server hang** ho jayega aur **saari services down** ho jayengi.

**Real Production Scenario 2:** Database container ko **abruptly stop** kiya (SIGKILL). Database **transaction complete nahi** kar paya. Result: **data corruption** - customer orders **half-saved** hain.

**Real Production Scenario 3:** Database password **environment variable** mein hai. `docker inspect` se **koi bhi dekh** sakta hai. Security audit mein **fail** ho gaye.

**Seekhoge:**
- **Resource limits:** RAM/CPU **cap** lagana
- **Signal handling:** **Graceful shutdown** implement karna
- **Secrets management:** Passwords **securely** store karna
- **Capabilities:** **Minimum permissions** dena

**Agar ye nahi kiya:**
- **Server crash** hoga (resource exhaustion)
- **Data loss** hoga (abrupt shutdowns)
- **Security breach** hoga (exposed secrets)

### 3. Practical Tasks - Step by Step Kya Karna Hai

#### Resource Limits
**Task 1: Memory Limit Ke Saath Container Run Karna**
- Container ko **memory limit** ke saath run karna hai
- `--memory="256m"` (256MB maximum)
- `--cpus="0.5"` (0.5 CPU cores)
- Ye **resource isolation** provide karta hai

**Task 2: Memory Stress Test Karna**
- Container ke **andar** memory allocate karne ki **koshish** karni hai
- Python: `x = 'a' * (300 * 1024 * 1024)` (300MB allocate)
- Container **OOM (Out Of Memory) killed** hona chahiye
- Ye **protection** hai - ek service **dusri services** ko affect nahi karegi

**Task 3: Stats Monitor Karna**
- `docker stats` command run karni hai
- **Real-time** resource usage dikhega
- Memory usage **limit ke andar** hona chahiye
- CPU usage bhi **capped** dikhega

**Task 4: Compose Mein Limits Define Karna**
- `docker-compose.yml` mein **resources section** add karna hai
- `deploy.resources.limits.memory: 256M`
- `deploy.resources.limits.cpus: '0.5'`
- Ye **declarative** approach hai

#### Signal Handling & Graceful Shutdown
**Task 5: App Code Mein SIGTERM Handler Add Karna**
- Application code mein **signal handler** likhna hai
- Python: `signal.signal(signal.SIGTERM, handler)`
- Handler mein: **connections close** karo, **cleanup** karo
- **Graceful shutdown message** print karo

**Task 6: Signals Image Build Karna**
- Updated code se image build karni hai
- Tag **`my-app:signals`** dena hai
- Signal handling **embedded** ho jayegi
- Production-ready **shutdown** hoga

**Task 7: Exec Form Verify Karna**
- Dockerfile mein **CMD/ENTRYPOINT** check karni hai
- **Exec form** hona chahiye: `["python", "app.py"]`
- **Shell form nahi:** `python app.py`
- Exec form mein **signals properly forward** hote hain

**Task 8: Graceful Shutdown Test Karna (Without Init)**
- Container **bina `--init`** ke run karna hai
- `docker stop <container>` command run karni hai
- Logs mein **graceful shutdown message** aana chahiye
- Agar **Exec form** sahi hai, toh kaam karega

**Task 9: Init Process Test Karna**
- Container **`--init` flag** ke saath run karna hai
- `docker stop` karne par **aur bhi reliable** shutdown hoga
- Init process **zombie processes** bhi handle karega
- Ye **best practice** hai

**Task 10: Compose Mein Init Add Karna**
- `docker-compose.yml` mein **`init: true`** add karna hai
- Har container **init process** ke saath start hoga
- **Production recommended** hai
- Signal handling **guaranteed** hai

**Task 11: Stop Grace Period Set Karna**
- Compose mein **`stop_grace_period: 30s`** add karna hai
- Container ko **30 seconds** milenge shutdown ke liye
- Agar **30s mein shutdown nahi** hua, toh **SIGKILL** bheja jayega
- Application ki **cleanup time** ke according set karo

#### Secrets Management
**Task 12: Docker Swarm Initialize Karna**
- **Temporary** Swarm mode enable karna hai
- `docker swarm init` command run karni hai
- Ye **secrets feature** enable karega
- Testing ke liye **local machine** par hi karo

**Task 13: Secret Create Karna**
- Docker secret **create** karni hai
- `echo "mypassword123" | docker secret create db_pass -`
- Secret **encrypted** store hoga
- **Environment variable** se better hai

**Task 14: Compose File Mein Secret Mount Karna**
- Compose file (v3.8+) mein **secrets section** add karna hai
- Service mein secret **mount** karna hai
- Secret **`/run/secrets/db_pass`** par available hoga
- **File** ke form mein milega, env var nahi

**Task 15: App Code Mein Secret Read Karna**
- Application code mein **file se password read** karna hai
- Python: `with open('/run/secrets/db_pass') as f: password = f.read()`
- **Environment variable** se **nahi** read karna
- Ye **secure** approach hai

**Task 16: Stack Deploy Karna**
- `docker stack deploy -c docker-compose.yml myapp` run karna hai
- **Swarm mode** mein deploy hoga
- Secrets **automatically mount** honge
- Container mein **verify** karo

**Task 17: Secret Verify Karna**
- Container ke **andar** `/run/secrets/` folder check karna hai
- `db_pass` file **exist** karni chahiye
- File **read** karke password verify karo
- `docker inspect` se secret **visible nahi** hona chahiye

**Task 18: Swarm Cleanup Karna**
- Testing ke baad **cleanup** karna hai
- `docker stack rm myapp` (stack remove)
- `docker swarm leave --force` (swarm leave)
- System **normal mode** mein aa jayega

**Task 19: Non-Swarm Alternative Setup Karna**
- **Production** mein Swarm nahi use kar rahe toh **alternative** hai
- Host machine par **secret file** banao
- Compose mein **volume mount** karo: `./secrets/db_pass:/run/secrets/db_pass:ro`
- **Read-only** mount karna zaroori hai

#### Capability Dropping
**Task 20: Capabilities Drop Karna**
- Compose mein **`cap_drop: ALL`** add karna hai
- Container ke paas **koi root capabilities nahi** hongi
- **Maximum security** hai
- Lekin app **break** ho sakti hai

**Task 21: Specific Capabilities Add Karna**
- Agar app ko **specific permission** chahiye, toh **selective add** karo
- `cap_add: NET_BIND_SERVICE` (port 80/443 bind karne ke liye)
- **Minimum required** capabilities hi do
- Ye **principle of least privilege** hai

**Task 22: Capabilities Test Karna**
- Container run karke **verify** karo ki app **chal rahi** hai
- **Unnecessary operations fail** hone chahiye
- Security **hardened** hai
- Production-ready **configuration** hai

### 4. Definition of Done - Kaise Pata Chalega Success Hua?
- Resource limits **enforce** ho rahe hain (OOM kill test pass)
- **CMD Exec form** mein hai aur **signals handle** ho rahe hain (graceful shutdown)
- Secrets **file se read** ho rahe hain, **env variable** mein nahi
- Container ke paas **root capabilities nahi** hain (cap_drop working)

---

## 🔍 Module 10: Advanced Debugging & Image Hardening (Linting, Scanning, Permissions)

### 1. The Concept - Kya Seekhoge?
Tum seekhoge ki **Dockerfile ko lint** kaise karte hain, **images ko security scan** kaise karte hain, **file permissions** kaise set karte hain, aur **crashed containers ko debug** kaise karte hain.

### 2. Why & Learning Outcome - Kyun Zaroori Hai?
**Real Production Scenario 1:** Tumne image **production mein push** kar di. Next day **security team** ne alert diya - image mein **critical vulnerability** hai (CVE-2023-XXXX). Ab **emergency patching** karni padegi, **downtime** hoga.

**Real Production Scenario 2:** Container **crash** ho gaya production mein. Logs mein **kuch nahi** dikh raha. Container **already removed** ho gaya. Ab **root cause** pata nahi chal raha.

**Real Production Scenario 3:** Dockerfile mein **bad practices** hain - **latest tag** use kiya, **secrets hardcoded** hain. Code review mein **reject** ho gaya.

**Seekhoge:**
- **Hadolint:** Dockerfile **best practices** check karna
- **Trivy:** Image **vulnerabilities** scan karna
- **File permissions:** **Secure permissions** set karna
- **Crash debugging:** **Failed containers** se data nikalna

**Agar ye nahi kiya:**
- **Vulnerable images** production mein jayengi
- **Crash analysis impossible** hoga
- **Security audit fail** hoga

### 3. Practical Tasks - Step by Step Kya Karna Hai

#### Hadolint (Dockerfile Linting)
**Task 1: Hadolint Run Karna**
- **Hadolint** Docker image use karke Dockerfile **lint** karni hai
- `docker run --rm -i hadolint/hadolint < Dockerfile`
- **Warnings aur errors** dikhenge
- Ye **CI/CD pipeline** mein integrate karna chahiye

**Task 2: Hadolint Warnings Fix Karna**
- **Version pinning:** `FROM python:3.9` ki jagah `FROM python:3.9.18`
- **Multiple RUN combine:** Ek hi RUN mein multiple commands
- **WORKDIR use:** `cd` ki jagah `WORKDIR`
- **Latest tag avoid:** Specific version use karo

**Task 3: Clean Dockerfile Verify Karna**
- Hadolint **phir se run** karo
- **No warnings** aane chahiye (ya acceptable warnings)
- Ye **production-ready** Dockerfile hai
- **Code review** mein pass hoga

#### Trivy (Image Vulnerability Scanning)
**Task 4: Trivy Se Image Scan Karna**
- **Trivy** Docker image use karke apni image **scan** karni hai
- `docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image my-app:v1`
- **Vulnerabilities list** dikhegi
- **Severity levels:** CRITICAL, HIGH, MEDIUM, LOW

**Task 5: Vulnerabilities Review Karna**
- **CRITICAL aur HIGH** vulnerabilities ko **priority** do
- **CVE numbers** note karo
- **Affected packages** identify karo
- **Fix available** hai ya nahi check karo

**Task 6: Vulnerabilities Fix Karna**
- **Base image update** karo (newer version mein fix ho sakta hai)
- **Dependencies update** karo (`pip install --upgrade`, `npm update`)
- **Alternative packages** use karo (agar fix nahi hai)
- Image **rebuild** karo

**Task 7: Rescan Karna**
- Fixed image ko **phir se scan** karo
- **Vulnerabilities reduce** hone chahiye
- **Zero critical** vulnerabilities target hai
- Ye **continuous process** hai

#### File Permissions
**Task 8: Dockerfile Mein Permissions Set Karna**
- **COPY instruction** mein **`--chmod`** flag use karna hai
- Sensitive files: `COPY --chmod=640 config.yml /app/`
- Executable files: `COPY --chmod=755 entrypoint.sh /app/`
- **Secure by default** approach

**Task 9: Chown Aur Chmod Combine Karna**
- **Dono flags** ek saath use kar sakte ho
- `COPY --chown=appuser:appuser --chmod=640 secret.key /app/`
- **One-step** secure copy
- **Best practice** hai

**Task 10: Permissions Verify Karna**
- Container ke **andar** jaake permissions check karni hain
- `ls -la /app/` command run karo
- **Expected permissions** dikhne chahiye
- **Security audit** pass hoga

#### Debugging Crashed Containers
**Task 11: Crash Container Create Karna**
- Ek container **intentionally crash** karna hai
- `docker run --name crash-test alpine sh -c "exit 1"`
- Container **immediately exit** ho jayega
- Ye **real crash** simulate karta hai

**Task 12: Exit Code Check Karna**
- `docker ps -a` se **exit code** dekhna hai
- **Exit code 1** dikhega (error)
- Different exit codes **different errors** indicate karte hain
- **Exit code 0** = success, **non-zero** = error

**Task 13: Inspect Se Details Nikalna**
- `docker inspect crash-test` run karni hai
- **State.ExitCode** dekhna hai
- **State.Error** message dekhna hai
- **Complete crash info** milti hai

**Task 14: Logs Retrieve Karna**
- `docker logs crash-test` run karni hai
- **Crash se pehle** ke logs dikhenge
- **Error messages** identify karo
- Ye **primary debugging** method hai

**Task 15: Crashed Container Se Files Copy Karna**
- Container **stopped** hai lekin **files accessible** hain
- `docker cp crash-test:/app/error.log ./` (file copy)
- **Crash dumps, logs** extract kar sakte ho
- **Forensic analysis** ke liye useful

**Task 16: Volumes-From Use Karna**
- Agar crashed container mein **volume** tha, toh **data access** kar sakte ho
- `docker run --rm --volumes-from crash-test alpine ls /data`
- **Data recovery** possible hai
- **Backup** nahi tha toh ye **lifesaver** hai

**Task 17: Running Container Mein Exec Karna**
- **Live debugging** ke liye container mein **shell** lena hai
- `docker exec -it <container> sh` (Alpine) ya `bash` (Debian)
- **Interactive debugging** kar sakte ho
- **Processes, files, network** check kar sakte ho

**Task 18: Exec Se Troubleshooting Karna**
- Container ke andar **diagnostic commands** run karo
- `ps aux` (running processes)
- `netstat -tulpn` (network connections)
- `df -h` (disk usage)
- **Live system** inspect kar sakte ho

### 4. Definition of Done - Kaise Pata Chalega Success Hua?
- **Hadolint warnings fix** ho gayi (ya acceptable hain)
- **Trivy scan** mein **critical vulnerabilities nahi** hain (ya mitigated)
- **File permissions** inside container **correct** hain (security compliant)
- **Crashed container** se **logs aur files retrieve** kar liye
- **Running container** mein **exec karke debugging** kar liya

---


