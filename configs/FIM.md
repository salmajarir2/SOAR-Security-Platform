Configuring and testing FIM on an Ubuntu machine


FIM is a technology that monitors the integrity of system and application files. It safeguards sensitive
data, application, and device files by routinely monitoring, scanning, and confirming their integrity. It
works by detecting changes to mission-critical files in the network and as a result, it brings down the
risk associated with data breaches.
The good news is that Wazuh has a built-in capability for FIM. This is possible because Wazuh uses
an Open Source HIDS Security (OSSEC) agent. OSSEC is a free, open-source host-based intrusion
detection system. When a user or process creates, modifies, or deletes a monitored file, the Wazuh
FIM module initiates an alert. Let’s understand a file integrity check by setting up a FIM module on
an Ubuntu machine. In order to test this use case, you need to follow these steps.
Requirements
To test the FIM use case, we would require the following:
The Wazuh manager
An Ubuntu machine (with the Wazuh agent installed)
Step 1 – Setting up the Wazuh agent on an Ubuntu machine
By default, the FIM module is enabled on the Wazuh agent. The configuration of the FIM module is
present in the <syscheck> tag under the ossec.conf file located at /var/ossec/etc. We only need to
add directories (to be monitored) under the <syscheck> block. The following configuration will
monitor specified files and directories for any types of changes or modifications:
<syscheck>
<disabled>no</disabled>
<frequency>720</frequency>
<scan_on_start>yes</scan_on_start>
<directories check_all="yes" report_changes="yes"
real_time="yes">/etc,/bin,/sbin</directories>
<directories check_all="yes" report_changes="yes"
real_time="yes">/lib,/lib64,/usr/lib,/usr/lib64</directories>
<directories check_all="yes" report_changes="yes"
real_time="yes">/var/www,/var/log,/var/named</directories>
<ignore>/etc/mtab</ignore>
<ignore>/etc/hosts.deny</ignore>
<ignore>/etc/mail/statistics</ignore>
<ignore>/etc/random-seed</ignore>
<ignore>/etc/adjtime</ignore>
<ignore>/etc/httpd/logs</ignore>
<ignore>/etc/utmpx</ignore>
<ignore>/etc/wtmpx</ignore>
<ignore>/etc/cups/certs</ignore>
<ignore>/etc/dumpdates</ignore>
<ignore>/etc/svc/volatile</ignore>
<ignore>/sys/kernel/security</ignore>
<ignore>/sys/kernel/debug</ignore>
<ignore>/sys</ignore>
<ignore>/dev</ignore>
<ignore>/tmp</ignore>
<ignore>/proc</ignore>
<ignore>/var/run</ignore>
<ignore>/var/lock</ignore>
<ignore>/var/run/utmp</ignore>
</syscheck>
Let’s break down the preceding configuration:
The <disabled> tag is set to no to enable the syscheck module on Wazuh.
The <scan_on_start> tag is set to yes to conduct the initial scan when the Wazuh agent shows up.
The <frequency> tag is set to 720 to conduct a file monitoring scan every 720 minutes.
The <directories> tags talk about all the directories to monitor. In this example, we’re monitoring important system
directories such as /etc, /bin, /sbin, /lib, /lib64, /usr/lib, /usr/lib64, /var/www, /var/log, and
/var/named.
The <ignore> tags indicate files or directories to ignore during the monitoring process. These are common system files that are
not generally important for FIM analysis.
Step 2 – Restart the Wazuh agent
For the configuration changes to take effect, we need to restart the wazuh-agent service as shown in
the following:
sudo systemctl restart wazuh-agent
Step 3 – Visualizing the alerts
To visualize the alerts, you can navigate to Security Alerts or the Integrity Monitoring module in
the Wazuh dashboard
