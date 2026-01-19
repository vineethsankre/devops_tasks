# 🖥️ System Monitoring Bash Script (TASK:7)

This project contains a **Bash shell script** that automates basic system monitoring and cloud-style operations, similar to what a **DevOps / Cloud Engineer** would do on a Linux server.

---

## 🚀 Installation & Setup

### 1️⃣ Create the Script File
Open your terminal and create a new script file:

```bash
vim monitor.sh
```

Paste the script below into the file:

```bash
#!/bin/bash

# ==============================================================================
# SCRIPT CONFIGURATION
# ==============================================================================

# Define directory and file paths
LOG_DIR="logs"
TIMESTAMP=$(date +"%Y-%m-%d_%H-%M-%S")
REPORT_FILE="$LOG_DIR/system_report_$TIMESTAMP.log"
HIGH_CPU_LOG="high_cpu_process.log"

# Create logs directory if it doesn't exist
mkdir -p "$LOG_DIR"

# ==============================================================================
# LOG ROTATION (Requirement 4)
# ==============================================================================

# Find and delete log files in the logs/ directory older than 7 days
find "$LOG_DIR" -name "system_report_*.log" -type f -mtime +7 -exec rm {} \;

# ==============================================================================
# MAIN MONITORING FUNCTION
# ==============================================================================

generate_report() {
    echo "======================================================================"
    echo "                   SYSTEM MONITORING REPORT                           "
    echo "======================================================================"

    # --- 1. System Information (Requirement 1) ---
    echo ""
    echo "### 1. SYSTEM INFORMATION ###"
    echo "Hostname      : $(hostname)"
    echo "Current Time  : $(date)"
    echo "Uptime        : $(uptime -p)"
    echo "Logged-in User: $(whoami)"
    echo "Active Users  : $(who | awk '{print $1}' | sort | uniq | tr '\n' ' ')"
    echo ""

    # --- 2. Resource Monitoring (Requirement 2) ---
    echo "### 2. RESOURCE USAGE ###"

    # CPU Usage (using top in batch mode to get a snapshot)
    CPU_LOAD=$(top -bn1 | grep "Cpu(s)" | awk '{print $2 + $4}')
    echo "CPU Usage     : $CPU_LOAD%"

    # Memory Usage
    MEM_USED=$(free -h | grep Mem | awk '{print $3 "/" $2}')
    echo "Memory Usage  : $MEM_USED"

    # Disk Usage & Alert
    echo "Disk Usage    :"
    df -h | grep -vE '^Filesystem|tmpfs|cdrom' | awk '{ print $5 " " $1 }' | while read output;
    do
        usage=$(echo $output | awk '{ print $1}' | cut -d'%' -f1)
        partition=$(echo $output | awk '{ print $2 }')

        if [ $usage -ge 80 ]; then
            echo "  [CRITICAL] Partition $partition is at $usage% capacity!"
        else
            echo "  [OK] Partition $partition is at $usage%"
        fi
    done
    echo ""

    # --- 3. Process Management (Requirement 3) ---
    echo "### 3. PROCESS MANAGEMENT ###"
    echo "Top 5 Processes by CPU Usage:"
    echo "--------------------------------------------------"
    ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%cpu | head -n 6
    echo "--------------------------------------------------"

    # Check for High CPU Processes (> 70%)
    ps -eo pid,cmd,%cpu --sort=-%cpu --no-headers | awk '$3 > 70.0 { print $0 }' | while read high_cpu_proc; do
        echo "[$(date)] HIGH CPU ALERT: $high_cpu_proc" >> "$HIGH_CPU_LOG"
        echo "  -> Alert: Process logged to $HIGH_CPU_LOG due to high CPU usage."
    done

    echo ""
    echo "======================================================================"
    echo "Report generated successfully."
}

# ==============================================================================
# EXECUTION & LOGGING (Requirement 4)
# ==============================================================================

# Run the function and save output to the report file AND display it on screen (tee)
generate_report | tee "$REPORT_FILE"
```

Save and exit:

```bash
:wq
```

---

### 2️⃣ Make the Script Executable
Run:

```bash
chmod +x monitor.sh
```

---

## 💻 Usage

To run the script manually and view output in the terminal:

```bash
./monitor.sh
```

The script will print the report to the console and also save it into the `logs/` directory.

---

## 📂 Output Structure

After running the script, your directory will look like this:

```
.
├── monitor.sh                  # The main script
├── high_cpu_process.log        # Created only if a process exceeds 70% CPU
└── logs/                       # Directory created automatically
    ├── system_report_2023-10-27_09-00-00.log
    └── system_report_2023-10-28_09-00-00.log
```

---

## ✅ Notes

- Disk partitions are flagged **CRITICAL** if usage is **80% or more**
- Any process consuming **more than 70% CPU** is logged into `high_cpu_process.log`
- Logs older than **7 days** are automatically deleted (rotation)

---


