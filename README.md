# Advanced-Wazuh-setting

## Logging vs Detection (Alerts)
By default, Wazuh logs events that trigger alerts based on configured rules. This approach is effective if we're solely focused on the alerts. However, if we aim to utilize Wazuh as a comprehensive SIEM for investigations and threat hunting, we need it to ingest all logs, not just those prompting alerts. To achieve this, we can configure Wazuh to log everything by following these steps<br/>
<br/>
<b>Step 1:</b> On the Wazuh manager, use the text editor nano to edit the configuration` file. <br/>
<br/>

`sudo nano /var/ossec/etc/ossec.conf`

<br/>
<br/>

<b>Step 2:</b> Change "logall" from `no` to `yes`.<br/>

 <p align="center">
   Change this:<br/>
IMAGE height="80%" width="80%"/>
<br />
<br />

<p align="center">
  To this:<br/>
IMAGE height="80%" width="80%"/>
<br />
<br />

<b>Step 3:</b> Restart Wazuh Manager<br/>
<br/>

`sudo systemctl restart wazuh-manager.service`

<br/>
<br/>

This configuration will cause Wazuh to archive all logs that it has been configured to ingest. However, the process is not complete yet. We need to edit the filebeat.yml file using nano.<br/>
