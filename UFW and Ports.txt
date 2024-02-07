`ufw` (Uncomplicated Firewall) is a user-friendly interface for managing `iptables` firewall rules on Linux systems. It's designed to simplify the process of configuring a firewall, making it more accessible to less experienced users. Below are some basic and advanced `ufw` commands to help you manage your firewall settings effectively.

### Basic Commands

- **Enable UFW**: To activate the firewall, use:
  ```bash
  sudo ufw enable
  ```
- **Disable UFW**: To deactivate the firewall, use:
  ```bash
  sudo ufw disable
  ```
- **Check UFW Status**: To see the current status and rules, use:
  ```bash
  sudo ufw status
  ```
  For a more detailed view, add the `verbose` flag:
  ```bash
  sudo ufw status verbose
  ```

### Setting Default Policies

- **Deny Incoming Connections**: To set the default policy to deny all incoming connections, use:
  ```bash
  sudo ufw default deny incoming
  ```
- **Allow Outgoing Connections**: To set the default policy to allow all outgoing connections, use:
  ```bash
  sudo ufw default allow outgoing
  ```

### Allowing and Denying Services

- **Allow a Service by Name**: For well-known services, `ufw` can allow traffic based on the service name:
  ```bash
  sudo ufw allow ssh
  ```
- **Allow a Specific Port**: To allow traffic on a specific port (e.g., TCP port 22 for SSH), use:
  ```bash
  sudo ufw allow 22/tcp
  ```
- **Deny Traffic on a Port**: To deny traffic on a specific port, use:
  ```bash
  sudo ufw deny 23
  ```

### Advanced Rules

- **Allow Traffic by IP Address**: To allow traffic from a specific IP address, use:
  ```bash
  sudo ufw allow from 192.168.1.100
  ```
- **Allow Traffic on a Specific Port from a Specific IP Address**: To refine the rule to a specific port and IP, use:
  ```bash
  sudo ufw allow from 192.168.1.100 to any port 22
  ```
- **Deny Traffic to a Specific Network Interface**: To deny incoming traffic on a specific network interface (e.g., `eth0`), use:
  ```bash
  sudo ufw deny in on eth0 to any port 80
  ```

### Managing UFW Rules

- **Delete Rules**: To delete rules, you can specify them by their number (as displayed in `ufw status numbered`) or by the exact rule itself. For example:
  - By number:
    ```bash
    sudo ufw delete 1
    ```
  - By specifying the rule:
    ```bash
    sudo ufw delete allow 80/tcp
    ```
- **Reset UFW**: To reset all `ufw` settings to the defaults, clearing all rules, use:
  ```bash
  sudo ufw reset
  ```

### Logging

- **Enable Logging**: To enable `ufw` logging, use:
  ```bash
  sudo ufw logging on
  ```
- **Disable Logging**: To disable logging, use:
  ```bash
  sudo ufw logging off
  ```
- **View Logs**: `ufw` logs firewall activity to `/var/log/ufw.log`. You can view this file using tools like `cat`, `less`, or `grep`.

### Application Profiles

- **List Application Profiles**: `ufw` supports application profiles stored in `/etc/ufw/applications.d`. To list available profiles, use:
  ```bash
  sudo ufw app list
  ```
- **Allow/Deny Using Application Profiles**: You can allow or deny traffic based on application profiles, which is useful for services with multiple ports:
  ```bash
  sudo ufw allow 'Nginx Full'
  ```

### Tips

- Always check the status of `ufw` and review your rules after making changes to ensure they are configured as intended.
- Be cautious when configuring rules, especially on remote servers, as incorrect settings can lock you out of your server.
- Consider setting up a cron job or at task to disable `ufw` temporarily when making remote changes, to prevent being locked out due to a misconfiguration.




=============================================================================================================================================

								PORTS STUFF

=============================================================================================================================================

In Linux, as in other operating systems, certain port numbers are designated for well-known services by the Internet Assigned Numbers Authority (IANA). Disabling ports typically involves stopping the services that are listening on those ports or blocking them using a firewall. Here's a list of some commonly used ports and their associated services, along with general guidance on how to disable them:

### Popular Ports and Their Services

- **Port 22**: SSH (Secure Shell) - used for secure remote administration.
- **Port 25**: SMTP (Simple Mail Transfer Protocol) - used for email sending.
- **Port 53**: DNS (Domain Name System) - used for resolving domain names.
- **Port 80**: HTTP (Hypertext Transfer Protocol) - used for web traffic.
- **Port 110**: POP3 (Post Office Protocol version 3) - used for email receiving.
- **Port 143**: IMAP (Internet Message Access Protocol) - used for accessing email on a remote server.
- **Port 443**: HTTPS (HTTP Secure) - used for secure web traffic.
- **Port 3306**: MySQL - used for MySQL database connections.
- **Port 5432**: PostgreSQL - used for PostgreSQL database connections.

### Disabling Ports

Disabling ports can be done in two main ways: stopping the service that listens on the port, or blocking the port using a firewall. Here's how you can do both:

#### Stopping the Service

1. **Identify the service** using the port with a command like `sudo lsof -i :<port-number>` or `sudo netstat -tuln | grep <port-number>`.
   
2. **Stop the service** using the service management command appropriate for your Linux distribution. On systemd-based systems (like CentOS, Fedora, Ubuntu, and Debian), you can use:

   ```bash
   sudo systemctl stop <service-name>
   ```

   To prevent the service from starting on boot:

   ```bash
   sudo systemctl disable <service-name>
   ```

#### Blocking the Port Using a Firewall

1. **Using `iptables`**: A widely used method for older systems or those preferring direct firewall rule management. To block incoming traffic on a specific port (e.g., port 22):

   ```bash
   sudo iptables -A INPUT -p tcp --dport 22 -j DROP
   ```

2. **Using `firewalld`** (CentOS/RHEL 7 and above, Fedora, and some other distributions):

   To block a specific port:

   ```bash
   sudo firewall-cmd --permanent --add-rich-rule='rule family="ipv4" port protocol="tcp" port="22" reject'
   sudo firewall-cmd --reload
   ```

3. **Using `ufw`** (Uncomplicated Firewall, common in Ubuntu):

   To deny incoming traffic on a specific port:

   ```bash
   sudo ufw deny 22
   ```

### Note

- Before disabling any ports, ensure you understand the implications for your environment and the services you are running. Disabling ports without proper consideration can lead to loss of functionality and accessibility issues.
- Always verify the configuration after making changes to ensure the desired security posture is achieved without unintended side effects.


