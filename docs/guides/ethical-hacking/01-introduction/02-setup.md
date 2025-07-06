---
sidebar_position: 2
description: "A guide for setting up a local pentesting lab using Kali Linux and Metasploitable2 with VirtualBox."
---

# Setting up the lab

This guide will walk you through creating a local pentesting lab using two virtual machines: **Kali Linux** (attacker) and **Metasploitable2** (vulnerable target).
Perfect for testing exploits, learning tools, and practicing ethical hacking techniques safely.

## 🧩 Step 1: Install VirtualBox

1. Go to the official [VirtualBox website](https://www.virtualbox.org/).
2. Download the latest version for your OS (Windows, Linux, macOS).
3. Install it using the default settings.
4. (Optional but recommended) Install the **Extension Pack** for additional features like USB support and drag-and-drop.

## 🐉 Step 2: Download Kali Linux VM

1. Visit the [Kali Linux Virtual Machines download page](https://www.kali.org/get-kali/#kali-virtual-machines).
2. Choose the **VirtualBox version** and download the `.7z` file.
3. Extract the archive using a tool like 7-Zip or `unzip`.
4. You’ll get an `.ova` file. Open VirtualBox and go to:

```txt
File -> Import Appliance -> Select the .ova file -> Next -> Import
```

5. Start the VM and log in with:

```txt
Username: kali
Password: kali
```

## 💣 Step 3: Download Metasploitable2 VM

1. Go to the [Metasploitable2 download page](https://sourceforge.net/projects/metasploitable/).
2. Download the `.zip` archive and extract it.
3. Inside, locate the `.vmdk` and `.vmx` files.
4. In VirtualBox:
   - Click **New**
   - Name: `Metasploitable2`
   - Type: `Linux`
   - Version: `Ubuntu (32-bit)`
   - Memory: `512-1024 MB` (enough for basic use)
   - Choose **Use an existing virtual hard disk file** and select the `.vmdk` file from the extracted folder.
5. Finish setup and start the VM.
6. Login credentials:

```txt
Username: msfadmin
Password: msfadmin
```

## 🔧 Step 4 : Configure Static IP Networking (Host-Only Adapter)

### 📌 Create a Host-Only Network

1. In **VirtualBox**, go to:

   ```txt
   File -> Tools -> Host Network Manager
   ```

2. Click **Create**. It will make a network like:

   ```txt
   vboxnet0  -  IP: 192.168.56.1  -  Netmask: 255.255.255.0
   ```

3. **Disable DHCP** for that network.

### 🔌 Attach the Network to Both VMs

For **both Kali and Metasploitable2**:

1. Open **Settings -> Network**.
2. Enable **Adapter 1**, set it to **Host-only Adapter**.
3. Choose **vboxnet0**.
4. (Optional) Disable all other adapters if you want full isolation.

## 🧰 Step 5: Set Static IPs in Each VM

### 🐉 Kali Linux

1. Open a terminal.
2. Input the following commands:

```sh
nmcli connection modify "Wired connection 1" \
ipv4.addresses 192.168.56.10/24 \
ipv4.gateway 192.168.56.1 \
ipv4.method manual \
ipv4.dns 8.8.8.8
nmcli connection down "Wired connection 1" && nmcli connection up "Wired connection 1"
```

### 💣 Metasploitable2

Metasploitable2 uses the old `interfaces` system:

1. Log in: `msfadmin / msfadmin`
2. Edit interfaces file:

```bash
sudo vim /etc/network/interfaces
```

Change to:

```bash
auto eth0
iface eth0 inet static
  address 192.168.56.20
  netmask 255.255.255.0
  gateway 192.168.56.1
```

3. Restart networking:

```bash
sudo /etc/init.d/networking restart
```

### 🌐 Final Network Overview

| VM              | IP Address      | Notes                     |
| --------------- | --------------- | ------------------------- |
| Kali Linux      | `192.168.56.10` | Attacker machine          |
| Metasploitable2 | `192.168.56.20` | Vulnerable target         |
| Host-only NIC   | `192.168.56.1`  | VirtualBox host interface |

## 🧪 Step 6: Test Connection

On Kali:

```bash
ping 192.168.56.20
```

You should get replies from Metasploitable2.

## ✅ Step 7: Take Snapshots

Once the VMs are configured, take **snapshots** so you can revert to a clean state anytime.

- Right-click VM -> **Take Snapshot**

## 🧠 You’re Ready

You now have a working local hacking lab. From here, you can:

- Use **Nmap** to scan Metasploitable2
- Run **Metasploit** against known services
- Practice enumeration, exploitation, and post-exploitation
- ...
