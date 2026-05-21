# SOC-Analysis-Project-On-Unauthorized-Process-Detection
This project aims to strengthen 10ALYTICS-DC  security monitoring by leveraging Wazuh’s command monitoring and response capabilities to detect unauthorized processes and block malicious actors. It covers the steps taken to configure Wazuh for process monitoring through security alerts and logs showing Netcat activities.
STEP I
I configured Kali as my Wazuh agent using the following command to get a list of running processes.
sudo nano /var/ossec/etc/ossec.conf
<ossec_config>
<localfile>
<log_format>full_command</log_format>
<alias>process list</alias>
<command> ps -e -o pid, uname, command</command>
<frequency>30</frequency>
</localfile>
</ossec_config>
STEP II
I installed Netcat using sudo apt install ncat nmap -y
STEP III
On Wazuh server, I ran the following command that triggers an alert each time Netcat program launches.
sudo nano var/ossec/etc/rules/local_rules.xml
<group name =”ossec,”>
<rule id=”100050” level=”0”>
<if_sid>530</if_sid>
<match>^ossec: ‘process list’</match>
<description>list of running processes.</description>
<group>process_monitor,</group>
</rule>
<rule id=”100051” level=”7” ignore=”900”>
<if_sid>100050</if_sid>
<match>nc -l</match>
<description>netcat listening for incoming connections.</description>
<group>process_monitor,</group>
</rule>
</group>
STEP IV
On the monitored Kali endpoint, I ran the attack emulation command nc -l 8000 for 30 seconds, which triggered Netcat process alert.
SECURITY ALERT/LOG ANALYSIS
Upon verification of SIEM (Wazuh) logs for Netcat activity, the following findings were made and information was gathered for investigation.
Date of incident: 11/03/2026
Time of incident: 18:58
Incident id: 1773251935.47555
Agent ip: 10.204.253.26
Rule id: 100051
With the above information, the intrusion can be traced and blocked to prevent potential data bridge and legal action can then be taken against the attacker if required.
RECOMMENDATIONS/MITIGATION STRATEGIES
Once the process is confirmed unauthorized/malicious, the affected system/workstation should be isolated from the entire network to prevent a spread to other systems. The intrusion process should be terminated immediately using EDR/command-line tools. It is also recommended that 10ALYTICS-DC update its detection rules (e.g adding new IoCs to the SIEM) to quickly detect similar intrusions in future.
