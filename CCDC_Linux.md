# Change Password
`passwd [Username]`
**LOGOUT EVERYONE/REBOOT!!!**

# BACKUP FILES IMPORTANT TO SERVICES
- `/bin`, `/usr`, `/etc`
- (note: if you remove a bad cron job or line in passwd etc, update the backup so it does not have the same issue)
- Webserver `/var/www/html`, `/etc/apache`, `/etc/apache2`, `/usr/local/apache`, `/usr/local/apache2`

# Current Active Users
`who -u`

# File Access
- `/etc/passwd -rw-r--r-- root root`
- `/etc/sudoers -r--r----- root root`
- `/etc/sudeors.d -rwx-r-x--- root root`

# User Permissions
## Check for inccorect permissions in /etc/passwd
- `cat /etc/passwd | grep ':0:'`
- `services should have /sbin/nologin`

## Check Sudoers
- check who has `ALL=(ALL) ALL` in sudoers `sudo cat /etc/sudoers | grep 'ALL=(ALL).*ALL'`
- find included sudoers files: `sudo cat sudoers | grep 'includedir'`
- check additional sudoer.d files `sudo ls /etc/sudoers.d`

# Open Ports
- `sudo lsof -i -P -n | grep LISTEN`
- `sudo netstat -tulpn | grep LISTEN`
- `sudo ss -tulpn | grep LISTEN`

# Services
- Update Out of date/Vulnerable versions
- Look for Bad configs in httpd.conf or other files (eg. listening to random port)

# Cron Jobs
Check for jobs running with permissions
- These can really mess things up try to find as many as possible whe you have the time
