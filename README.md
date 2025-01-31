# CCDC Blueteam Manual

## Pre-Competition Methodology

The events that you will encounter in CCDC will be correlated to real paths attackers will take, but the competition is a bit gamified in order to present a challenge to both the blue team and red team. Many of the things you learn to defend systems in this competition will directly translate to real-world applicable cybersecurity skills.

### Stop Chasing Ghosts!

Many people will get caught up with the idea that a red team could be in their systems and will panic at everything they see that they do not recognize. Often the unknown files, processes, or user accounts are standard Linux or Windows features. Chasing these leads that turn out to be nothing malicious will only eat up time and will allow the red team to gain an advantage. It would be wise to get a list of the common files, processes, and user accounts for the operating systems that you will be defending to minimize your time chasing ghosts.

## Initial Cleaning

---

As soon as you gain access to your new machines during the competition, every single person needs to have a solid plan of action. Often teams will have a plan for the first 15 minutes, then it falls apart when people get stuck or notice suspected red team activity. This is the responsibility of the team leader to ensure everyone is working together and as a single unit. **Your team will either succeed together or fail together.** Here are some common things to address immediately after gaining access to your systems.

### Linux

**Commands listed are not all-encompassing and are for a baseline only!**

1. Change **all** user passwords. *Yes, especially the credentials given to you!*

    `# passwd username`

2. Audit /etc/shadow for users with passwords set, or no password at all.

    `cat /etc/shadow`

3. Remove all SSH keys present on the box.

    `# find / -name authorized_keys 2> /dev/null`

    `# find / -name id_rsa 2> /dev/null`
4. Audit sudo access given to users.

    `# cat /etc/sudoers`

    `# cat /etc/sudoers.d/*`

    `# getent group sudo | cut -d: -f4` (The *sudo* group is Debian, *wheel* is for RHEL)
5. Audit /etc/passwd to check for account shells and UIDs.

    `$ cat /etc/passwd | grep :0:`

    `$ cat /etc/passwd | grep -v /bin/false | grep -v /sbin/nologin`
6. Verify that there are not any non-standard cron jobs on the system.

    `# cat /etc/cron.d/*`

    `# for user in $(cut -f1 -d: /etc/passwd); do crontab -u $user -l; done`
7. Remove packages that could be used for malicious purposes if not needed.

    `# apt remove socat nc ncat nmap netcat` (*apt* is for Debian, *yum* is for RHEL). Other packages must be manually inspected in order to prevent taking down critical services.
8. Stop services that are not critical to the system or competition needs.

    `# systemctl --type=service --state=active`

    `# systemctl stop servicename`
8. Identify SUID and SGID files. Cross-reference with https://gtfobins.github.io/ to narrow down malicous instances of SUID and SGID files.

    `# find / -perm -4000 -print 2>/dev/null` for SUID

    `# find / -perm -2000 -print 2>/dev/null` for SGID

10. Identify world-writable files and directories.

    `# find / -type d \( -perm -g+w -or -perm -o+w \) -exec ls -adl {} \;` for directories

    `# find / ! -path "*/proc/*" -perm -2 -type f -print 2>/dev/null` for files

11. Check who is currently logged into the machine.
    
    `$ who`

#### Additional Resources

- [Linux Privilege Escalation Guide](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md)
- [Linux Persistence Guide](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Persistence.md)

#### Useful Tools

- [LinEnum](https://github.com/rebootuser/LinEnum)
- [pspy](https://github.com/DominicBreuker/pspy)

### Windows


1. Change **all** user passwords. *Yes, especially the credentials given to you!*

    `> net user <username> <password>` NOTE: If domain user, append `/domain`

2. Audit important groups.

    `> net localgroup Administrators`

    `> net localgroup "Remote Desktop Users"`

    `> net localgroup "Remote Management Users"`

3. Disable WinRM if not needed.
    
    `PS> Disable-PSRemoting -Force`

4. Check Windows Defender registry keys

    `> regedit.exe`  HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows Defender

5. Check for tasks set to run through the registry.

    - HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
    - HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce
    - HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunServices
    - HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunServicesOnce

6. Check system and user startup folder.
    
    - User: C:\Users\USERNAME\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\
    - System: C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp\

7. Audit scheduled tasks.

    `> schtasks`

8. Check PowerShell Execution policy.

    `PS> Get-ExecutionPolicy`

    `PS> Set-ExecutionPolicy -ExecutionPolicy Restricted -Scope LocalMachine`

9. Check Windows Defender status.

    `PS> Get-MPComputerStatus`

10. Audit SMB shares.

    `> net view \\127.0.0.1`

11. Disable Guest account.

    `> net user guest /active no`

#### Additional Resources

- [Windows Privilege Escalation Guide](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md)
- [Windows Persistence Guide](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Persistence.md)

#### Useful Tools

- [PowerUp](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1)

## Monitoring Activity

---

### Linux

1. Audit listening ports and established connections

    `# lsof -i -P -n`

2. Audit running processes
    
    `# ps aux`

3. Monitor logs in `/var/log/`

### Windows

1.  Audit listening ports

    `> netstat –na`

2. List the SMB sessions this machine has opened with
other systems

    `> net use`

3. List the open SMB sessions with this machine
   
   `> net session`

4. Audit running processes

    `> tasklist`

