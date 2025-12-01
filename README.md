# DevOps & Node.js Deployment Documentation

This repository contains structured documentation for setting up MongoDB, Node.js (via NVM), and deploying applications on Windows and Ubuntu EC2 instances. It also covers troubleshooting, multi-user environments, and process management with PM2 and Forever.

## 📚 Documentation Index

| File | Description |
|------|-------------|
| [MongoDB Installation on Windows](docs/mongodb-installation-windows.md) | Step-by-step guide to installing MongoDB Server and Shell on Windows. |
| [MongoDB Installation on EC2](docs/mongodb-installation-ec2.md) | Guide for installing MongoDB on Ubuntu EC2, including service setup and security. |
| [Node.js & NVM Setup](docs/node-nvm-setup.md) | Instructions for removing system-wide Node.js and setting up NVM for multiple users. |
| [Multi-User App Deployment](docs/app-deployment-multi-user.md) | Deploying Node.js apps for specific users, cloning repos, and environment configuration. |
| [Troubleshooting & Advanced Setup](docs/troubleshooting-and-advanced-setup.md) | Solutions for sudo errors, Nginx reverse proxy setup, and PM2 vs Forever comparison. |

---

## 🚀 Quick Command Reference

### MongoDB
```bash
# Start MongoDB Service (Linux)
sudo systemctl start mongodb

# Check Status
sudo systemctl status mongodb

# Connect to Shell
mongosh
```

### Node.js & NVM
```bash
# Install NVM
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
source ~/.nvm/nvm.sh

# Install Node.js Version
nvm install 18
nvm use 18

# Check Version
node -v
```

### Process Management (PM2)
```bash
# Start App
pm2 start server.js --name myapp

# View Logs
pm2 logs myapp

# List Processes
pm2 list

# Setup Auto-start
pm2 startup systemd
pm2 save
```

### Process Management (Forever)
```bash
# Start App
forever start server.js

# List Processes
forever list

# Stop App
forever stop server.js
```

### System & Permissions
```bash
# Update Packages
sudo apt update && sudo apt upgrade -y

# Add User to Sudo Group
sudo usermod -aG sudo <username>
```

---

## 🛠️ Troubleshooting & Issues

### 1. Sudo Permission Error
**Issue:** User cannot run sudo commands (`user is not in the sudoers file`).
**Fix:** Log in as ubuntu/root and run:
```bash
sudo usermod -aG sudo <username>
```

### 2. MongoDB Installation Error on Ubuntu
**Issue:** `Package mongodb is not available`.
**Fix:** Ensure you are using the correct repository or install via `apt install -y mongodb` after updating. If issues persist, check `mongodb-org` packages (though the guide covers the default repo method).

### 3. App Port Conflicts
**Issue:** Running multiple apps on the same port.
**Fix:** Use different ports (e.g., 3000, 4000) or use Nginx as a reverse proxy to route traffic from port 80 to different internal ports.

---

## 🔐 Security & Best Practices

- **MongoDB Bind IP:** Keep `bindIp` set to `127.0.0.1` to prevent unauthorized remote access.
- **NVM Isolation:** Use NVM to manage Node.js versions per user to avoid system-wide conflicts.
- **Process Managers:** Always use PM2 or Forever to ensure apps restart after crashes or reboots.
- **Nginx Reverse Proxy:** Use Nginx to serve apps on port 80/443 instead of exposing application ports directly.

---

## 🔮 Future Recommendations

- **Database Persistence:** Move from in-memory storage to a persistent MongoDB database (local or Atlas).
- **SSL/TLS:** Implement SSL certificates (Let's Encrypt) for secure HTTPS connections.
- **CI/CD:** Automate deployment using GitHub Actions.
- **Monitoring:** Set up PM2 monitoring or other tools to track server health.
