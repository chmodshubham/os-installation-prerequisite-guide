# **OS Installation Prerequisite Guide**

This guide covers the key steps for configuring your system during the OS installation process, including disabling LVM, enabling OpenSSH, and setting up a static IP address.

---

## **Installation Steps**

### 🔧 **Step 1: Disable LVM Group**

- On the **"Guided Storage Configuration"** screen during installation, make sure to turn **off** the **LVM group** option.

![image](https://github.com/user-attachments/assets/18cd1ae7-cb28-463b-96a6-b2c754af068f)

### 🔧 **Step 2: Enable OpenSSH Server**

- During the package selection stage, ensure you select **OpenSSH Server** to allow secure remote access to the system.

---

## **Static IP Configuration**

![image](https://github.com/user-attachments/assets/627813d0-9b4e-4a09-bd3e-c592077d9838)

### 🖥 **Network Settings Overview**

| **Setting**    | **Value**          |
|----------------|--------------------|
| **Subnet**     | `192.168.1.0/24`   |
| **Address**    | `192.168.1.111` (changable)    |
| **Gateway**    | `192.168.1.1`      |
| **Name Servers** | `8.8.8.8, 8.8.4.4` |
| **Search Domains** | (Leave empty)    |

### 📄 **How to Configure Static IP Through Terminal**

Follow the steps below to set a static IP address using the terminal:

1️⃣ **Switch to root user:**
```bash
sudo su
```

2️⃣ **Navigate to the Netplan configuration directory:**
```bash
cd /etc/netplan/
```

3️⃣ **Open the configuration file in your preferred text editor (e.g., `vim`):**
```bash
vim <file-name>.yaml
```

4️⃣ **Fill in the file with the following configuration:**

```yaml
network:
    version: 2
    ethernets:
        eno1:
            addresses:
            - 192.168.1.111/24
            nameservers:
                addresses:
                - 8.8.8.8
                - 8.8.4.4
                search: []
            routes:
            -   to: default
                via: 192.168.1.1
```

> [!NOTE]  
> Avoid configuring the same default route for multiple interfaces, as it can cause conflicts. Instead, you can specify different routes for each interface by using the `metric` option to prioritize routes or assigning static routes to specific networks.

Example:

```yaml
network:
    version: 2
    ethernets:
        eno1:
            dhcp4: false
            addresses:
              - 192.168.1.111/24
            routes:
            - to: default
              via: 192.168.1.1
            nameservers:
                addresses:
                  - 8.8.8.8
                  - 8.8.4.4
        eno2:
            dhcp4: false
            addresses:
              - 192.168.1.121/24
            nameservers:
                addresses:
                  - 8.8.8.8
                  - 8.8.4.4
```

### 🚫 **Disabling Cloud-Init Network Configuration**

If your system uses **cloud-init**, you need to disable its network configuration to ensure your static IP settings persist across reboots. To do this:

1️⃣ **Create the following file:**
```bash
vim /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
```

2️⃣ **Add the following content:**
```cfg
network: {config: disabled}
```

### ✅ **Apply the Netplan Configuration**

Once you've completed the configuration, apply the changes:

```bash
netplan apply
```

### 🔄 **Reboot the System**

To verify that the static IP persists across reboots, restart the system:

```bash
sudo reboot
```
