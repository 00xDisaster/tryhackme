# 🧨 FFUF — TryHackMe Room Write-up  
**Author: Disaster** ☠️

> *Learn how the web breaks… then break it better.*  
> — Disaster

---

## 👨‍💻 About Me

I'm **Disaster** — I break systems to understand how they truly work.

🔴 Passionate about **Red Teaming & Penetration Testing**  
🏆 Active **CTF Player**  
✍️ Writing clean, practical, detailed write-ups  
🧠 Focused on **understanding vulnerabilities** — not just solving challenges  
🐧 Very comfortable in **Linux / CLI environments**

> **Learn → Break → Understand → Improve** 💡

## 🌐 Find Me

- **TryHackMe**: [@Disaster](https://tryhackme.com/p/Disvster)  
- **HackTheBox**: [@Disaster](https://app.hackthebox.com/users/3078244)  
- **GitHub**: [@Disaster](https://github.com/00xDisaster)  
- **Medium**: [@Disaster](https://medium.com/@00xdisaster)

---

## 🧪 Room Information

- **Room Name**: ffuf  
- **Room Link**: https://tryhackme.com/room/ffuf

### 📦 Requirements

- 🛠️ [ffuf](https://github.com/ffuf/ffuf)  
- 📚 [SecLists](https://github.com/danielmiessler/SecLists)

---

## 🚀 What is ffuf?

**Fuzz Faster U Fool** — super fast web fuzzer written in Go.

### Installation

- Download prebuilt binary → https://github.com/ffuf/ffuf/releases/latest  
- macOS (Homebrew): `brew install ffuf`  
- Go: `go install github.com/ffuf/ffuf/v2@latest`  
- From source:
  ```bash
  git clone https://github.com/ffuf/ffuf
  cd ffuf
  go get
  go build
  ```
- Requires Go 1.16+
---
## Key Usage Examples
```bash
ffuf -u https://example.com/FUZZ -w wordlist.txt
ffuf -u https://example.com/ -H "Host: FUZZ" -w hosts.txt -mc 200
ffuf -u https://example.com/ -X POST -d '{"key":"FUZZ"}' -fr "error"
```
 - Full help: `ffuf -h`
 - Interactive Mode (press ENTER while running)
```bash
afc  fc  afl  fl  afw  fw  afs  fs  aft  ft  rate  queueshow  queuedel  restart  resume  show  savejson
```
---
## 📚 SecLists
 - Best wordlists collection for security testing.

Quick install:
```bash
git clone --depth 1 https://github.com/danielmiessler/SecLists.git
```
 - Or on Kali:
```bash
sudo apt install seclists
```

---
## 🔍 Walkthrough – Task by Task
### Task 1 – Info

 - ffuf installed ✅
 - SecLists installed ✅

### Task 2 – Basics
```bash
ffuf -u http://MACHINE_IP/FUZZ -w /usr/share/seclists/Discovery/Web-Content/big.txt
```
Answers:

 - First file found with 200 status? → `favicon.ico` 🖼️

### Task 3 – Finding Pages & Directories
  - 1 - General files:
```bash
ffuf -u http://MACHINE_IP/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-files-lowercase.txt
```
  - 2 - Index extensions:
```bash
ffuf -u http://MACHINE_IP/indexFUZZ -w /usr/share/seclists/Discovery/Web-Content/web-extensions.txt
```
  - 3 - 3+4. Directories + extensions:
```bash
ffuf -u http://MACHINE_IP/FUZZ -w /usr/share/seclists/Discovery/Web-Content/raft-medium-words-lowercase.txt -e .php,.txt
```
Answers:

 - Text file found → `robots.txt` 🤖
 - Two extensions for index → `php, phps`
 - Page with size 4840 → `about.php`
 - Number of directories → `4` 🗂️

### Task 4 – Using Filters 
 - Hide 403s: 
```bash
ffuf -u http://MACHINE_IP/FUZZ -w .../raft-medium-files-lowercase.txt -fc 403
```
 - Only 200s:
```bash
ffuf ... -mc 200
```
 - Config folder fuzz:
```bash
ffuf -u http://MACHINE_IP/config/FUZZ -w ... -fc 403
```
 - Hide dotfiles false positives:
```bash
ffuf ... -fr '/\..*'
```
Answers:

 - Results after `-fc 403` → `11`
 - Results after `-mc 200` → `6`
 - Valuable file hidden by `-fc 403` → `wp-forum.phps` ⚠️
### Task 5 – Fuzzing Parameters & Brute Force
 - Parameter discovery:
```bash
ffuf -u 'http://MACHINE_IP/sqli-labs/Less-1/?FUZZ=1' -w /usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt -fw 39
```
 - Value fuzzing (0–255):
```bash
seq 0 255 | ffuf -u '.../Less-1/?id=FUZZ' -w - -fw 33
```
 - Login brute force:
```bash
ffuf -u http://MACHINE_IP/sqli-labs/Less-11/ -X POST \
     -d 'uname=Dummy&passwd=FUZZ&submit=Submit' \
     -w /usr/share/seclists/Passwords/Leaked-Databases/hak5.txt \
     -fs 1435 -H 'Content-Type: application/x-www-form-urlencoded' -c
```
Answers:

 - Parameter found → `id`
 - Highest valid ID → `14`
 - Dummy's password → `p@ssword` 🔓

### Task 6 – Vhosts & Subdomains
 - Subdomain enum:
```bash
ffuf -u http://FUZZ.mydomain.com -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -fs 0
```
Vhost enum:
```bash
 - ffuf -u http://mydomain.com -H "Host: FUZZ.mydomain.com" -w ... -fs 0
```
### Task 7 – Proxifying ffuf

 - Through Burp / proxy:
```bash
ffuf ... -x http://127.0.0.1:8080
```
 - Replay only matches:
```bash
ffuf ... -replay-proxy http://127.0.0.1:8080
```
### Task 8 – Useful Options Review 
 - Save output as markdown: `-of md -o ffuf.md`
 - Reuse raw HTTP request: `-request`
 - Ignore wordlist comments: `-ic`
 - Read wordlist from stdin: `-w -`
 - Verbose (full URLs + redirects): `-v`
 - Follow redirects: `-r`
 - Colorized output: `-c` 🎨

 ---

## Happy fuzzing! 🛠️🔥
Break stuff responsibly.
— Disaster out 🖤
