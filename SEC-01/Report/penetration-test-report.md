# SEC-01: Enterprise Red Team Simulation and Penetration Test

## 1. Executive Summary

A penetration test was conducted against an intentionally vulnerable Metasploitable 2 system in an isolated VirtualBox laboratory network.

The assessment identified critical security weaknesses, including an unauthenticated root shell and remote command execution through a vulnerable UnrealIRCd backdoor. Anonymous access to SMB and FTP services, exposed server configuration information, and web directory listings were also observed.

The findings demonstrate the risks of insecure services, outdated software, unnecessary service exposure, and inadequate access controls.

## 2. Assessment Details

- Project: SEC-01 Enterprise Red Team Simulation and Pentest Engagement
- Assessment date: 18 November 2026
- Tester: Harsha Vardhan
- Attacker machine: Kali Linux
- Target machine: Metasploitable 2
- Target IP: 192.168.77.20
- Lab platform: Oracle VirtualBox
- Network: Isolated Internal Network (redteam-lab)
- Assessment type: Vulnerability assessment and controlled penetration testing

## 3. Scope

The assessment was limited to the intentionally vulnerable target at:

192.168.77.20

Testing was performed inside an isolated lab network. No external systems were targeted.

## 4. Methodology

The following phases were performed:

1. Lab setup and connectivity verification.
2. Network reconnaissance and port scanning using Nmap.
3. Service version detection and enumeration.
4. Identification of exposed and insecure services.
5. Validation of anonymous FTP and SMB access.
6. Validation of the unauthenticated root shell.
7. Controlled validation of the UnrealIRCd backdoor.
8. Web server and application enumeration.
9. Evidence collection and documentation.
10. Risk assessment and remediation recommendations.

## 5. Findings Summary

| ID | Finding | Severity |
|---|---|---|
| SEC01-01 | Unauthenticated root shell on TCP 1524 | Critical |
| SEC01-02 | UnrealIRCd backdoor remote command execution (CVE-2010-2075) | Critical |
| SEC01-03 | Anonymous SMB share access | Medium-High |
| SEC01-04 | Anonymous FTP authentication | Low-Medium |
| SEC01-05 | Apache directory listing enabled | Low-Medium |
| SEC01-06 | PHP configuration information disclosure | Low-Medium |
| SEC01-07 | Exposed phpMyAdmin interface | Informational-Low |
| SEC01-08 | TikiWiki misconfiguration | Informational |

Severity ratings are qualitative assessment ratings. Formal CVSS scoring was not performed.

---

## 6. Detailed Findings

### SEC01-01: Unauthenticated Root Shell

**Severity:** Critical  
**Target:** 192.168.77.20  
**Port:** 1524/TCP  
**Service:** Bind shell

#### Description
A remote shell service was accessible without requiring authentication.

#### Evidence
A connection from Kali Linux to TCP port 1524 provided a shell with root privileges.

Verification commands:

- whoami
- id

Observed output:

- whoami: root
- id: uid=0(root) gid=0(root) groups=0(root)

#### Impact
An unauthenticated attacker with network access to the service could execute commands with full root privileges, potentially compromising the entire system.

#### Recommendation
Disable the exposed bind shell, remove unnecessary services, restrict network access, investigate unauthorized service installations, and rebuild the host from a trusted image if compromise is suspected.

#### Evidence Files
- evidence/root-shell-proof.txt
- Initial scan: evidence/intial-scan.txt

---

### SEC01-02: UnrealIRCd Backdoor Remote Command Execution

**Severity:** Critical  
**Target:** 192.168.77.20  
**Port:** 6667/TCP  
**Service:** UnrealIRCd 3.2.8.1  
**CVE:** CVE-2010-2075

#### Description
The target was running a vulnerable UnrealIRCd version containing a known backdoor that permits remote command execution.

#### Validation
The vulnerability was checked using the appropriate Metasploit module. The target was reported vulnerable.

A controlled command was executed through the backdoor, and the resulting output was verified on the target.

The command output confirmed:

uid=0(root) gid=0(root)

#### Impact
An unauthenticated remote attacker could execute arbitrary commands as root, resulting in complete system compromise.

#### Recommendation
Remove the vulnerable UnrealIRCd installation, upgrade to a trusted supported version if the service is required, restrict IRC access, and investigate the system for unauthorized activity.

#### Evidence Files
- evidence/unrealircd-exploitation.txt
- evidence/secondary-services.txt

---

### SEC01-03: Anonymous SMB Share Access

**Severity:** Medium-High  
**Target:** 192.168.77.20  
**Ports:** 139/TCP and 445/TCP  
**Service:** Samba

#### Description
Anonymous access to the SMB share named tmp was possible without providing credentials.

#### Evidence
An SMB connection was established using anonymous access. Directory contents were successfully listed.

#### Impact
Anonymous users may be able to access exposed files and potentially sensitive information. Nmap also reported READ/WRITE permissions during enumeration; write permission was not independently verified through a manual write test.

#### Recommendation
Disable anonymous access, review share permissions, restrict SMB to trusted systems, and upgrade unsupported Samba versions.

#### Evidence Files
- evidence/smb-anonymous-proof.txt
- evidence/detailed-enumeration.txt

---

### SEC01-04: Anonymous FTP Login

**Severity:** Low-Medium  
**Target:** 192.168.77.20  
**Port:** 21/TCP  
**Service:** vsFTPd 2.3.4

#### Description
The FTP server accepted an anonymous username without requiring a valid user password.

#### Evidence
The server returned:

230 Login successful.

Anonymous login was verified manually. Directory listing was attempted, but no files were captured during the final verification.

#### Impact
Anonymous FTP access may permit unauthorized users to access files made available through the service, depending on the server configuration and permissions.

#### Recommendation
Disable anonymous FTP unless explicitly required. Use secure file-transfer protocols, restrict access, and upgrade the service.

#### Evidence Files
- evidence/ftp-anonymous-proof.txt
- evidence/detailed-enumeration.txt

---

### SEC01-05: Apache Directory Listing Enabled

**Severity:** Low-Medium  
**Target:** 192.168.77.20  
**Port:** 80/TCP  
**Affected paths:** /doc/ and /icons/

#### Description
Apache directory indexing was enabled on the identified paths.

#### Evidence
HTTP responses contained directory index pages with file and directory links.

#### Impact
Directory listings expose filenames and directory structure, potentially assisting attackers in discovering additional resources.

#### Recommendation
Disable directory indexing using Apache Options -Indexes and restrict access to sensitive directories.

#### Evidence Files
- evidence/directory-listing-finding.txt
- evidence/doc-listing.html
- evidence/icons-listing.html

---

### SEC01-06: PHP Information Disclosure

**Severity:** Low-Medium  
**Target:** 192.168.77.20  
**Port:** 80/TCP  
**Affected path:** /phpinfo.php

#### Description
The PHP information page was publicly accessible and disclosed system and PHP configuration details.

#### Information Observed
- PHP version: 5.2.4-2ubuntu5.10
- Operating system: Linux
- Kernel: 2.6.24-16-server
- Server API: CGI/FastCGI
- PHP configuration path: /etc/php5/cgi/php.ini
- Document root: /var/www/
- display_errors: On
- allow_url_fopen: On

#### Impact
Detailed system information assists attackers in identifying outdated software and selecting targeted attacks.

#### Recommendation
Remove publicly accessible phpinfo.php files or restrict access to authorized administrators. Upgrade unsupported PHP versions and securely configure PHP.

#### Evidence Files
- evidence/phpinfo-finding.txt
- evidence/web-path-check.txt

---

### SEC01-07: Exposed phpMyAdmin Interface

**Severity:** Informational-Low  
**Target:** 192.168.77.20  
**Port:** 80/TCP  
**Affected path:** /phpMyAdmin/

#### Description
The phpMyAdmin database administration interface was accessible over HTTP and displayed a username/password login form.

#### Evidence
The page returned HTTP 200 and presented a login form.

#### Impact
Exposing a database administration interface increases the attack surface. Risk would be higher if weak credentials, vulnerable software, or authentication weaknesses were present.

These additional weaknesses were not verified during this assessment.

#### Recommendation
Restrict the interface to authorized administrators or trusted IP addresses, maintain supported software versions, and enforce strong authentication.

#### Evidence Files
- evidence/phpmyadmin-finding.txt
- evidence/web-path-check.txt

---

### SEC01-08: TikiWiki Application Misconfiguration

**Severity:** Informational  
**Target:** 192.168.77.20  
**Port:** 80/TCP  
**Affected path:** /tikiwiki/

#### Description
The accessible TikiWiki application path displayed an error indicating that the application was not properly set up.

#### Evidence
The application returned:

TikiWiki is not properly set up.

#### Impact
The exposed misconfigured application may reveal deployment information and increases the web server attack surface.

No exploitable TikiWiki vulnerability was confirmed.

#### Recommendation
Remove unused applications, complete secure configuration if the application is required, and restrict access to unnecessary application paths.

#### Evidence Files
- evidence/tikiwiki-finding.txt
- evidence/web-path-check.txt

---

## 7. Additional Service Exposure

The initial and secondary scans identified several additional services and outdated software that require further security review.

Observed services included:

- Telnet
- SSH
- SMTP
- DNS
- RPC
- Samba
- RSH, Rlogin and Rexec
- Java RMI
- NFS
- ProFTPD
- MySQL
- PostgreSQL
- VNC
- UnrealIRCd
- Apache
- Tomcat

The presence of these services does not automatically mean each was exploited or confirmed vulnerable. They should be reviewed, updated, or disabled if unnecessary.

## 8. MITRE ATT&CK Mapping

The following table maps selected activities observed during the assessment to relevant MITRE ATT&CK techniques.

| Observed Activity | MITRE ATT&CK Technique | Technique ID |
|---|---|---|
| Network reconnaissance and service scanning using Nmap | Network Service Discovery | T1046 |
| SMB share enumeration | Network Share Discovery | T1135 |
| Controlled exploitation of the vulnerable UnrealIRCd remote service | Exploitation of Remote Services | T1210 |
| Examination of publicly exposed PHP and server configuration information | System Information Discovery | T1082 |

**Mapping limitations:**

- These mappings describe the observed assessment activities and relevant techniques.
- The assessment did not include a Windows Active Directory environment.
- BloodHound-based Active Directory attack-path analysis was not performed.
- Active Directory lateral movement techniques were not tested.
- No claim is made that untested MITRE ATT&CK techniques were successfully demonstrated.
## 9. Assessment Limitations

- Testing was conducted against Metasploitable 2, an intentionally vulnerable Linux system.
- A Windows Active Directory environment was not established.
- BloodHound-based Active Directory attack-path analysis was not completed.
- The assessment was focused on the available isolated Linux lab.
- Screenshots are pending and will be added to the evidence/screenshots directory.
- Formal CVSS scores were not calculated.
- Not every discovered service was subjected to exploitation or exhaustive testing.
- No production systems were assessed.

## 10. Remediation Priority

### Immediate
1. Disable unauthenticated root-shell services.
2. Remove or patch vulnerable UnrealIRCd.
3. Review the host for unauthorized services and possible compromise.

### High Priority
4. Disable anonymous SMB access and review share permissions.
5. Disable anonymous FTP if not required.
6. Remove outdated and unsupported network services.

### Medium Priority
7. Disable Apache directory indexing.
8. Remove or restrict phpinfo.php.
9. Restrict phpMyAdmin to trusted administrators.
10. Remove or securely configure unused web applications.

### Ongoing
11. Maintain an inventory of services.
12. Regularly update software and operating systems.
13. Monitor authentication and network activity.
14. Perform periodic vulnerability assessments.

## 11. Conclusion

The assessment identified multiple serious weaknesses in the target system. The unauthenticated root shell and UnrealIRCd backdoor were successfully validated and demonstrated the possibility of full system compromise.

Anonymous access to SMB and FTP, web configuration disclosure, and directory indexing further increased the attack surface.

The assessment demonstrates the importance of disabling unnecessary services, maintaining supported software, enforcing authentication and access controls, and conducting regular security testing.

## 12. Evidence Directory

Evidence collected during testing is stored in the evidence directory.

Key files include:

- intial-scan.txt
- detailed-enumeration.txt
- secondary-services.txt
- web-enumeration.txt
- web-path-check.txt
- root-shell-proof.txt
- unrealircd-exploitation.txt
- smb-anonymous-proof.txt
- ftp-anonymous-proof.txt
- directory-listing-finding.txt
- doc-listing.html
- icons-listing.html
- phpinfo-finding.txt
- phpmyadmin-finding.txt
- tikiwiki-finding.txt

Screenshots are stored separately in the Windows folder SEC-01-Screenshots and are included with the submission.

---

**End of Report**


