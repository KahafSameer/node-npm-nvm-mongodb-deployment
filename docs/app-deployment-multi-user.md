# NVM Version Management of Node

Zabardast boss 👌 — ab tum apna **URL shortener app** jo pehle banaya tha, usko **EC2 ke naye users (user4 / user5)** ke under clone karke test karna chahte ho. Chalo stepwise guide banate hain:

---

## 🛠 Step 1: App Clone karna

Har user ke under alag‑alag clone karna hoga:

```bash
# Login as user4
ssh -i your-key.pem user4@<EC2-PUBLIC-IP>

# App clone karo (GitHub se)
git clone https://github.com/<tumhara-repo>/url-shortener.git

cd url-shortener
```

Same steps user5 ke liye bhi repeat karo.

---

## 🛠 Step 2: Dependencies Install

App folder ke andar:
```bash
npm install
```

Ye tumhare `package.json` ke hisaab se saari dependencies install karega.

---

## 🛠 Step 3: Environment Variables Set

Agar tum MongoDB use karna chahte ho to connection string set karo:

```bash
export MONGO_URL="mongodb://127.0.0.1:27017/shortener"
```

Ya agar Atlas use karna hai to Atlas ka connection string paste karo.

---

## 🛠 Step 4: App Run karna

PM2 ke sath run karo (recommended):

```bash
pm2 start server.js --name shortener
```

Logs check karo:
```bash
pm2 logs shortener
```

Expected output:
```
MongoDB connected
Server running on port 4000
```

---

## 🛠 Step 5: Browser Test

- EC2 ka **public IP** lo.
- Agar Nginx reverse proxy set hai → `http://<EC2-PUBLIC-IP>/`
- Agar direct run hai → `http://<EC2-PUBLIC-IP>:4000` (ya jo port tumne set kiya hai).

---

## 🎯 Result

- Tumhara **URL shortener app** ab user4 / user5 ke under independently run karega.
- Dono apne apne Node.js versions ke sath test kar sakte hain.
- MongoDB ke sath data persist hoga, reboot ke baad bhi links save rahenge.

---

👉 Boss, kya tum chahte ho main tumhe ab **Nginx multi‑user config** bana ke du taake user4 aur user5 ke apps alag paths (`/user4`, `/user5`) pe serve ho EC2 ke public IP par?
