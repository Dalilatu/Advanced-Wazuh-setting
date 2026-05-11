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

 Make sure to pay attention to the identations.<br/>

**Note** that if you disabled Windows Defender in prevous labs, you will need to enable it in order for the logs to be forwarded to Wazuh. <br/>
<br/>
Restart Wazuh service using PowerShell.<br/>
`NET STOP WazuhSvc`<br/>
`NET START WazuhSvc`<br/>
<br/>

**Step 2:** Enable Windows Defender using **gpedit**. <br/>
 Follow the following steps ; **Local Computer Policy > Administrative Templates > Windows Components > Microsoft Defender Antivirus > Turn Off microsoft defenders antivirus** <br/>

 <p align="center">
  Local Group Editor:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/f0fd7471-76d1-443a-a83d-1c537ec2e5e9" height="80%" width="80%"/>
<br/>
<br/>

 <p align="center">
  Disable Microsoft Defender Antivirus:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/60433fe0-82b8-4593-b09e-314f9de44d86" height="80%" width="80%"/>
<br/>
<br/>


<p align="center">
  Turn off real-time protection:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/fcd43ff2-7983-4352-9f76-5612d84f3b84" height="80%" width="80%"/>
<br/>
<br/>

<p align="center">
  Disable real-time protection:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/d4b3d6df-cac5-4027-85f8-e6b302175790" height="80%" width="80%"/>
<br/>
<br/>






## Testing both Windows Defender and PowerShell Integrations

To test the Windows Defender integration we will download another eicar file using the PowerShell script below.<br/>

<!-- 
   Invoke-WebRequest -Uri https://wildfire.paloaltonetworks.com/publicapi/test/pe  -OutFile C:/Users/m122/Downloads/pe.exe                            -->



<p align="center">
  Wazuh platform showing malware detection using Microsoft Antivirus Detection:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/9717ed5d-1b33-496f-8a02-96f143a1e384" height="80%" width="80%"/>
<br/>
<br/>


## Troubleshooting Logging

If events are not showing up in the Discovery feature of Wazuh, you can check the archives log with the following command. We will grep for pe.exe, the name of the EICAR file we executed earlier.<br/>

`sudo cat /var/ossec/logs/archives/archives.json | grep -i pe.exe`<br/>


<p align="center">
  Wazuh server logs showing multiple hits.:<br/>
<img alt="image" src="https://github.com/user-attachments/assets/dabae370-6d43-4d8f-9073-7435f108ad7e" height="80%" width="80%"/>
<br/>
<br/>

Another way to troubleshoot is to go back to the VM and look at the logging with **Event Viewer**.

## Conclusion
Integrating both VirusTotal and Windows Defender makes it possible to monitor malware and suspicious activity across multiple endpoints through a centralized view in the Wazuh Security Events Dashboard. Additionally, enabling log archiving for all ingested events supports incident investigations and enhances threat hunting capabilities.





