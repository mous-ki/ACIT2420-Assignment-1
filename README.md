
# **Setting Up a DigitalOcean Droplet with Arch Linux**
**Miles Sasaki, 2024**

## **Overview**
In this guide, I’ll show you how to create a DigitalOcean droplet with Arch Linux. We’ll set it up to use SSH keys for secure access and automate some initial tasks with cloud-init. Once finished, you’ll have a working server that doesn’t require passwords to log in.

---

## **Step 1: Generating SSH Keys**

SSH keys are used to securely connect to the server. First, we need to generate a key pair on your local machine.

### **Generating SSH Key Pair**

Open your terminal (or PowerShell if on Windows) and run the following command:

```bash
ssh-keygen -t rsa -b 4096
```

This will create two files: a private key (id_rsa) and a public key (id_rsa.pub). When it asks where to save, just hit Enter to use the default. If you want extra protection, you can add a passphrase, but it's optional.


### **Grab the Public Key**

To log into the server later, we'll need your public key. To get it, run this:

```bash
cat ~/.ssh/id_rsa.pub
```

Copy the key that begins with `ssh-rsa`. Save this text — you’ll need it for the next step.

---

## **Step 2: Adding Your SSH Key to DigitalOcean**

1. Sign into **[DigitalOcean](https://www.digitalocean.com/)**.
2. Navigate to **Settings > Security > SSH Keys**.
3. Click **Add SSH Key** and paste the public key you copied earlier.
4. Give it a name you’ll recognize/remember (e.g., "my_laptop_key").

---

## **Step 3: Creating a Custom Droplet Running Arch Linux**

Now that your SSH key is added, we can create a droplet.

1. Head to the **Droplets** page and click **Create Droplet**.
2. For the operating system, choose **Arch Linux** from the **Custom Images** section.
3. Select a plan that meets your needs (the $5/month plan is perfect for this project).
4. In the **Authentication** section, pick **SSH Key** and select the key you added earlier.
5. Scroll down to  **Advanced Options**.

---

## **Step 4: Automating Setup using Cloud-Init**

Cloud-init helps us automate basic setup tasks like creating a user and installing necessary software.

### **Adding the Cloud-Init Configuration**

In the User Data field under Advanced Options, paste this configuration:

```yaml
#cloud-config
users:
  - name: newuser
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    ssh_authorized_keys:
      - ssh-rsa your-public-key-here
package_update: true
packages:
  - sudo
  - git
ssh_pwauth: false
```

This script does a few things:
- Creates a new user (`newuser`) with sudo access and no password required.
- Installs `sudo` and `git`.
- Turns off password login for SSH. Make sure to replace 'your-public-key-here' with your actual public key. 

---

## **Step 5: Connecting to Your Droplet**

Once your droplet is ready, you’ll get its IP address from DigitalOcean.

To connect to the server, run this command:

```bash
ssh newuser@your-droplet-ip
```

Replace `your-droplet-ip` with the actual IP address you got earlier. If everything is set up properly, you should be logged into your new server without needing a password.

---


## **Conclusion**

And that’s it! You’ve successfully set up a secure DigitalOcean droplet running Arch Linux, added SSH key access, and used cloud-init to handle some initial tasks. Your server is now ready to go, and you can run any projects you need to.

--- 
## **References**

DigitalOcean. (n.d.). *How to add SSH keys to Droplets*. DigitalOcean. Retrieved October 1, 2024, from https://docs.digitalocean.com/products/droplets/how-to/add-ssh-keys/

OpenSSH. (n.d.). *OpenSSH key management*. Retrieved October 1, 2024, from https://www.ssh.com/academy/ssh/keygen

DigitalOcean. (n.d.). *How to use cloud-config for your initial server setup*. DigitalOcean. Retrieved October 1, 2024, from https://www.digitalocean.com/community/tutorials/how-to-use-cloud-config-for-your-initial-server-setup

cloud-init. (n.d.). *Cloud-init documentation*. cloud-init. Retrieved October 1, 2024, from https://cloudinit.readthedocs.io/en/latest/

Arch Linux. (n.d.). *Arch Linux installation guide*. Arch Linux. Retrieved October 1, 2024, from https://wiki.archlinux.org/title/Installation_guide

