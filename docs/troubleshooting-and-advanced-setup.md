# Troubleshooting and Advanced Setup

## 1. User Sudo Permission Error

**Error:**
```bash
user1@ip-172-31-38-139:~$ sudo apt update && sudo apt install -y nodejs npm
[sudo] password for user1:
user1 is not in the sudoers file.
user1@ip-172-31-38-139:~$
```

**Solution:**
Switch to ubuntu login and add user to sudo:
```bash
sudo usermod -aG sudo user1
```

---

## 2. Repo Analysis (links-shorten-app-ec2-deployment)

**Quick Take:** Tumhara app repo clean aur structured hai 👌. Ye ek **Node.js + Express based URL shortener** hai, Tailwind CSS UI ke sath. Agar tum user1 hi use karoge EC2 pe, toh deployment simple ho jaayega — bas Node.js install, repo clone, dependencies install, aur run/start service karna hoga.

- **Tech Stack**:
  - Backend: Node.js, Express.js
  - Frontend: HTML5, Vanilla JS, Tailwind CSS
  - Styling: Dark theme + animations
  - Tools: npm, Git

- **Features**:
  - 🔗 Instant URL shortening (unique short IDs)
  - ⚡ Fast redirection
  - 🎨 Premium dark-mode UI
  - 📋 One-click copy for links
  - 🛡 Error handling (404 pages)
  - 💾 In-memory storage (can extend to DB later)

- **Repo Files**:
  - `server.js` → main entry point
  - `views/` → frontend templates
  - `public/` → static assets (CSS/JS)
  - `test.js` → testing logic
  - `package.json` → dependencies + scripts
  - `README.md` → setup instructions

---

## 3. Deployment Plan (EC2, user1)

Since tumne decide kiya hai **user1** hi use karna, steps ye rahenge:

### 1. Login EC2 as user1
```bash
ssh -i your-key.pem user1@<EC2-PUBLIC-IP>
```

### 2. Install Node.js & npm
```bash
sudo apt update && sudo apt install -y nodejs npm
node -v
npm -v
```

### 3. Clone Repo
```bash
git clone https://github.com/KahafSameer/links-shorten-app-ec2-deployment.git
cd links-shorten-app-ec2-deployment
```

### 4. Install Dependencies
```bash
npm install
```

### 5. Run App
```bash
node server.js
```
Browser: `http://<EC2-PUBLIC-IP>:3000`

### 6. Background Service (Optional)
```bash
npm install -g pm2
pm2 start server.js
pm2 status
```

---

## 4. Next Steps
- **Security**: Agar user1 hi use karna hai, toh ensure karo ke uske permissions aur SSH keys secure hon.
- **Persistence**: pm2 ya systemd use karo taake app reboot ke baad bhi chale.
- **Scaling**: Future me DB (MongoDB/Postgres) add karna easy hoga, kyunki abhi in-memory storage hai.

👉 Kahaf, tum chaho to main tumhe **pm2 + Nginx reverse proxy setup** bhi bata dun taake app production-grade ho jaaye. Kya tum abhi simple run test karna pasand karoge ya direct production‑style setup seekhna chahte ho?

---

## 5. Nginx Reverse Proxy Setup (Parallel Run)

Bilkul sahi soch hai Kahaf 👍 — tum chahte ho ke **jo app abhi chal raha hai wo disturb na ho**, aur ek **new directory me same app ko Nginx ke sath run karo**. Ye approach safe hai, kyunki tum ek tarah se “duplicate deployment” karoge: ek test instance (jo abhi chal raha hai) aur ek production‑style instance (Nginx ke sath).

### 🛠 Stepwise Plan

#### 1. New Directory Banaana
EC2 pe user1 ke under:
```bash
cd ~
mkdir nodeapp-nginx
cd nodeapp-nginx
```

#### 2. Repo Clone Karna (fresh copy)
```bash
git clone https://github.com/KahafSameer/links-shorten-app-ec2-deployment.git .
npm install
```

#### 3. App Run Karna (Port Change)
Taake existing app (port 3000) disturb na ho, isko **different port** pe run karo (e.g. 4000):

`server.js` me port change:
```javascript
const PORT = 4000;
```

Run test:
```bash
node server.js
```
Browser: `http://<EC2-PUBLIC-IP>:4000`

#### 4. Nginx Reverse Proxy Setup
Nginx config file banao:
```bash
sudo nano /etc/nginx/sites-available/nodeapp-nginx
```

Config:
```nginx
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://127.0.0.1:4000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

Enable config:
```bash
sudo ln -s /etc/nginx/sites-available/nodeapp-nginx /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

### ✅ Result
- Tumhara **pehla app** abhi bhi port 3000 pe chal raha hai (direct browser access).
- Tumhara **new app** Nginx ke through port 80 pe accessible hai:
  ```
  http://<EC2-PUBLIC-IP>/
  ```

---

## 6. PM2 vs Forever

Bilkul 👍 — chalo easy wording me samjhata hoon taake tumhe clear ho jaaye:

### 🔑 PM2 aur Forever kya hote hain?

#### 🟢 **PM2**
- Ye ek **process manager** hai Node.js apps ke liye.
- Matlab: tumhe `node server.js` manually chalane ki zarurat nahi hogi.
- Ye app ko **background me run** karata hai, aur agar server reboot ho jaaye to app **automatically restart** ho jaata hai.
- Extra features:
  - Logs dekhna (`pm2 logs`)
  - Multiple apps manage karna (`pm2 list`)
  - Auto‑restart agar crash ho jaaye
  - Easy monitoring
Soch lo PM2 ek **smart manager** hai jo tumhare Node.js app ko hamesha zinda rakhta hai.

#### 🔵 **Forever**
- Ye bhi ek **simple process manager** hai Node.js ke liye.
- Naam ke hisaab se: app ko “forever” chalata hai.
- Agar app crash ho jaaye to restart kar deta hai.
- Thoda lightweight hai, PM2 ke comparison me kam features deta hai.
- Mainly use hota hai jab tumhe sirf ek app ko background me chalana ho bina monitoring ke.

### ⚖️ Difference (Easy Words)

| Tool     | Use Case | Features |
|----------|----------|----------|
| **PM2**  | Production apps | Auto‑restart, monitoring, logs, multiple apps |
| **Forever** | Simple background run | Auto‑restart, lightweight, fewer features |

👉 Easy analogy:
- **Forever** = ek simple watchman jo sirf app ko chalata rakhta hai.
- **PM2** = ek full manager jo app ko chalata bhi hai, monitor bhi karta hai, aur crash hone pe handle bhi karta hai.

---

## 7. PM2 Setup Guide

Perfect Kahaf 👌 — chalo ab tumhare app ko **PM2** ke sath run karte hain. Ye tumhe production‑style stability dega: app background me chalega, crash hone pe auto‑restart hoga, aur reboot ke baad bhi survive karega.

### 1. PM2 Install karo (global)
```bash
npm install -g pm2
```
Check version:
```bash
pm2 -v
```

### 2. App ko PM2 ke sath start karo
App directory me jao (jo Nginx ke sath run karna hai):
```bash
cd ~/nodeapp-nginx
pm2 start server.js --name nodeapp
```
👉 `--name nodeapp` se tumhe ek friendly name milta hai instead of process ID.

### 3. Status aur Logs dekhna
```bash
pm2 list
pm2 logs nodeapp
```

### 4. App ko manage karna
- Stop: `pm2 stop nodeapp`
- Restart: `pm2 restart nodeapp`
- Delete: `pm2 delete nodeapp`

### 5. Auto‑start on Reboot
```bash
pm2 startup systemd
```
Ye command tumhe ek line output karega (jaise `sudo env PATH=$PATH:/usr/bin pm2 startup systemd -u user1 --hp /home/user1`).
Us line ko copy‑paste karke run karo.

Phir save current processes:
```bash
pm2 save
```

### ✅ Result
- Tumhara app ab **background me run** karega.
- Agar crash ho jaaye → PM2 auto‑restart karega.
- Agar EC2 reboot ho jaaye → PM2 startup script se app auto‑start ho jaayega.
- Nginx ab hamesha port 80 pe serve karega, bina manual `node server.js` chalaye.

---

## 8. Forever Setup Guide

Zabardast Kahaf 👌 — ab tum apne **dusre folder wale app** ko port **3000** par **Forever** ke sath test karna chahte ho. Chalo stepwise easy guide deta hoon:

### 1. Forever Install karo (user1 ke under)
```bash
npm install -g forever
```
Check version:
```bash
forever --version
```

### 2. App Directory me jao
Tumne jo dusra folder banaya tha (port 3000 wala):
```bash
cd ~/nodeapp   # ya jo bhi folder ka naam hai
```

### 3. Forever ke sath App Start karo
```bash
forever start server.js
```
👉 Ye tumhara app background me run karega port 3000 par.
Ab tum terminal band bhi kar do, app chalti rahegi.

### 4. Status Check karo
```bash
forever list
```
Ye tumhe dikhayega kitne apps Forever ke sath chal rahe hain.

### 5. Logs dekhna
```bash
forever logs
```
Ya specific app ke liye:
```bash
forever logs 0
```
(0 = process ID jo `forever list` me dikhega)

### 6. Manage karna
- Stop: `forever stop server.js`
- Restart: `forever restart server.js`
- Delete (remove from list): `forever stopall`

### 🎯 Result
- Tumhara **dusra app** ab port 3000 par Forever ke sath background me chal raha hoga.
- Tumhare paas ab **do setups parallel** hain:
  - Port 4000 → PM2 ke sath (Nginx reverse proxy)
  - Port 3000 → Forever ke sath (direct run)

👉 Kahaf, ab tum dono tools ka **real comparison** kar sakte ho:
- PM2 → monitoring, auto‑restart on reboot, logs, multiple apps manage.
- Forever → simple background run, lightweight, basic restart.
