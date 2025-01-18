1. Add a new sudo user
```bash
adduser liel
usermod -aG sudo liel
```

2. Allow the user ssh
```bash
su - liel
mkdir ~/.ssh
chmod 700 ~/.ssh
```

3. Update machine
```bash
sudo apt update && sudo apt upgrade -y
```

4. Install packages
```bash
sudo apt install vim firewalld fail2ban openssh-server
```

5. Enable ssh
```bash
sudo systemctl enable ssh
sudo systemctl start ssh
```

6. Update ssh settings 
Generate a public key on local PC beforehand
```bash
ssh-keygen -t rsa -b 4096 -C "liel@lielamar.com"
type C:\Users\Liel\.ssh\id_rsa.pub | ssh -p 1803 liel@192.168.1.196 "cat >> .ssh/authorized_keys"
```

Update configuration
```bash
vim /etc/ssh/sshd_config
   Port 1803
   PermitRootLogin no
   PasswordAuthentication no
```

Restart service
```bash
sudo systemctl restart ssh
```

7. Add firewall rules
```bash
sudo systemctl enable firewalld
sudo systemctl start firewalld
sudo firewall-cmd --permanent --add-port=443/tcp
sudo firewall-cmd --permanent --add-port=1194/udp
sudo firewall-cmd --permanent --add-port=1803/tcp
sudo firewall-cmd --permanent --add-port=3389/tcp
sudo firewall-cmd --reload
```

8. Enable xrdp
```bash
sudo systemctl enable xrdp
sudo systemctl start xrdp
sudo adduser liel xrdp
```

9. Set fail2ban to prevent brute-force attacks
```bash
sudo vim /etc/fail2ban/jail.local
    [sshd]
    enabled = true
    port = 1803
    logpath = /var/log/auth.log
    maxretry = 5
    bantime = 3600
```

Restart service
```bash
sudo systemctl restart fail2ban
sudo systemctl enable fail2ban
```

10. Install docker
Install dependencies
```bash
sudo apt install -y ca-certificates curl gnupg lsb-release
```

Add Docker's official GPG key
```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

Setup docker repository
```bash
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Install docker engine
```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Test docker installation
```bash
sudo docker run hello-world
```

Add user to docker group
```bash
sudo usermod -aG docker $USER
```

11. Install docker compose
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

12. Run everything
```bash
docker-compose up -d
```