# 🟢 PHASE 0 – Infrastructure & Connectivity (The Foundation)

---

## Level 0: The Control Node Hardening

**Mission Name:** *The Fortress*

**💡 Concept:**  
Ansible ka control node wahi machine hoti hai jahan se saari commands chalti hain. Production mein is machine ko harden karna zaroori hai – dedicated user, non‑root execution, aur secure SSH.

**🔥 Real‑World Horror Story:**  
Ek company ne control node root user pe chhoda. Hacker ne SSH brute‑force kiya aur root access le liya. Saare managed nodes ka control haath se nikal gaya. Backup bhi nahi tha. 😱

**🎯 Practical Task:**  
1. Create a dedicated `ansible` user: `sudo useradd -m -s /bin/bash ansible`  
2. Set password: `sudo passwd ansible`  
3. Give sudo access without password: `echo "ansible ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible`  
4. Harden SSH: edit `/etc/ssh/sshd_config`  
   - `PermitRootLogin no`  
   - `PasswordAuthentication no` (after key setup)  
   - `PubkeyAuthentication yes`  
5. Restart SSH: `sudo systemctl restart sshd`  
6. Install Ansible on control node:  
   ```bash
   sudo apt update && sudo apt install ansible -y
   ansible --version   # verify
   ```

**🚩 Flag Verification:**  
- ✅ `ansible` user exists and can run sudo without password.  
- ✅ SSH root login fails.  
- ✅ `ansible --version` shows correct version.

---

## Level 1: The Managed Legion (Docker Nodes)

**Mission Name:** *Rise of the Slaves*

**💡 Concept:**  
Real data center mein multiple servers hote hain. Lekin tere paas ek hi machine hai – isliye hum **Docker containers** ko managed nodes banayenge. Har container ek alag OS simulate karega.

**🔥 Real‑World Horror Story:**  
Ek team ne sirf ek environment (Ubuntu) par playbook test kiya. Production mein CentOS tha – playbook fail ho gayi kyunki package names alag the. Multi‑OS testing zaroori hai.

**🎯 Practical Task:**  
1. Install Docker if not present: `sudo apt install docker.io -y`  
2. Add `ansible` user to docker group: `sudo usermod -aG docker ansible`  
3. Create a `docker-compose.yml` file:  
   ```yaml
   version: '3'
   services:
     ubuntu:
       image: ubuntu:20.04
       container_name: ansible-ubuntu
       command: ["sleep", "infinity"]
       privileged: true
     centos:
       image: centos:7
       container_name: ansible-centos
       command: ["sleep", "infinity"]
       privileged: true
     debian:
       image: debian:11
       container_name: ansible-debian
       command: ["sleep", "infinity"]
       privileged: true
   ```  
4. Start containers: `docker-compose up -d`  
5. Check containers: `docker ps`

**🚩 Flag Verification:**  
- ✅ `docker ps` shows three containers with names `ansible-ubuntu`, `ansible-centos`, `ansible-debian`.  
- ✅ Har container accessible hai: `docker exec -it ansible-ubuntu bash` works.

---

## Level 2: The Trust Protocol (SSH Keys)

**Mission Name:** *No Passwords Allowed*

**💡 Concept:**  
Ansible agentless hai – control node managed nodes se SSH ke through baat karta hai. Password‑based SSH insecure hai. Isliye **SSH key pairs** banakar har container mein public key daalni hoti hai.

**🔥 Real‑World Horror Story:**  
Ek admin ne password authentication use kiya. Hacker ne network sniffing kiya aur root password intercept kar liya. Puri infrastructure compromise.

**🎯 Practical Task:**  
1. `ansible` user switch karo: `sudo su - ansible`  
2. Generate SSH key pair (ED25519): `ssh-keygen -t ed25519 -f ~/.ssh/ansible_key -N ""`  
3. Har container mein SSH service install aur start karo:  
   ```bash
   docker exec -it ansible-ubuntu bash
   apt update && apt install -y openssh-server
   service ssh start
   exit
   ```  
   CentOS ke liye: `yum install -y openssh-server; systemctl start sshd`  
   Debian same as Ubuntu.  
4. Container ka IP find karo: `docker inspect ansible-ubuntu | grep IPAddress`  
5. Public key copy karo container mein:  
   ```bash
   docker cp ~/.ssh/ansible_key.pub ansible-ubuntu:/root/
   docker exec ansible-ubuntu cat /root/ansible_key.pub >> /root/.ssh/authorized_keys
   ```  
6. Test SSH: `ssh -i ~/.ssh/ansible_key root@<ip>` – bina password login ho jana chahiye.  
7. Tino containers ke liye yahi process repeat karo.

**🚩 Flag Verification:**  
- ✅ Control node se bina password har container mein SSH ho raha hai.  
- ✅ `ssh -i ~/.ssh/ansible_key root@<ip>` direct login ho jaye.

---

## Level 3: The Static Inventory

**Mission Name:** *The Census*

**💡 Concept:**  
Inventory file mein managed nodes ki list hoti hai (IPs ya hostnames). Hum group bana sakte hain (e.g., `[ubuntu]`, `[centos]`). Inventory `ini` ya `yaml` format mein likhi ja sakti hai.

**🔥 Real‑World Horror Story:**  
Ek team ne inventory mein IPs hardcode kar diye. Jab naye servers add hue, to update karna bhool gaye. Playbook galat servers pe chali.

**🎯 Practical Task:**  
1. `ansible` user ke home mein `inventory` directory banao: `mkdir ~/inventory`  
2. `ini` format inventory banao `~/inventory/hosts.ini`:  
   ```ini
   [ubuntu]
   ubuntu1 ansible_host=<ip-ubuntu>

   [centos]
   centos1 ansible_host=<ip-centos>

   [debian]
   debian1 ansible_host=<ip-debian>

   [linux:children]
   ubuntu
   centos
   debian

   [linux:vars]
   ansible_user=root
   ansible_ssh_private_key_file=~/.ssh/ansible_key
   ```  
3. `yaml` format inventory banao `~/inventory/hosts.yaml`:  
   ```yaml
   all:
     children:
       ubuntu:
         hosts:
           ubuntu1:
             ansible_host: <ip-ubuntu>
       centos:
         hosts:
           centos1:
             ansible_host: <ip-centos>
       debian:
         hosts:
           debian1:
             ansible_host: <ip-debian>
     vars:
       ansible_user: root
       ansible_ssh_private_key_file: ~/.ssh/ansible_key
   ```  
4. Test inventory: `ansible-inventory -i inventory/hosts.ini --list`  
5. Ping all hosts: `ansible all -i inventory/hosts.ini -m ping`

**🚩 Flag Verification:**  
- ✅ `ansible all -i inventory/hosts.ini -m ping` sab nodes se "pong" return kare.  
- ✅ `ansible-inventory` output sahi groups dikhaye.

---

# 🟢 PHASE 1 – Ad-Hoc Operations & Core Modules

---

## Level 4: The Ping & Setup Ritual

**Mission Name:** *First Contact*

**💡 Concept:**  
`ping` module connectivity check karta hai. `setup` module managed node ke saare facts (OS, IP, memory, etc.) collect karta hai. Facts later variables ki tarah use hote hain.

**🔥 Real‑World Horror Story:**  
Ek playbook Ubuntu 20.04 ke liye likhi thi, lekin node pe Ubuntu 18.04 tha. Playbook fail hui kyunki package version mismatch tha. Facts gather karke conditionally run karna chahiye tha.

**🎯 Practical Task:**  
1. Ping all nodes: `ansible all -i inventory/hosts.ini -m ping`  
2. Facts gather karo specific host se: `ansible ubuntu1 -i inventory/hosts.ini -m setup`  
3. Filter facts: `ansible ubuntu1 -i inventory/hosts.ini -m setup -a "filter=ansible_distribution*"`  
4. Sab nodes ke facts ek saath gather karo: `ansible all -i inventory/hosts.ini -m setup`

**🚩 Flag Verification:**  
- ✅ Ping command returns `"pong"` for all hosts.  
- ✅ Setup command OS version dikhata hai jo containers se match kare.

---

## Level 5: Package & Service Mastery

**Mission Name:** *The Installer*

**💡 Concept:**  
Package management (`apt`, `yum`) aur service management (`systemd`) ad‑hoc commands se bhi ki ja sakti hai. Idempotency ensure karni hoti hai – agar package already installed hai toh kuch na kare.

**🔥 Real‑World Horror Story:**  
Ek admin ne ad‑hoc `apt install` use kiya bina `--no-upgrade` ke. Saare packages upgrade ho gaye, aur application version mismatch se crash ho gayi.

**🎯 Practical Task:**  
1. Ubuntu container mein `nginx` install karo:  
   `ansible ubuntu1 -i inventory/hosts.ini -m apt -a "name=nginx state=present"`  
2. CentOS container mein `httpd` install karo:  
   `ansible centos1 -i inventory/hosts.ini -m yum -a "name=httpd state=present"`  
3. Debian container mein `apache2` install karo (same as Ubuntu).  
4. Start service:  
   `ansible ubuntu1 -i inventory/hosts.ini -m service -a "name=nginx state=started enabled=yes"`  
5. Check status: `ansible ubuntu1 -i inventory/hosts.ini -m shell -a "systemctl status nginx"`  
6. Verify idempotency: dubara install command chalao – result `changed=0` hona chahiye.

**🚩 Flag Verification:**  
- ✅ Har container mein respective web server installed ho.  
- ✅ Service started ho.  
- ✅ Second run par `changed=0` dikhe.

---

## Level 6: File Manipulation

**Mission Name:** *The Scribe*

**💡 Concept:**  
Files ko copy karna, content modify karna, permissions set karna – `copy`, `fetch`, `file` modules se hota hai. `copy` control node se file push karta hai, `fetch` managed node se file pull karta hai.

**🔥 Real‑World Horror Story:**  
Ek playbook ne `copy` module se sensitive config file push ki, lekin permissions 666 rakh di. Kisi aur user ne file read kar li – secret leak.

**🎯 Practical Task:**  
1. Control node par ek file banao `~/test.txt` with content "Hello Ansible".  
2. Use `copy` module to push this file to all nodes:  
   ```bash
   ansible all -i inventory/hosts.ini -m copy -a "src=~/test.txt dest=/tmp/test.txt mode=0644 owner=root group=root"
   ```  
3. Check file existence on a node: `ansible ubuntu1 -i inventory/hosts.ini -m command -a "cat /tmp/test.txt"`  
4. Use `fetch` module to pull file from node back to control node:  
   `ansible ubuntu1 -i inventory/hosts.ini -m fetch -a "src=/tmp/test.txt dest=./fetched/"`  
5. Verify file downloaded in `./fetched/` directory.  
6. Use `file` module to change permissions: `ansible ubuntu1 -i inventory/hosts.ini -m file -a "path=/tmp/test.txt mode=0644"`

**🚩 Flag Verification:**  
- ✅ Har node par `/tmp/test.txt` exist kare with correct content.  
- ✅ Fetch command se file control node par aaye.  
- ✅ File permissions sahi hoon.

---

# 🟢 PHASE 2 – Playbook Foundations (Everything as Code)

---

## Level 7: The YAML Scroll

**Mission Name:** *The Syntax Templar*

**💡 Concept:**  
Playbooks YAML format mein likhi jaati hain. Indentation ka sahi hona zaroori hai – ek extra space se playbook fail ho sakti hai. `ansible-playbook` command se run karte hain.

**🔥 Real‑World Horror Story:**  
Ek senior engineer ne YAML mein indentation galat kar di. Playbook syntax error de rahi thi, lekin code review mein kisi ne notice nahi kiya. Production deploy fail ho gaya, 1 ghanta downtime.

**🎯 Practical Task:**  
1. Apne repo mein `playbooks` directory banao.  
2. Pehla playbook `playbooks/01-ping.yml` likho:  
   ```yaml
   ---
   - name: Test connectivity
     hosts: all
     tasks:
       - name: Ping all nodes
         ansible.builtin.ping:
   ```  
3. Playbook validate karo: `ansible-playbook -i inventory/hosts.ini playbooks/01-ping.yml --syntax-check`  
4. Run karo: `ansible-playbook -i inventory/hosts.ini playbooks/01-ping.yml`  
5. Ab intentionally indentation tod kar dekho – syntax error aana chahiye. Phir sahi karo.

**🚩 Flag Verification:**  
- ✅ Playbook syntax check pass kare.  
- ✅ Playbook successful run ho.

---

## Level 8: The Law of Idempotency

**Mission Name:** *The Golden Rule*

**💡 Concept:**  
Idempotency ka matlab hai ki ek task baar‑baar chalane par bhi system ki state same rahe. Playbook pehli baar mein changes kare, dusri baar mein kuch na kare (`changed=0`). Modules khud idempotent hote hain, lekin shell/command modules nahi – unke liye hume extra care leni padti hai.

**🔥 Real‑World Horror Story:**  
Ek team ne `command` module se user add kiya. Har baar playbook chalne par wahi user dubara add ho jata, system inconsistent ho gaya.

**🎯 Practical Task:**  
1. Playbook `playbooks/02-user.yml` banao jo ek user `devops` create kare:  
   ```yaml
   ---
   - name: Create user
     hosts: all
     tasks:
       - name: Ensure user devops exists
         ansible.builtin.user:
           name: devops
           state: present
   ```  
2. Run karo: `ansible-playbook -i inventory/hosts.ini playbooks/02-user.yml` – pehli baar `changed=1` dikhega.  
3. Dubara run karo – `changed=0` hona chahiye.  
4. `command` module se idempotency tod kar dekho:  
   ```yaml
       - name: Bad way - create user with command
         ansible.builtin.command: useradd -m -s /bin/bash baduser
         ignore_errors: yes
   ```  
   Har baar error dega ya status change dikhayega.

**🚩 Flag Verification:**  
- ✅ Idempotent task second run par `changed=0` dikhaye.  
- ✅ Non‑idempotent task avoid karne ka lesson mila.

---

## Level 9: The Shell vs. Command Battle

**Mission Name:** *The Wise Choice*

**💡 Concept:**  
`shell` module actual shell environment use karta hai (environment variables, pipes, redirections allowed). `command` module direct binary call karta hai, more secure. Jab tak pipe ya env vars zaroori na ho, `command` use karna chahiye.

**🔥 Real‑World Horror Story:**  
Ek playbook mein `shell` use kiya `rm -rf` ke saath. Variable undefined tha, to `rm -rf /` execute ho gaya. Pura server wipe! 😱

**🎯 Practical Task:**  
1. Playbook `playbooks/03-shell-vs-command.yml` banao:  
   ```yaml
   ---
   - name: Demonstrate shell vs command
     hosts: all
     tasks:
       - name: Use command to echo
         ansible.builtin.command: echo "Hello from command"
         register: cmd_result
       - name: Show command output
         ansible.builtin.debug:
           var: cmd_result.stdout

       - name: Use shell to echo with env var
         ansible.builtin.shell: echo "Hello from $SHELL"
         register: shell_result
       - name: Show shell output
         ansible.builtin.debug:
           var: shell_result.stdout
   ```  
2. Run karo – dono ka output dekh.  
3. Test redirection: `ansible.builtin.shell: echo "test" > /tmp/testfile`  
   Command se aisa possible nahi.  
4. Security demo: `shell` se dangerous command likh kar dekh (jaise `rm`), lekin sambhal kar.

**🚩 Flag Verification:**  
- ✅ Samajh aa gaya kab `command`, kab `shell` use karna hai.  
- ✅ Playbook successfully run ho.

---

# 🟡 PHASE 3 – Variables, Facts & The Jinja2 Engine

---

## Level 10: Variable Scoping

**Mission Name:** *The Hierarchy*

**💡 Concept:**  
Variables multiple levels par define ho sakte hain: host vars, group vars, play vars, extra vars. Priority order important hai. Commonly: extra vars > play vars > group vars > host vars > inventory vars.

**🔥 Real‑World Horror Story:**  
Ek team ne same variable group vars aur play vars mein define kiya. Playbook unexpected behavior de rahi thi kyunki priority clear nahi thi.

**🎯 Practical Task:**  
1. Inventory file mein group vars define karo:  
   `[all:vars]` section mein `package_name=apache2` daalo (Debian/Ubuntu ke liye).  
2. CentOS group mein `package_name=httpd` daalo.  
3. Playbook `playbooks/04-vars.yml` banao:  
   ```yaml
   ---
   - name: Test variable precedence
     hosts: all
     vars:
       package_name: nginx   # play level var
     tasks:
       - name: Install package
         ansible.builtin.package:
           name: "{{ package_name }}"
           state: present
   ```  
4. Run with extra var: `ansible-playbook -i inventory/hosts.ini playbooks/04-vars.yml -e "package_name=mysql"` – konsa install hoga?  
5. Observe precedence: extra var > play var > group var.

**🚩 Flag Verification:**  
- ✅ Saari nodes par package install ho according to variable precedence.  
- ✅ Extra var se override successful ho.

---

## Level 11: Fact-Based Decision Making

**Mission Name:** *The Detective*

**💡 Concept:**  
`setup` module se facts collect hote hain. Un facts ko playbook mein use kar sakte hain, e.g., OS ke hisaab se alag package install karna.

**🔥 Real‑World Horror Story:**  
Ek playbook ne hamesha `apt` use kiya, CentOS node par fail ho gayi. Facts check karke distribution wise module use karna chahiye tha.

**🎯 Practical Task:**  
1. Playbook `playbooks/05-facts.yml` banao:  
   ```yaml
   ---
   - name: Use facts to decide
     hosts: all
     gather_facts: yes
     tasks:
       - name: Print OS family
         ansible.builtin.debug:
           msg: "This is {{ ansible_os_family }} family"

       - name: Install httpd on RedHat, apache2 on Debian
         ansible.builtin.package:
           name: "{{ 'httpd' if ansible_os_family == 'RedHat' else 'apache2' }}"
           state: present
   ```  
2. Run karo – har node par sahi package install ho.  
3. Add a condition: sirf Ubuntu par nginx install karo agar fact `ansible_distribution == 'Ubuntu'`.

**🚩 Flag Verification:**  
- ✅ Playbook har node par appropriate package install kare.  
- ✅ Facts correctly used ho.

---

## Level 12: Jinja2 Templating

**Mission Name:** *The Sculptor*

**💡 Concept:**  
Jinja2 templates se dynamic configuration files generate karte hain. Template files `.j2` extension mein hoti hain, jinmein variables aur loops/conditionals daal sakte hain.

**🔥 Real‑World Horror Story:**  
Ek team ne static config file copy kiya. Har environment ke liye alag file rakhni padi. Jinja2 use karte to ek template se sab kaam ho jata.

**🎯 Practical Task:**  
1. Control node par `templates/index.html.j2` banao:  
   ```html
   <html>
   <head><title>Welcome</title></head>
   <body>
     <h1>Hello from {{ ansible_hostname }}</h1>
     <p>OS: {{ ansible_distribution }} {{ ansible_distribution_version }}</p>
   </body>
   </html>
   ```  
2. Playbook `playbooks/06-template.yml`:  
   ```yaml
   ---
   - name: Deploy dynamic HTML
     hosts: all
     tasks:
       - name: Copy template
         ansible.builtin.template:
           src: templates/index.html.j2
           dest: /tmp/index.html
           mode: '0644'
   ```  
3. Run karo.  
4. Verify: `ansible ubuntu1 -i inventory/hosts.ini -m command -a "cat /tmp/index.html"` – hostname aur OS version dynamically fill honi chahiye.

**🚩 Flag Verification:**  
- ✅ Har node par generated HTML mein us node ka hostname aur OS info ho.  
- ✅ Template rendering sahi ho.

---

## Level 12B: Jinja2 Filters & Tests (Optional Deep Dive)

**Mission Name:** *The Filter Magician*

**💡 Concept:**  
Jinja2 filters data manipulate karte hain (e.g., `default`, `regex_replace`, `to_json`). Tests conditions check karte hain (e.g., `is defined`, `is number`).

**🔥 Real‑World Horror Story:**  
Ek playbook mein IP address se port nikalna tha, lekin `split` filter nahi pata tha, isliye complex shell script likhi. Filter se ek line mein ho jata.

**🎯 Practical Task:**  
1. Playbook `playbooks/06b-filters.yml` banao:  
   ```yaml
   ---
   - name: Show filters
     hosts: all
     vars:
       my_ip: "192.168.1.100:8080"
     tasks:
       - name: Extract port
         debug:
           msg: "Port is {{ my_ip.split(':')[-1] | default('80') }}"
       - name: Convert to JSON
         debug:
           msg: "{{ {'key': 'value'} | to_json }}"
   ```  
2. Tests use karo:  
   ```yaml
   - name: Check if variable is defined
     debug:
       msg: "my_ip is defined"
     when: my_ip is defined
   ```  
3. Run and verify.

**🚩 Flag Verification:**  
- ✅ Filters sahi output de.  
- ✅ Tests conditionally run hon.

---

# 🟡 PHASE 4 – Logic Gates & Flow Control

---

## Level 13: The Conditional Gate (`when`)

**Mission Name:** *The Gatekeeper*

**💡 Concept:**  
`when` condition ke according task run hota hai ya skip. Facts, variables, registered output par condition laga sakte hain.

**🔥 Real‑World Horror Story:**  
Ek playbook ne production node par bhi test task chala diya, kyunki `when` condition lagaana bhool gaye. Test scripts ne production data corrupt kar diya.

**🎯 Practical Task:**  
1. Playbook `playbooks/07-when.yml`:  
   ```yaml
   ---
   - name: Demonstrate when
     hosts: all
     tasks:
       - name: Run only on Ubuntu
         ansible.builtin.debug:
           msg: "This is Ubuntu"
         when: ansible_distribution == 'Ubuntu'

       - name: Run only if memory > 1GB
         ansible.builtin.debug:
           msg: "Memory is sufficient"
         when: ansible_memtotal_mb > 1024
   ```  
2. Run karo – sirf Ubuntu node par first task run ho, second task sab par (agar memory condition true ho).  
3. Extra: `when` mein complex expression use karo, e.g., `when: ansible_os_family == 'RedHat' or ansible_distribution == 'Debian'`.

**🚩 Flag Verification:**  
- ✅ Tasks condition ke hisaab se run ho rahe hain.  
- ✅ Debug output se verify ho.

---

## Level 14: The Iteration Loop (`loop`)

**Mission Name:** *The Conveyor Belt*

**💡 Concept:**  
Ek task ko multiple items ke saath run karne ke liye `loop` use karte hain. Pehle `with_items` tha, ab `loop` recommended hai.

**🔥 Real‑World Horror Story:**  
Ek admin ne 50 packages install karne ke liye 50 alag tasks likhe. Playbook 500 lines ki ho gayi. Loop se 5 lines mein ho jata.

**🎯 Practical Task:**  
1. Playbook `playbooks/08-loop.yml`:  
   ```yaml
   ---
   - name: Install multiple packages
     hosts: all
     tasks:
       - name: Install packages (Ubuntu)
         ansible.builtin.apt:
           name: "{{ item }}"
           state: present
         loop:
           - curl
           - git
           - vim
         when: ansible_os_family == 'Debian'

       - name: Install packages (CentOS)
         ansible.builtin.yum:
           name: "{{ item }}"
           state: present
         loop:
           - curl
           - git
           - vim
         when: ansible_os_family == 'RedHat'
   ```  
2. Run karo – saari nodes par packages install ho.  
3. Loop with list of dicts: `loop: [{name: 'user1', uid: 1001}, {name: 'user2', uid: 1002}]` and use `item.name`.

**🚩 Flag Verification:**  
- ✅ Packages successfully installed.  
- ✅ Task count kam raha.

---

## Level 15: The Signal Handlers (`handlers`)

**Mission Name:** *The Reactor*

**💡 Concept:**  
Handlers special tasks hote hain jo sirf tab run hote hain jab kisi task ne unhe notify kiya ho. Generally service restart ke liye use hote hain.

**🔥 Real‑World Horror Story:**  
Ek playbook ne config file update kiya, lekin service restart nahi kiya. New config apply nahi hui, application fail hoti rahi. Handler use karna chahiye tha.

**🎯 Practical Task:**  
1. Playbook `playbooks/09-handler.yml`:  
   ```yaml
   ---
   - name: Test handlers
     hosts: all
     tasks:
       - name: Update index.html
         ansible.builtin.copy:
           content: "New content"
           dest: /tmp/index.html
         notify: restart webserver

       - name: Ensure webserver is installed
         ansible.builtin.package:
           name: "{{ 'httpd' if ansible_os_family == 'RedHat' else 'apache2' }}"
           state: present

     handlers:
       - name: restart webserver
         ansible.builtin.service:
           name: "{{ 'httpd' if ansible_os_family == 'RedHat' else 'apache2' }}"
           state: restarted
   ```  
2. Pehli baar run karo – handler call hoga.  
3. Dubara run karo – handler call nahi hoga kyunki file change nahi hui.  
4. Verify with service status.

**🚩 Flag Verification:**  
- ✅ Handler sirf tab run ho jab notify ho.  
- ✅ Service restart ho.

---

# 🟡 PHASE 5 – Security & The Secret Vault

---

## Level 16: Ansible Vault (File Level)

**Mission Name:** *The Cryptex*

**💡 Concept:**  
Sensitive data (passwords, keys) ko plaintext mein commit nahi karte. Ansible Vault se poori file encrypt kar sakte hain.

**🔥 Real‑World Horror Story:**  
Ek developer ne AWS keys playbook mein hardcode kar diye, Git push kar diye. 2 ghante mein hacker ne crypto mining shuru kar di – $50,000 ka bill. 😱

**🎯 Practical Task:**  
1. Ek file `secrets.yml` banao jisme variable ho: `db_password: SuperSecret123`  
2. Encrypt file: `ansible-vault encrypt secrets.yml` – password maangega.  
3. File content ab encrypted hai.  
4. Playbook `playbooks/10-vault.yml`:  
   ```yaml
   ---
   - name: Use vault
     hosts: all
     vars_files:
       - secrets.yml
     tasks:
       - name: Print secret (masked)
         ansible.builtin.debug:
           msg: "Password is {{ db_password }}"
         no_log: true   # to avoid printing
   ```  
5. Run with vault password: `ansible-playbook -i inventory/hosts.ini playbooks/10-vault.yml --ask-vault-pass`  
6. Ab `secrets.yml` decrypt karo: `ansible-vault decrypt secrets.yml` (manually ya using vault password file).

**🚩 Flag Verification:**  
- ✅ Encrypted file playbook mein successfully use ho.  
- ✅ Bina password ke playbook fail ho.

---

## Level 17: Variable Level Encryption

**Mission Name:** *The Hidden Gem*

**💡 Concept:**  
Poore file ki jagah sirf ek variable encrypt karna. `!vault` tag se single value encrypt hoti hai.

**🔥 Real‑World Horror Story:**  
Ek team ne poora inventory file encrypt kar diya, jisse group vars edit karna mushkil ho gaya. Sirf sensitive vars encrypt karna better hai.

**🎯 Practical Task:**  
1. Encrypt a single string: `ansible-vault encrypt_string 'SuperSecret123' --name 'db_password'`  
2. Output copy karo, aur `group_vars/all.yml` mein daalo:  
   ```yaml
   db_password: !vault |
             $ANSIBLE_VAULT;1.1;AES256
             66386439653236336...
   ```  
3. Playbook mein use karo: `debug` module se print karo (with `no_log`).  
4. Run with vault password.

**🚩 Flag Verification:**  
- ✅ Sirf encrypted variable vault protected ho, baaki file readable ho.  
- ✅ Playbook run ho.

---

## Level 18: Vault Passwords & IDs

**Mission Name:** *The Keymaster*

**💡 Concept:**  
Vault password ko file mein store karke automate kar sakte hain. Multiple vault passwords ke liye `--vault-id` use karte hain.

**🔥 Real‑World Horror Story:**  
Ek admin har baar vault password manual type karta tha. CI/CD pipeline automate nahi ho pa rahi thi. Vault‑password file use karna chahiye tha.

**🎯 Practical Task:**  
1. Vault password file banao `~/.vault_pass` jisme password ho (single line). Permissions 600 rakho.  
2. Encrypt koi file using password file: `ansible-vault encrypt --vault-password-file ~/.vault_pass secrets.yml`  
3. Playbook run karo bina prompt: `ansible-playbook -i inventory/hosts.ini playbooks/10-vault.yml --vault-password-file ~/.vault_pass`  
4. Multiple vault‑id demo: `ansible-vault encrypt --vault-id dev@~/.vault_pass_dev prod_secrets.yml`

**🚩 Flag Verification:**  
- ✅ Bina manual password enter kiye playbook run ho.  
- ✅ Different vault‑id work kare.

---

# 🟠 PHASE 6 – Modularity: The Architect’s Roles

---

## Level 19: The Role Structure

**Mission Name:** *The Organizer*

**💡 Concept:**  
Roles reusable components hote hain. Ek role mein tasks, handlers, vars, defaults, files, templates sab organized hote hain.

**🔥 Real‑World Horror Story:**  
Ek company ke paas 2000 lines ka ek playbook tha. Koi change karna mushkil tha. Roles mein todne ke baad maintenance easy ho gaya.

**🎯 Practical Task:**  
1. Role create karo: `ansible-galaxy init roles/nginx`  
2. Directory structure dekho: `tree roles/nginx`  
3. Role mein task define karo `roles/nginx/tasks/main.yml`:  
   ```yaml
   ---
   - name: Install nginx
     ansible.builtin.package:
       name: nginx
       state: present
   - name: Start nginx
     ansible.builtin.service:
       name: nginx
       state: started
       enabled: yes
   ```  
4. Playbook `playbooks/11-role.yml`:  
   ```yaml
   ---
   - name: Apply nginx role
     hosts: all
     roles:
       - nginx
   ```  
5. Run karo – role execute ho.  
6. Add default variable in `roles/nginx/defaults/main.yml`: `nginx_port: 80` and use it in task.

**🚩 Flag Verification:**  
- ✅ Role successfully install and start nginx.  
- ✅ Variables se port customize ho.

---

## Level 20: Ansible Galaxy Mastery

**Mission Name:** *The Community*

**💡 Concept:**  
Ansible Galaxy se community roles download kar sakte hain. Apne roles bhi Galaxy pe share kar sakte hain.

**🔥 Real‑World Horror Story:**  
Ek team ne MySQL ka role khud likha – time waste hua. Galaxy pe already tested role tha, use kar sakte the.

**🎯 Practical Task:**  
1. Search for a role: `ansible-galaxy search geerlingguy.nginx`  
2. Install role: `ansible-galaxy install geerlingguy.nginx` – ye `~/.ansible/roles` mein install hoga.  
3. Create a `requirements.yml` file:  
   ```yaml
   - src: geerlingguy.nginx
   - src: geerlingguy.mysql
   ```  
4. Install from requirements: `ansible-galaxy install -r requirements.yml`  
5. Use in playbook:  
   ```yaml
   - hosts: all
     roles:
       - geerlingguy.nginx
       - geerlingguy.mysql
   ```  
6. Run playbook – community roles use ho rahe hain.

**🚩 Flag Verification:**  
- ✅ Community roles successfully install hue.  
- ✅ Playbook un roles ka use kare.

---

## Level 21: Cross-Platform Roles

**Mission Name:** *The Unifier*

**💡 Concept:**  
Ek role jo different OS par kaam kare – usme OS‑specific tasks, vars, aur templates alag rakhe ja sakte hain.

**🔥 Real‑World Horror Story:**  
Ek role Ubuntu ke liye likha, CentOS par fail ho gaya. Cross‑platform support nahi tha.

**🎯 Practical Task:**  
1. Role `roles/common` banao.  
2. `roles/common/tasks/main.yml` mein include karo OS‑specific tasks:  
   ```yaml
   ---
   - name: Include OS-specific vars
     ansible.builtin.include_vars: "{{ ansible_os_family }}.yml"

   - name: Run OS-specific tasks
     ansible.builtin.include_tasks: "{{ ansible_os_family }}.yml"
   ```  
3. Create `vars/Debian.yml` and `vars/RedHat.yml` with different package names.  
4. Create `tasks/Debian.yml` and `tasks/RedHat.yml` with actual tasks.  
5. Playbook mein role use karo – Ubuntu aur CentOS dono par chale.

**🚩 Flag Verification:**  
- ✅ Role dono OS families par successfully run ho.  
- ✅ Har OS ke hisaab se packages install hon.

---

# 🟠 PHASE 7 – Resilience & Error Handling

---

## Level 22: The Safety Net (`block`, `rescue`, `always`)

**Mission Name:** *The Try‑Catch*

**💡 Concept:**  
`block` mein tasks group karte hain. `rescue` tab run hota hai jab block mein koi task fail ho. `always` hamesha run hota hai.

**🔥 Real‑World Horror Story:**  
Ek playbook database update kar rahi thi. Update fail hua, lekin rollback nahi tha. Data corrupt ho gaya. Block/rescue se rollback trigger kar sakte the.

**🎯 Practical Task:**  
1. Playbook `playbooks/12-block.yml`:  
   ```yaml
   ---
   - name: Demonstrate block/rescue
     hosts: all
     tasks:
       - block:
           - name: This might fail
             ansible.builtin.command: /bin/false
           - name: This won't run if previous fails
             ansible.builtin.debug:
               msg: "I am safe"
         rescue:
           - name: Rescue task
             ansible.builtin.debug:
               msg: "Something failed, but we are fixing"
         always:
           - name: Always runs
             ansible.builtin.debug:
               msg: "This runs no matter what"
   ```  
2. Run karo – block fail hoga, rescue chalega, always chalega.  
3. Rescue mein kuch recovery action likho, jaise file restore.

**🚩 Flag Verification:**  
- ✅ Rescue block execute ho.  
- ✅ Always block execute ho.  
- ✅ Playbook fail nahi hoti (overall status maybe failed but tasks run).

---

## Level 23: Assertions & Failures

**Mission Name:** *The Guardian*

**💡 Concept:**  
`assert` module se conditions check kar sakte hain. `fail` module se manual error throw kar sakte hain. `meta: end_play` se playbook rok sakte hain.

**🔥 Real‑World Horror Story:**  
Ek playbook ne bina space check kiye deploy kar diya. Disk full tha, application crash ho gayi. Assert se pehle check karna chahiye tha.

**🎯 Practical Task:**  
1. Playbook `playbooks/13-assert.yml`:  
   ```yaml
   ---
   - name: Check preconditions
     hosts: all
     tasks:
       - name: Ensure disk space > 10%
         ansible.builtin.assert:
           that:
             - ansible_mounts[0].size_available > ansible_mounts[0].size_total * 0.1
           fail_msg: "Disk space below 10%"
           success_msg: "Disk space ok"

       - name: Fail if OS is CentOS 6 (example)
         ansible.builtin.fail:
           msg: "CentOS 6 not supported"
         when: ansible_distribution == 'CentOS' and ansible_distribution_major_version == '6'

       - name: End play if not Ubuntu
         ansible.builtin.meta: end_play
         when: ansible_distribution != 'Ubuntu'
   ```  
2. Run karo – conditions check hongi, agar fail hui to playbook stop ho.

**🚩 Flag Verification:**  
- ✅ Assert fail hone par playbook stop ho with proper message.  
- ✅ `fail` module ka use samajh aa gaya.

---

## Level 24: Ignoring Errors & Overriding Status

**Mission Name:** *The Daredevil*

**💡 Concept:**  
Kabhi kabhi expected failures hote hain jinhe ignore karna chahiye (`ignore_errors: yes`). `changed_when` se manual status set kar sakte hain.

**🔥 Real‑World Horror Story:**  
Ek playbook mein service restart ka command tha, jo pehli baar fail ho raha tha kyunki service running nahi thi. `ignore_errors` se ignore kiya, lekin `changed_when` galat set kiya to status galat ho gaya.

**🎯 Practical Task:**  
1. Playbook `playbooks/14-ignore.yml`:  
   ```yaml
   ---
   - name: Demonstrate ignore_errors
     hosts: all
     tasks:
       - name: This will fail but we ignore
         ansible.builtin.command: /bin/false
         ignore_errors: yes

       - name: This will always report changed
         ansible.builtin.command: echo "Hello"
         register: result
         changed_when: result.rc == 0   # always true
   ```  
2. Run karo – pehla task fail par playbook continue karega.  
3. Check output – second task always changed dikhayega.

**🚩 Flag Verification:**  
- ✅ `ignore_errors` se playbook fail nahi rukti.  
- ✅ `changed_when` se status manually override hota hai.

---

# 🟠 PHASE 8 – Optimization & Performance

---

## Level 25: The Parallel Strike (`forks`)

**Mission Name:** *The Swarm*

**💡 Concept:**  
Ansible default 5 forks use karta hai – ek saath 5 nodes par tasks run karta hai. `forks` parameter increase karke parallel execution badha sakte hain.

**🔥 Real‑World Horror Story:**  
Ek team ne 100 servers par playbook chalayi, default forks 5, isliye 1 ghanta laga. Forks=50 karne par 10 minute mein complete ho gaya.

**🎯 Practical Task:**  
1. Inventory mein at least 3 nodes hain.  
2. Playbook `playbooks/15-forks.yml`:  
   ```yaml
   ---
   - name: Test forks
     hosts: all
     tasks:
       - name: Sleep for 10 seconds
         ansible.builtin.command: sleep 10
   ```  
3. Run with default forks: `ansible-playbook -i inventory/hosts.ini playbooks/15-forks.yml` – time note karo.  
4. Run with forks=1: `ansible-playbook -i inventory/hosts.ini playbooks/15-forks.yml --forks=1` – sequential, zyada time.  
5. Run with forks=10: `ansible-playbook -i inventory/hosts.ini playbooks/15-forks.yml --forks=10` – parallel, kam time.

**🚩 Flag Verification:**  
- ✅ Forks increase karne par execution time kam ho.  
- ✅ Samajh aa gaya ki parallel execution kaise control karte hain.

---

## Level 26: Pipelining & Mitogen

**Mission Name:** *The Speedster*

**💡 Concept:**  
Pipelining SSH ke multiple round trips reduce karta hai. Mitogen ek third‑party strategy hai jo aur bhi speed boost deti hai.

**🔥 Real‑World Horror Story:**  
Ek playbook 1000 servers par chal rahi thi, 2 ghante lag rahe the. Pipelining enable ki to 30 minute ho gaye. Mitogen use kiya to 10 minute.

**🎯 Practical Task:**  
1. `ansible.cfg` file banao (current directory ya `~/.ansible.cfg`):  
   ```ini
   [defaults]
   forks = 20
   gathering = smart
   [ssh_connection]
   pipelining = True
   ```  
2. Playbook `playbooks/16-pipelining.yml` with multiple small tasks run karo.  
3. Observe speed difference by toggling pipelining.  
4. (Optional) Install Mitogen:  
   - Download Mitogen from GitHub.  
   - In `ansible.cfg`: `strategy_plugins = /path/to/mitogen/ansible_mitogen/plugins/strategy`  
   - `strategy = mitogen_linear`  
5. Run same playbook with mitogen – aur speed boost milega.

**🚩 Flag Verification:**  
- ✅ Pipelining enabled hone par performance improve ho.  
- ✅ Mitogen setup ho (optional but advanced).

---

## Level 27: Async & Poll

**Mission Name:** *The Fire‑and‑Forget*

**💡 Concept:**  
Long‑running tasks (jaise software installation, large file download) ko async mode mein background mein chhod sakte hain, taaki playbook aage badhe.

**🔥 Real‑World Horror Story:**  
Ek playbook mein database backup task 10 minute leta tha. Playbook wait karti rahi, total time 15 minute. Async use kiya to parallel mein doosre tasks bhi chal sake.

**🎯 Practical Task:**  
1. Playbook `playbooks/17-async.yml`:  
   ```yaml
   ---
   - name: Test async
     hosts: all
     tasks:
       - name: Long running task (async)
         ansible.builtin.command: sleep 30
         async: 60
         poll: 5

       - name: This task runs immediately
         ansible.builtin.debug:
           msg: "I am running while long task is in background"
   ```  
2. Run karo – second task turant chala, first task background mein 30 sec chalta raha.  
3. Poll interval 5 seconds hai – har 5 sec status check.  
4. Option: `poll: 0` means fire and forget – completely background.

**🚩 Flag Verification:**  
- ✅ Async task background mein chale, playbook aage badhe.  
- ✅ Task successfully complete ho.

---

# 🔴 PHASE 9 – Enterprise Integration & IaC

---

## Level 28: Infrastructure Provisioning

**Mission Name:** *The Creator*

**💡 Concept:**  
Ansible se hi containers/cloud instances provision kar sakte hain – `docker_container`, `aws_ec2`, etc. modules se. Isse complete infrastructure as code ho jata hai.

**🔥 Real‑World Horror Story:**  
Ek team ne manually 50 servers AWS console se banaye. 2 ghante lage, aur 2 servers ka tag galat ho gaya. Ansible se 5 minute mein ban jaate.

**🎯 Practical Task:**  
1. Install Docker SDK for Python: `pip install docker`  
2. Playbook `playbooks/18-provision.yml`:  
   ```yaml
   ---
   - name: Provision Docker containers
     hosts: localhost
     connection: local
     tasks:
       - name: Create a new container
         docker_container:
           name: provisioned-nginx
           image: nginx:alpine
           state: started
           ports:
             - "8081:80"
   ```  
3. Run karo – naya container bane.  
4. Verify: `docker ps` – `provisioned-nginx` dikhna chahiye.  
5. Extend: use loop to create multiple containers.

**🚩 Flag Verification:**  
- ✅ Playbook se naya container create ho.  
- ✅ Container accessible ho.

---

## Level 29: Linting & Best Practices

**Mission Name:** *The Critic*

**💡 Concept:**  
`ansible-lint` se playbook best practices check kar sakte hain. `yamllint` se YAML syntax. CI/CD mein integrate karte hain.

**🔥 Real‑World Horror Story:**  
Ek team ne playbook push kiya jisme dangerous `rm -rf` tha, linter pakad leta.

**🎯 Practical Task:**  
1. Install linters: `pip install ansible-lint yamllint`  
2. Run on a playbook: `ansible-lint playbooks/12-block.yml`  
3. Observe warnings/errors.  
4. Fix issues (e.g., no tabs, use `name` for tasks, etc.).  
5. Run `yamllint playbooks/12-block.yml` to check YAML formatting.  
6. Create a `.ansible-lint` config file to skip certain rules if needed.

**🚩 Flag Verification:**  
- ✅ Linter passes with zero errors (or acceptable warnings).  
- ✅ Samajh aa gaya linting ka importance.

---

## Level 30: The Grand Orchestration (Final Project)

**Mission Name:** *The Architect*

**💡 Concept:**  
Sab kuch ek saath – deploy a load‑balanced web stack: Nginx (load balancer) + PHP + MySQL. Roles, templates, vault, handlers, conditionals, loops sab use karo.

**🔥 Real‑World Horror Story:**  
Yeh final project hai – isme fail mat hona!

**🎯 Practical Task:**  
1. Inventory: separate groups for `[loadbalancer]`, `[webservers]`, `[database]`.  
2. Create roles: `nginx_lb`, `php_app`, `mysql_db`.  
3. Use vault to store MySQL root password.  
4. Use templates for Nginx config and PHP info page.  
5. Use handlers to restart services on config change.  
6. Use `when` to run tasks only on appropriate groups.  
7. Use `loop` to install multiple PHP extensions.  
8. Playbook `site.yml` jo saare roles include kare.  
9. Run on your Docker containers (assign different containers to different groups).  
10. Verify: access load balancer IP, page should show content, and database connection should work.

**🚩 Flag Verification:**  
- ✅ Web page load ho.  
- ✅ Database se data fetch ho.  
- ✅ Idempotent ho – second run par `changed=0` mostly.  
- ✅ Vault password bina prompt ke use ho.  
- ✅ Handlers sirf tab run ho jab config change ho.

---

# ✅ Final Verification – Kya Kuch Miss Hai?

## Jo Covered Hai:

| Category | Topics Covered |
|----------|----------------|
| **Infrastructure** | Control Node hardening, Docker containers as slaves, SSH keys, static inventory |
| **Ad‑Hoc** | Ping, setup, package, service, file modules |
| **Playbooks** | YAML syntax, idempotency, shell vs command |
| **Variables & Facts** | Variable precedence, facts, Jinja2 templating, filters, tests |
| **Logic** | when, loop, handlers |
| **Security** | Vault (file & variable), vault passwords, no_log |
| **Modularity** | Roles, Galaxy, cross‑platform roles |
| **Error Handling** | block/rescue/always, assert/fail, ignore_errors |
| **Performance** | forks, pipelining, Mitogen, async |
| **Enterprise** | Provisioning, linting, final project |
| **Real‑World** | Horror stories in every level |
| **Gamification** | Mission names, flag verification |
| **Multi‑OS** | Ubuntu, CentOS, Debian containers |

---

## Jo Intentionally Excluded Hain (Kyunki Beginner ke liye nahi / Rarely Used):

| Topic | Reason |
|-------|--------|
| Windows Hosts | Tune Linux containers use kiye hain |
| Ansible Tower / AWX | GUI tool, core Ansible nahi |
| Vault with HSM | 0.01% users use karte hain |
| Custom Python Modules | Algae ka topic hai |
| Ansible Pull | Rarely used, mostly push |
| Kerberos / LDAP | Advanced auth, Level 3 mein hint diya |
| SSH Jumphosts | Advanced networking |

---

