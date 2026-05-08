# Advanced-Wazuh-setting

## Logging vs Detection (Alerts)
By default, Wazuh logs events that trigger alerts based on configured rules. This approach is effective if we're solely focused on the alerts. However, if we aim to utilize Wazuh as a comprehensive SIEM for investigations and threat hunting, we need it to ingest all logs, not just those prompting alerts. To achieve this, we can configure Wazuh to log everything by following these steps<br/>
<br/>
<b>Step 1:</b> On the Wazuh manager, use the text editor nano to edit the configuration` file. <br/>

`sudo nano /var/ossec/etc/ossec.conf`

<br/>

<b>Step 2:</b> Change "logall" from `no` to `yes`.<br/>

 <p align="center">
   Change this:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/fa3b1610-ee6a-4471-8ac9-0769fc746f15" height="80%" width="80%"/>
<br />
<br />

<p align="center">
  To this:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/45726499-8d05-43cc-87d8-9a9530ef4307" height="80%" width="80%"/>
<br />
<br />

<b>Step 3:</b> Restart Wazuh Manager<br/>

`sudo systemctl restart wazuh-manager.service`

<br/>

This configuration will cause Wazuh to archive all logs that it has been configured to ingest. However, the process is not complete yet. We need to edit the filebeat.yml file using nano.<br/>

<b>Step 4:</b> Edit the `filebeat.yml` file.<br/>
`sudo nano /etc/filebeat/filebeat.yml` <br/>
Scroll down to find the filebeat.modules wazuh archives enabled:false. Change the archives enabled to true. <br/>

<p align="center">
 Change "archive" to "true":<br/>
<img alt="image" src="https://github.com/user-attachments/assets/aab023de-7ca2-40f1-b438-56c6714918c0" height="80%" width="80%"/>
<br />
<br />

 Restart the filebeat system using : `sudo systemctl restart filebeat`
<br/>
<br/>

<b>Step 5:</b> Go back to the Wazuh dashboard.<br/>

<p align="center">
 Select the hamburger menu in the upper right and scoll down to Management —> Stack Management:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/ddac6da9-b902-42bd-946f-e0f254e945f1" height="80%" width="80%"/>
<br/>
<br/>

<p align="center">
 Select “Index Patterns” and then select “Create Index Pattern”:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/4a360af7-efd0-44bd-bfc9-f6913ffdaed0" height="80%" width="80%"/>
<br/>
<br/>

<p align="center">
 Type in wazuh-archives-*, you should see the wazuh-archive. Then select “Next Step”:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/7e446391-cad6-4022-b63b-4bc0eda8e1b0" height="80%" width="80%"/>
<br/>
<br/>

<p align="center">
 Select “timestamp” from the drop down and then select “Create Index Pattern”:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/8131dd37-1f02-436f-b4ca-6e7c6344c316" height="80%" width="80%"/>
<br/>
<br/>

 Restart wazuh manager via command line:<br/>

`sudo systemctl restart wazuh-manager.service`

<br/>

<p align="center">
 Go back to the hamburger menu and select Discover:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/cfc8127f-8746-453c-b9ae-042da33d936f" height="80%" width="80%"/>
<br/>
<br/>


<p align="center">
 Select wazuh-archives:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/81004462-f123-49b5-90e8-e3900eb8b4ba" height="80%" width="80%"/>
<br/>
<br/>

## Integrating Windows Defender and PowerShell Logs

### **Windows Defender logs**

Windows Defender stands out as the predominant free antivirus solution for PC users, commanding approximately 40% of the market share among free antivirus software options. 

Windows Defender logs can play a crucial role in aiding SOC analysts to grasp the security posture of endpoints, discern potential cyber threats, and conduct thorough investigations into security incidents. These logs contain various types of data including scan activities, threat detections, updates, quarantine actions, remediation efforts, firewall and network activities, as well as real-time protection events.

By default, Wazuh does not read the Windows Defender logs; however, we can configure Wazuh to integrate Windows Defender logs.  

### PowerShell Logs

**Windows PowerShell Log**: This log captures general operational information about PowerShell, including startup, shutdown, and errors encountered during execution. You can find this log under "Applications and Services Logs" -> "Microsoft" -> "Windows" -> "PowerShell".<br/>
<br/>

**Windows PowerShell Operational Log**: This log provides detailed information about PowerShell script execution, module loading, and other operational events. It's particularly useful for auditing purposes as it logs detailed information about script blocks executed within PowerShell. You can find this log under "Applications and Services Logs" -> "Microsoft" -> "Windows" -> "PowerShell" -> "Operational".<br/>
<br/>

- **Script Block Logging**: PowerShell can be configured to log script block execution, capturing the contents of scripts as they're executed. This is a powerful auditing tool for tracking exactly what commands were run on a system.<br/>
- **Module Logging**: PowerShell logs information about the loading and unloading of modules. This can help administrators understand which modules are being used and when they are being accessed.<br/>
- **Transcription Logging**: PowerShell can be configured to log the input and output of PowerShell sessions, effectively recording interactive PowerShell sessions. This is particularly useful for compliance and security purposes.<br/>
<br/>

## Adding logging to the ossec configuration file. 

<b>Step 1: On the Windows VM:<b/> Use a text editor (notepad++ or notepad) as an Administrator and edit the `C:\\Program Files (x86)\\ossec-agent\\ossec.conf` file and add the following entries:<br/>

<p align="center">
 copy the following int the ossec.conf file:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/3679be6e-7705-4db5-a5d7-a9cdab7488d4" height="80%" width="80%"/>
<br/>
<br/>


<p align="center">
 screenshot of configuration:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/cd4940ed-dfac-4858-a29a-1caf72116c2f" height="80%" width="80%"/>
<br/>
<br/>








