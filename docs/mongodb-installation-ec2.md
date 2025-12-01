# MongoDB Installation on EC2 Ubuntu (user1, step by step)

Thik hai bhai — pehle EC2 pe MongoDB install karte hain, phir app me integrate karenge. Slow, stepwise, bina jump kiye.

---

## 1. System update

- **Update packages:**
  ```bash
  sudo apt update && sudo apt upgrade -y
  ```

---

## 2. MongoDB install

- **Install MongoDB server (Ubuntu repo):**
  ```bash
  sudo apt install -y mongodb
  ```

**Error aye ga yaha pr:**
```
ubuntu@ip-172-31-38-139:~$ sudo apt install -y mongodb
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Package mongodb is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source

E: Package 'mongodb' has no installation candidate
ubuntu@ip-172-31-38-139:~$
```

**Solution:**

*(Note: The original text had "solution:" followed by blank lines. Preserving as is.)*

- **Start aur enable service:**
  ```bash
  sudo systemctl start mongodb
  sudo systemctl enable mongodb
  ```

- **Status check:**
  ```bash
  sudo systemctl status mongodb
  ```
  - Expected: “active (running)”

---

## 3. Local security check

- **Default bind IP:** MongoDB by default 127.0.0.1 pe bind hota hai (local machine only). Ye safe hai; abhi hum remote access nahi khol rahe.
- **Port:** 27017 local pe open hota hai. EC2 security group me isko open karne ki zarurat nahi (app same server pe hai).

---

## 4. Basic test (mongo shell)

- **Connect to local MongoDB:**
  ```bash
  mongosh
  ```
  Agar `mongosh` na mile:
  ```bash
  sudo apt install -y mongodb-clients
  mongosh
  ```

- **Quick DB test:**
  ```javascript
  use shortener
  db.urls.insertOne({ originalUrl: "https://example.com", shortId: "ex1" })
  db.urls.find()
  ```

- **Exit:**
  ```bash
  exit
  ```

---

## 5. App ke liye database aur user (optional but good practice)

Agar tum app user create karna chahte ho:

- **Admin login:**
  ```bash
  mongosh
  ```

- **Create app DB and user:**
  ```javascript
  use shortener
  db.createUser({
    user: "appuser",
    pwd: "Strong_Password_ChangeKaro",
    roles: [{ role: "readWrite", db: "shortener" }]
  })
  exit
  ```

- **Connection string (local):**
  ```
  mongodb://appuser:Strong_Password_ChangeKaro@127.0.0.1:27017/shortener
  ```

---

## 6. Node.js app integration (minimal wiring now, code later)

- **Mongoose install (app folder me):**
  ```bash
  npm install mongoose
  ```

- **Env variable set (temporary, shell session):**
  ```bash
  export MONGO_URL="mongodb://127.0.0.1:27017/shortener"
  ```
  Ya agar user bana liya hai:
  ```bash
  export MONGO_URL="mongodb://appuser:Strong_Password_ChangeKaro@127.0.0.1:27017/shortener"
  ```

- **server.js me connection (example):**
  ```javascript
  const mongoose = require('mongoose');
  const mongoUrl = process.env.MONGO_URL || 'mongodb://127.0.0.1:27017/shortener';

  mongoose.connect(mongoUrl, { useNewUrlParser: true, useUnifiedTopology: true })
    .then(() => console.log('MongoDB connected'))
    .catch(err => console.error('MongoDB error:', err));
  ```

---

## 7. Service hardening (later, jab base chal jaye)

- **Auth enable + config file:**
  - File: `/etc/mongodb.conf` ya `/etc/mongod.conf` (Ubuntu variant)
  - Ensure:
    - **bindIp:** `127.0.0.1`
    - **authorization:** enabled
  - Restart:
    ```bash
    sudo systemctl restart mongodb
    ```
- Abhi hum local-only rakhenge; remote exposure bilkul nahi.

---

## 8. Verify end-to-end

- **MongoDB status:** `sudo systemctl status mongodb`
- **Shell test:** `mongosh` → `use shortener` → `db.stats()`
- **App start:** PM2/Forever jo use kar rahe ho:
  ```bash
  pm2 restart nodeapp
  ```
  Logs check:
  ```bash
  pm2 logs nodeapp
  ```
  Expected: “MongoDB connected”

---

## 9. Common issues quick-fix

- **mongosh not found:** `sudo apt install -y mongodb-clients`
- **Service inactive:** `sudo systemctl start mongodb`
- **Connection refused:** Check `bindIp` is `127.0.0.1`, and app connecting to `127.0.0.1:27017`
- **Auth errors:** If user enabled, use correct username/password in connection string.

---

Bhai, ye base setup ho jaye to message drop karna: “Mongo connected” logs aa rahe hain ya koi error. Phir hum schema + routes ko MongoDB pe shift karenge step-by-step (in-memory se persistent storage).
