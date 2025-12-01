# Mango DB Installation on Windows

Hum do (2) cheezein install karenge: MongoDB Server aur MongoDB Shell (command line se use karne ke liye).

## 💻 Step 1: MongoDB Community Server Download Karna

**Browser Kholo**: Apne web browser mein yeh link type karo (ya click karo):

[https://www.mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)

**Options Select Karo**: Download page par, yeh options select karo:

- **Version**: Latest version (usually default hota hai).
- **OS (Operating System)**: Windows.
- **Package**: MSI (Yah installation file hai).

**Download**: Download button par click karo aur file ko save hone do.

## ⚙️ Step 2: MongoDB Server Install Karna

**Installer Chalao**: Jo file (.msi) download hui hai, us par double-click karo.

**Setup Wizard**: Setup Wizard shuru hoga.

**Setup Type**:
- "Choose Setup Type" screen par, "Complete" option select karo.

**Service Configuration (Zaroori)**:
- "Install MongoD as a Service" option ko checked rehne do.
- "Run service as Network Service user" (default) ko select karo.
- "Service Name" ko MongoDB hi rehne do.

**MongoDB Compass (Optional)**:
- "Install MongoDB Compass" (GUI tool) ko checked rehne do (Yah files/data ko graphical tareeke se dekhne mein madad karta hai).

**Finish**: Install par click karo aur installation poora hone do.

## 🛠️ Step 3: MongoDB Shell (mongosh) Install Karna

MongoDB Shell mongosh zaroori hai commands chalaane ke liye.

**Download Shell**: Browser mein yeh link open karo:

[https://www.mongodb.com/try/download/shell](https://www.mongodb.com/try/download/shell)

**Options Select Karo**:
- **OS**: Windows
- **Package**: MSI

**Installer Chalao**: Jo shell installer file download hui hai, us par double-click karo aur default settings ke saath install kar lo.

## 🧪 Step 4: Verification (Check Karna)

Ab hum check karenge ki sab kuch theek se kaam kar raha hai ya nahi.

**Command Prompt Kholo**: Windows Search mein jao aur cmd type karke Command Prompt (ya PowerShell) kholo.

**Shell Run Karo**: Yeh command type karo:

```bash
mongosh
```

Agar sab kuch theek hai, to tum MongoDB shell ke andar chale jaaoge, aur tumhein kuch aisa dikhega:

```
Current Mongosh version: X.X.X
Connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
...
>
```

**Exit**: Shell se bahar aane ke liye type karo:

```bash
exit
```

Ab tumhara MongoDB Server aur Shell Windows 11 par ready hai!

**Next Step Kya Hoga?**

Kya tum Node.js mein MongoDB connect karne ka code dekhna chahte ho, ya To-Do List project continue karna chahte ho?
