# TASK: 7 — System Monitoring + Log Management Script (Bash)

This project contains a Bash shell script that automates basic **system monitoring** and **cloud-style operations** like a DevOps / Cloud Engineer would do on a Linux server.

---

## ✅ Features Covered

### 1️⃣ System Information
Displays:
- Hostname
- Current date and time
- Uptime
- Logged-in users

### 2️⃣ Resource Monitoring
Checks and displays:
- CPU usage
- Memory usage
- Disk usage  
  - Alerts if any partition exceeds **80%**

### 3️⃣ Process Management
- Lists **Top 5 processes** consuming the most CPU
- If any process consumes **more than 70% CPU**, it logs it to:
  - `high_cpu_process.log`

### 4️⃣ Log Management
- Creates `logs/` directory if missing
- Saves full output into:
  - `logs/system_report_&lt;date&gt;.log`
- Deletes logs older than **7 days** (log rotation)

---

## 📁 Project Structure

```bash
.
├── system_monitor.sh
├── high_cpu_process.log
└── logs/
    ├── system_report_2026-01-18_12-30-10.log
    └── system_report_2026-01-18_12-40-55.log
## ✅ Step-by-Step Setup Instructions### Step 1: Create the script fileCreate a new file:nano system_monitor.sh
Paste the full script code inside it and save.### Step 2: Make the script executableRun:chmod +x system_monitor.sh

Step 3: Execute the scriptRun:./system_monitor.sh
## 📄 Output &amp; Logs### ✅ System report logsEach time you run the script, it generates a new report file like:logs/system_report_YYYY-MM-DD_HH-MM-SS.log
Example:logs/system_report_2026-01-18_12-30-10.log
### ⚠️ High CPU process logsIf any process exceeds 70% CPU, it gets logged into:high_cpu_process.log
Format example:2026-01-18 12:30:10 HIGH CPU PROCESS -&gt; 2345 python3 92.4
## 🔁 Log Rotation (Auto Cleanup)The script automatically deletes old report logs inside logs/ directory that are older than 7 days:find logs/ -type f -name "system_report_*.log" -mtime +7 -delete
## 🛠 RequirementsThis script works on most Linux distributions with:bashtopfreedfpsawkfind## 🚀 Optional: Run Automatically (Cron Job)To run the script every 10 minutes:


Step 1: Open crontabcrontab -e
### Step 2: Add this lineReplace /path/to/ with your real path:*/10 * * * * /path/to/system_monitor.sh
## ✅ Verification ChecklistAfter running the script, confirm:logs/ folder exists ✅A report log file is created inside logs/ ✅high_cpu_process.log is created if high CPU processes exist ✅Old logs older than 7 days are removed automatically ✅## 📌 NotesDisk alerts trigger when partition usage is &gt; 80%High CPU logging triggers when process CPU is &gt; 70%Script output is saved to both terminal and log file using tee## 👨‍💻 AuthorCreated for TASK:7 (DevOps Monitoring Automation in Bash)
