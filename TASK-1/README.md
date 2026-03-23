# 🐧 Linux Commands Guide for DevOps

This task provides a structured guide to essential Linux commands
used in DevOps workflows --- from basics to intermediate system
operations.

------------------------------------------------------------------------

## 📁 Level 1: Basic Linux Commands

### 1. Create a directory

``` bash
mkdir ~/linux_practice
cd ~/linux_practice
```

### 2. Create files

``` bash
touch file1.txt file2.txt file3.txt file4.txt file5.txt
```

### 3. Write content into a file

``` bash
echo "Name: Vinod" > file1.txt
date >> file1.txt
```

### 4. View file contents

``` bash
cat file1.txt
less file1.txt
```

### 5. Copy a file

``` bash
cp file1.txt backup_file1.txt
```

### 6. Rename a file

``` bash
mv file2.txt data.txt
```

### 7. Delete a file

``` bash
rm file5.txt
```

------------------------------------------------------------------------

## 🔐 Level 2: File Permissions & Ownership

### 8. Check permissions

``` bash
ls -l
```

### 9. Change permissions

``` bash
chmod 640 data.txt
```

### 10. Make file executable

``` bash
chmod +x file3.txt
```

### 11. Change ownership

``` bash
sudo chown username:username file4.txt
```

### 12. Permission Meaning

-   `r` → Read\
-   `w` → Write\
-   `x` → Execute

------------------------------------------------------------------------

## 🔍 Level 3: Searching & Viewing Files

### 13. Search text

``` bash
grep "Linux" *.txt
```

### 14. Count lines, words, characters

``` bash
wc file1.txt
```

### 15. View logs

``` bash
tail -n 10 /var/log/syslog
# OR
tail -n 10 /var/log/messages
```

### 16. Find files

``` bash
find ~/linux_practice -name "*.txt"
```

------------------------------------------------------------------------

## ⚙️ Level 4: Process & System Monitoring

### 17. List processes

``` bash
ps aux
```

### 18. Find process

``` bash
ps aux | grep sshd
ps aux | grep systemd
```

### 19. Kill process

``` bash
kill <PID>
```

### 20. Monitor system

``` bash
top
# OR
htop
```

### 21. Disk usage

``` bash
df -h
du -sh ~/linux_practice
```

------------------------------------------------------------------------

## 📦 Level 5: Package Management

### 22. Install package

``` bash
sudo apt install tree -y
# OR
sudo yum install tree -y
# OR
sudo dnf install tree -y
```

### 23. Remove package

``` bash
sudo apt remove tree -y
sudo yum remove tree -y
sudo dnf remove tree -y
```

### 24. Check Git version

``` bash
git --version
```

### 25. List installed packages

``` bash
apt list --installed
yum list installed
dnf list installed
```

------------------------------------------------------------------------

## 🌐 Level 6: Networking Basics

### 26. Check IP address

``` bash
ip addr show
# OR
ifconfig
```

### 27. Test connectivity

``` bash
ping google.com
```

### 28. Check open ports

``` bash
ss -tulnp
# OR
netstat -tulnp
```

### 29. DNS configuration

``` bash
cat /etc/resolv.conf
```

### 30. Download files

``` bash
wget https://example.com/file.txt
# OR
curl -O https://example.com/file.txt
```

------------------------------------------------------------------------

## 🚀 Usage

Clone this repository and practice commands step-by-step:

``` bash
git clone <your-repo-url>
cd linux-commands-guide
```

