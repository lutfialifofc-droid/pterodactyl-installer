# Pterodactyl Auto-Installer (Ubuntu 24.04 LTS)

An automated bash installer script for deploying **Pterodactyl Panel** and **Wings** on a clean **Ubuntu 24.04 LTS (Noble Numbat)** VPS.

---

## 📁 Files Included

- `install.sh`: The complete automated shell installer script.

---

## ⚙️ What the Installer Automates

1. **System & Security Validation**: Checks for root privileges, architecture (`x86_64` or `arm64`), and Ubuntu 24.04 OS compatibility.
2. **Package Stack**:
   - Web Server: **Nginx**
   - Scripting: **PHP 8.3** (FPM + CLI) with all required extensions (`gd`, `mysql`, `pdo`, `mbstring`, `bcmath`, `xml`, `curl`, `zip`, `intl`, `tokenizer`)
   - Database: **MariaDB 10.x+** with auto-generated secure credentials
   - Cache / Queue: **Redis Server**
   - Package Manager: **Composer 2.x**
   - Daemon Engine: **Docker Engine** (via official Docker installer)
   - SSL: **Certbot** with Nginx plugin for automated Let's Encrypt certificates
3. **Panel Installation**:
   - Clones latest official Pterodactyl release
   - Configures `.env` environment file automatically
   - Executes database migrations and base seeders
   - Prompts for the initial administrative account creation
   - Sets up Linux cron schedule runner (`schedule:run`)
   - Configures systemd queue worker daemon (`pteroq.service`)
4. **Wings Daemon Installation**:
   - Installs Docker CE
   - Downloads the native Wings binary
   - Configures and enables `wings.service` under systemd

---

## 🚀 How to Use on Your VPS

### 1. Upload `install.sh` to your VPS

Choose any of the following methods to transfer the script:

#### Option A: Using SCP / PowerShell from your local machine
```powershell
scp "C:\Addons ptero\install.sh" root@YOUR_SERVER_IP:/root/
```

#### Option B: Using SFTP / FileZilla / MobaXterm / Termius
Connect to your VPS as `root` and drag-and-drop `install.sh` into `/root/`.

#### Option C: Copy & Paste directly inside SSH
On your VPS terminal:
```bash
nano /root/install.sh
# Paste the content of install.sh, then press CTRL+O, ENTER, CTRL+X
```

---

### 2. Run the Installer

On your Ubuntu 24.04 VPS terminal:

```bash
# Move to the directory
cd /root

# Make executable and fix line endings (if uploaded from Windows)
chmod +x install.sh
sed -i 's/\r$//' install.sh

# Run the installer as root
sudo ./install.sh
```

---

### 3. Interactive Menu Options

When launched, the script presents an interactive menu:

```text
  [1] Install Pterodactyl Panel
  [2] Install Wings (Daemon)
  [3] Install Both (Panel + Wings on this VPS)
  [4] Update Existing Panel
  [0] Exit
```

* **Option 1**: Use this if you only want the Web Panel on this VPS.
* **Option 2**: Use this if this machine is purely a game compute node (Wings daemon).
* **Option 3**: Use this if this is an all-in-one VPS (both Panel and Wings on the same machine).

---

## 🛡️ Recommended Firewall Ports (UFW)

Make sure to open the necessary ports on your VPS / Cloud Provider (AWS, Oracle, Hetzner, Contabo, etc.):

```bash
# Web Panel & SSL
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Wings Daemon (API & SFTP)
sudo ufw allow 8080/tcp
sudo ufw allow 2022/tcp

# Game Ports (Example range for game servers)
sudo ufw allow 25565:25595/tcp
sudo ufw allow 25565:25595/udp

# SSH
sudo ufw allow 22/tcp

sudo ufw enable
```

---

## 📝 Post-Installation Checklist

1. **Access the Panel**: Navigate to `http://YOUR_SERVER_IP` or `https://YOUR_DOMAIN.COM` in your web browser and log in with the admin credentials you created.
2. **Add Node in Panel**:
   - Go to **Admin Area** (`/admin`) -> **Nodes** -> **Create New**.
   - Set the FQDN/IP and port (`8080`).
   - Click the **Configuration** tab, copy the generated config block, and paste it into `/etc/pterodactyl/config.yml` on the node.
   - Start Wings:
     ```bash
     systemctl start wings
     systemctl status wings
     ```
