# Individual Reflection – SEC-02: Security Operations Center (SOC) Build-Out

## 1. Introduction

Working on the Security Operations Center (SOC) Build-Out project gave me practical exposure to security monitoring, log analysis, and basic threat detection. The main objective was to understand how a SOC collects security events from different systems, monitors suspicious activities, and helps security analysts investigate potential threats.

## 2. Work Completed

During this project, I configured Splunk Enterprise as the central SIEM platform on my Windows machine. I connected Windows and Ubuntu systems using Splunk Universal Forwarders to collect and forward their logs.

I also configured Sysmon on Windows to capture detailed system activity, including process creation and network connections. After collecting the logs in Splunk, I created searches and dashboards to monitor:

- Windows failed login attempts.
- Ubuntu authentication activity.
- PowerShell process activity.
- Administrator group changes.
- Network connection activity.

I performed controlled security tests, analyzed the generated events, and configured an alert for multiple failed Windows login attempts. I also documented the implementation process and collected screenshots as project evidence.

## 3. Skills and Knowledge Gained

This project helped me improve my understanding of:

- SIEM concepts and the role of Splunk in security monitoring.
- Windows event logs and Sysmon.
- Linux authentication and system logs.
- Log forwarding and centralized log collection.
- SPL queries for searching and analyzing security events.
- Security dashboards, alerts, and basic incident investigation.
- Mapping suspicious activities to relevant MITRE ATT&CK techniques.

## 4. Challenges Faced

One of the main challenges was configuring log collection from both Windows and Ubuntu systems. I had to troubleshoot forwarding configurations, verify that the correct events were reaching Splunk, and identify the appropriate fields for analysis.

Another challenge was creating useful searches and dashboards from the collected events. I learned that successful security monitoring requires not only collecting logs but also understanding what the events represent and identifying patterns that may indicate suspicious activity.

## 5. Limitations and Future Improvements

Due to the available hardware and project time, I implemented a focused SOC environment using Splunk, Sysmon, Windows, and Ubuntu.

The project did not include full integration of Suricata, TheHive, Cortex, Shuffle SOAR, or Atomic Red Team. These components would be valuable additions in a more advanced SOC environment.

In the future, I would like to expand the lab by integrating network intrusion detection, automated incident response, case management, and additional attack simulations.

## 6. Conclusion

Overall, this project was a valuable hands-on learning experience. It helped me understand how security monitoring works in a practical environment and how a SIEM platform can be used to collect, investigate, and visualize security events.

I gained practical experience with Splunk, Sysmon, Windows and Linux log analysis, and basic detection engineering. This project has strengthened my interest in cybersecurity, particularly in Security Operations Center (SOC) analysis and Blue Team operations.
