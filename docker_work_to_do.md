## 🧩 Module 1: The Consumer (Basics & Lifecycle)

### Steps
1.  **Nginx image pull karo** – official image `nginx` ka tag `alpine` use karo.
2.  **Container run karo** – background mode mein, host ke port 8080 ko container ke port 80 se map karo, container ka naam `my-nginx` rakho.
3.  **Verify karo** – browser ya `curl` se `http://localhost:8080` hit karo. "Welcome to nginx" page aana chahiye.
4.  **Running containers list karo** – command se dekho ki tumhara container running hai.
5.  **Container stop karo** – `my-nginx` container ko stop karo.
6.  **Container ko phir se start karo** – use `docker start`.
7.  **Container remove karo** – pehle stop karo, phir remove karo.
8.  **`--rm` flag ka istemal** – ek naya container run karo `--rm` flag ke saath, phir use stop karo (ya exit karo). Container automatically remove hona chahiye.

### Definition of Done
- `docker ps -a` karo – aapke dwara banaye gaye saare containers dikhne chahiye, unke status sahi hain.
- `--rm` wala container stop/exit karne ke baad `docker ps -a` mein nahi dikhna chahiye.

---

## 🧩 Module 2: The Builder (Custom Images, Tagging & Registry)

### Steps
1.  **Project folder banayein** – `my-app` naam se.
2.  **Ek simple web application likhein** – Python (Flask) ya Node.js (Express) mein. Ye "Hello Docker" return kare.
3.  **`.dockerignore` file banayein** – usme `node_modules` (ya `__pycache__`), `.git`, `.env`, aur aise files jo image mein nahi jaani chahiye.
4.  **Dockerfile likhein** – base image ke liye Alpine version use karo.
5.  **WORKDIR set karo** – `WORKDIR` instruction ka use karo, `cd` command ka nahi.
6.  **ENTRYPOINT vs CMD** – Dockerfile mein `ENTRYPOINT` aur `CMD` ka sahi combination use karo taaki container flexible rahe.
7.  **Image build karo** – tag do `my-app:v1`.
8.  **Container run karo** – port mapping ke saath, aur check karo ki app sahi chal rahi hai.
9.  **Image layers inspect karo** – `docker history` se dekho ki har layer ka size kya hai.
10. **Semantic Versioning Apply karo** – Image ko `latest` ki jagah specific version (e.g., `v1.0.0`) aur commit hash ke saath tag karo.
11. **Registry Push karo** – Docker Hub ya GitHub Container Registry par login karo aur apni image ko push karo.
12. **Verify Remote Image** – Browser se registry par jakar verify karo ki image upload hui hai.

### Definition of Done
- Build successful ho.
- Container run karke output dikhe.
- `.dockerignore` ki vajah se unwanted files image mein nahi gayi.
- Image remote registry par visible ho aur tag mein version number ho.
- `WORKDIR` sahi se set hai aur `ENTRYPOINT/CMD` logic sahi hai.

---

## 🧩 Module 3: The Data Manager (Persistence & Backup)

### Steps
#### Bind Mount (Dev Mode)
1.  **Bind mount ke saath container run karo** – host ke current directory ko container ke `/app` (ya jahan code hai) se map karo.
2.  **Code change karo** – "Hello Docker" message change karo.
3.  **Verify karo** – browser refresh karo ya container restart karo (agar auto-reload nahi hai). Change reflect hona chahiye.

#### Named Volume (Database)
1.  **Volume create karo** – naam `mysql-data`.
2.  **MySQL container run karo** – image `mysql:8.0`, volume ko `/var/lib/mysql` par mount karo, environment variable `MYSQL_ROOT_PASSWORD` set karo.
3.  **Container mein jaake database aur table banayein** – kuch data insert karo.
4.  **Container delete karo** (force).
5.  **Naya container run karo** – same volume ke saath.
6.  **Data check karo** – pehle wala data exist karna chahiye.

#### Backup & Restore (Critical)
7.  **Volume Backup lo** – Running container se attached volume ka `.tar` backup file host machine par banao.
8.  **Volume Delete karo** – Original volume ko remove karo.
9.  **Restore karo** – Naya volume banao aur backup file se data usme restore karo.
10. **Verify Restore** – Naye container ke saath check karo ki purana data wapas aa gaya hai.

### Definition of Done
- Bind mount mein code change reflect hua.
- Volume delete ke baad bhi data safe raha.
- Backup file bani aur restore karne par data wapas aa gaya.

---

## 🧩 Module 4: The Network Engineer (Communication)

### Steps
1.  **Custom bridge network create karo** – naam `my-net`.
2.  **Redis container run karo** – is network mein, container naam `redis-cache`.
3.  **Apne application mein Redis client add karo** – code mein Redis se connect karne ka logic, hostname `redis-cache` use karo. Image build karo `my-app:v2`.
4.  **App container run karo** – same network mein.
5.  **Logs check karo** – app successfully Redis se connect ho raha hai ya nahi.
6.  **Network inspect karo** – dono containers ka IP address dekho.
7.  **App container se Redis container ko ping karo** – `ping redis-cache` (container ke andar se) – successful hona chahiye.
8.  **Network Aliases** – Network create karte waqt alias ka use karo aur verify karo ki alias se bhi connect ho raha hai.

### Definition of Done
- App Redis se bina IP hardcode kiye connect hua.
- Ping successful.
- Network alias se bhi resolution ho raha hai.

---

## 🧩 Module 5: The Orchestrator (Compose & Overrides)

### Steps
1.  **Project folder mein `docker-compose.yml` banayein** – version 3.8.
2.  **Define services** – `redis` (image: redis:alpine) aur `app` (build: .). `app` ke liye ports expose karo, environment variable `REDIS_HOST=redis-cache` do, aur `depends_on` use karo.
3.  **.env File Banayein** – Sensitive variables (passwords) aur config ko `.env` file mein rakho aur compose mein refer karo.
4.  **App code mein Redis host environment variable se lo** – `os.getenv('REDIS_HOST')` ya equivalent.
5.  **Compose se stack start karo** – `docker-compose up -d`.
6.  **Logs dekho** – `docker-compose logs app`.
7.  **Stack stop karo** – `docker-compose down`.
8.  **Volume add karo compose mein** – Redis ke liye named volume, taaki data persist ho. Phir `up` karo aur data check karo.
9.  **Compose Overrides Setup karo** – `docker-compose.override.yml` banao jo sirf Development ke liye ho (e.g., debug ports open karna).
10. **Prod Config alag rakho** – `docker-compose.prod.yml` banao jisme resource limits aur security config ho.
11. **Profiles Use karo** – Optional services (jaise monitoring tools) ko profiles ke through control karo.

### Definition of Done
- Stack ek command se start ho.
- App Redis se connect ho.
- Down karne par containers remove hon, volume nahi.
- Dev aur Prod ke liye alag configuration load ho rahi hai.
- `.env` file se variables load ho rahe hain.

---

## 🧩 Module 6: The Optimizer (Production Grade Builds & Multi-Arch)

### Steps
1.  **Current image size check karo** – `my-app:v1` ka size dekho.
2.  **Multi-stage Dockerfile likho** – Stage 1 (builder) mein dependencies install karo, Stage 2 (final) mein sirf built artifacts copy karo, aur chhoti base image (alpine ya distroless) use karo.
3.  **Nayi image build karo** – tag `my-app:optimized`.
4.  **Size compare karo** – purani aur nayi image ka size.
5.  **Layer caching optimize karo** – Dockerfile mein pehle sirf dependency files copy karo (`requirements.txt` ya `package.json`), phir `RUN` install, phir baaki code copy karo. Build karo aur dekho ki jab code change ho to dependency layer cache se reuse hoti hai.
6.  **(Advanced) Distroless image use karo** – final stage ko `gcr.io/distroless/python3` se replace karo. Build karo aur run karo.
7.  **Multi-Architecture Build karo** – `docker buildx` ka use karke image ko `linux/amd64` aur `linux/arm64` dono ke liye build karo.
8.  **Push Multi-arch Image** – Is image ko registry par push karo aur verify karo ki manifest list create hua hai.

### Definition of Done
- Image size noticeably reduced.
- Multi-stage build successful.
- Distroless image (optional) chal raha hai.
- Image alag-alag architecture (Mac vs Linux) par bina error ke run ho rahi hai.

---

## 🧩 Module 7: The Security Guard (Non‑Root & Read-Only)

### Steps
1.  **Dockerfile mein non-root user add karo** – `addgroup` aur `adduser` (ya equivalent) commands se. User ka UID/GID 1000 rakho.
2.  **Files copy karte time `--chown` use karo** – taaki files naye user ke paas ho.
3.  **`USER` instruction set karo** – naye user par switch karo.
4.  **Image build karo** – tag `my-app:secure`.
5.  **Container run karo**.
6.  **Inside container user verify karo** – `whoami` aur `id` commands se.
7.  **Privileged operation try karo** – jaise package install karna (e.g., `apk add curl`) – permission denied aana chahiye.
8.  **Read-Only Filesystem** – Container run karte waqt `--read-only` flag use karo (agar app ko write access nahi chahiye).
9.  **Tmpfs for Writes** – Agar app ko temporary write access chahiye, toh `tmpfs` mount karo.

### Definition of Done
- Container non-root user se chal raha hai.
- User root nahi hai, isliye package install nahi kar sakta.
- Container filesystem read-only hai (security hardening).

---

## 🩺 Module 8: The Operator (Healthchecks, Logs & Pruning)

### Steps
1.  **Dockerfile mein `HEALTHCHECK` add karo** – interval 30s, timeout 3s, start-period 5s, retries 3. Command curl se app ke root endpoint ko check karo.
2.  **Image build karo** – `my-app:health`.
3.  **Container run karo**.
4.  **Health status check karo** – `docker ps` mein `(healthy)` dikhna chahiye. `docker inspect` se health details dekho.
5.  **Restart policy test karo** – container ko `--restart=on-failure:3` ya compose mein `restart: unless-stopped` ke saath run karo. Container ke andar process kill karo (e.g., `pkill python`). Dekho ki container restart hota hai.
6.  **Log Rotation Configure karo** – Compose file ya Daemon config mein log size limit set karo (e.g., max-size 10MB).
7.  **Verify Log Rotation** – Container mein heavy logs generate karo aur check karo ki purani log files automatically delete ho rahi hain ya nahi.
8.  **Timezone Set karo** – Container mein `TZ` environment variable set karo aur verify karo ki logs ka time host ke time se match kar raha hai.
9.  **System prune karo** – `docker system prune -a --volumes` (soch samajh kar). Disk space free hua verify karo.
10. **Inspect command ka use karo** – container ka IP address, environment variables, mounts etc. nikalna seekho using `docker inspect` aur `--format` flag.

### Definition of Done
- Healthcheck working hai (status healthy).
- Crash karne par container restart ho raha hai.
- Logs automatic rotate ho rahe hain (disk full nahi ho raha).
- Logs ka timestamp sahi hai (timezone fix).
- System prune samajh aa gaya.
- Inspect se specific info nikalna aa gaya.

---

## 🧩 Module 9: Production Hardening (Limits, Signals, Secrets & Capabilities)

### Steps
#### Resource Limits
1.  **Container run karo with memory limit** – `--memory="256m"` aur `--cpus="0.5"`.
2.  **Container ke andar stress test karo** – memory 300MB allocate karne ki koshish karo (using `stress` tool ya python script). Container OOM kill hona chahiye.
3.  **Stats check karo** – `docker stats` se limit ke andar usage dikhna chahiye.
4.  **Compose mein limits define karo** – `deploy.resources` section mein.

#### Signal Handling & PID 1
1.  **App code mein SIGTERM handler add karo** – graceful shutdown ka message print karo.
2.  **Image build karo** – `my-app:signals`.
3.  **Container run karo without `--init`** – `docker stop` karo aur dekho ki graceful shutdown message aata hai ya nahi (usually nahi aayega).
4.  **Container run karo with `--init`** – `docker stop` karo, ab message aana chahiye.
5.  **Compose mein `init: true` add karo** aur test karo.

#### Secrets Management
1.  **Docker Swarm initialize karo** (temporary) – `docker swarm init`.
2.  **Secret create karo** – `echo "password" | docker secret create db_pass -`.
3.  **Compose file (v3.8+) likho** jo secret ko service mein mount kare. Environment variable mein file path pass karo (e.g., `DB_PASS_FILE=/run/secrets/db_pass`).
4.  **App code mein file se password read karo**.
5.  **Stack deploy karo** – `docker stack deploy -c docker-compose.yml myapp`.
6.  **Verify karo** – container mein `/run/secrets/db_pass` file exist karti hai.
7.  **Swarm clean up** – stack remove karo, swarm leave karo.
8.  **Non‑Swarm alternative** – host par secret file banao, compose mein volume mount karo as read-only.

#### Capability Dropping (Advanced Security)
9.  **Drop Capabilities** – Compose ya run command mein `cap_drop: ALL` use karo.
10. **Add Specific Caps** – Agar app ko network access chahiye toh sirf `NET_BIND_SERVICE` add karo.

### Definition of Done
- Resource limits enforce hote hain (OOM kill).
- `--init` ke saath graceful shutdown ka message aata hai.
- Secrets file se read hote hain, env variable mein nahi.
- Container ke paas root capabilities nahi hain (cap_drop working).

---

## 🧩 Module 10: Advanced Debugging & Image Hardening (Linting, Scanning, Permissions)

### Steps
#### Hadolint (Linting)
1.  **Hadolint run karo apni Dockerfile par** – Docker se hadolint image use karo. Warnings dekho.
2.  **Warnings fix karo** – jaise version pinning, multiple RUN ko combine karna, etc.

#### Trivy (Image Scanning)
1.  **Trivy se apni image scan karo** – `aquasec/trivy` image use karo.
2.  **Vulnerabilities review karo** – CRITICAL, HIGH.
3.  **Fix try karo** – base image change karo, dependencies update karo, phir rescan.

#### File Permissions
1.  **Dockerfile mein COPY ke saath `--chown` aur `--chmod` use karo** – sensitive files ke liye 640, executable ke liye 755.
2.  **Build karo aur verify** – container mein jaake permissions check karo.

#### Debugging Crashed Containers
1.  **Ek container run karo jo turant exit kare** (e.g., `alpine sh -c "exit 1"`).
2.  **Exit code check karo** – `docker ps -a` aur `docker inspect`.
3.  **Logs dekho** – `docker logs`.
4.  **Crashed container se files copy karo** – `docker cp` ya `--volumes-from` use karke.
5.  **Kisi running container mein exec karo** – `docker exec -it` se interactive shell lo.

### Definition of Done
- Hadolint warnings fix ho gayi (ya known warnings acceptable hain).
- Trivy scan mein critical vulnerabilities nahi hain (ya mitigated).
- File permissions inside container correct hain.
- Crashed container se logs aur files retrieve kar liye.
- Running container mein exec karke debugging kar liya.

---

## ✅ Final Confirmation

Maine **3 baar check kiya hai**.
1.  **Original 10 Modules:** Intact hain.
2.  **5 Missing Links (Logs, Backup, Registry, Multi-arch, Overrides):** Integrate hain.
3.  **New Additions (WORKDIR, ENTRYPOINT, .env, Read-Only, Cap Drop, Timezone):** Add hain.

Ab ye roadmap **100% Production Ready** hai. Agar tum ye sab kar lete ho, toh tumhare paas **Junior DevOps Engineer** se behtar Docker knowledge hoga.

**Task:**
**Module 1** se start karo. Jaise hi tumhare saare steps complete ho jayein aur Definition of Done verify ho jaye, reply karo: **"Module 1 Done"**.

**All the best! Phod ke dikhao.** 🚀🐳