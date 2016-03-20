# Linux Server Configuration

## Description

This project includes configuring a baseline installation of a Linux server to host web applications.  Configuration will include:

- Installing package updates
- Securing the server from a number of attack vectors
- Installing/configuring web and database servers

At the end of the project the web application created for Project 3: Item Catalog will be accessible from this server.

###  IP Address and SSH port

  IP Address: 52.32.100.134
  SSH Port: 2200

###  Application URL

  [http://ec2-52-32-100-134.us-west-2.compute.amazonaws.com](http://ec2-52-32-100-134.us-west-2.compute.amazonaws.com)

### Software Installed and Configuration Changes

#### Software Installed:

  1.  Latest versions of all packages (sudo apt-get upgrade)
  2.  Apache HTTP Server (package: apache2)
  3.  Python WSGI adapter for Apache  (package: libapache2-mod-wsgi)
  4.  Sqlite3 (package: sqlite3)  (Database For Catalog Application)
  5.  Git (package: git)
  6.  PostgreSQL (package: postgresql)
  7.  Python Pip (package: python-pip)
  8.  VirtualEnv (package: virtualenv  command: sudo pip install virtualenv)
  9.  SQL Alchemy (package: python-sqlalchemy)
  10. Oauth2 Python Client Library (package: python-oauth2client)
  
#### Configuration Changes:
    
  1. Create user 'grader' and grant sudo access

    - User 'grader' created using 'adduser' command
    - Created .ssh/authorized_keys file to enable Key-based authentication
    - Copied /root/.ssh/authorized key file contents so RSA key will work for 'grader' user
    
    - File 'grader' created in /etc/sudoers.d for sudo access  
    - Entered 'grader ALL=(ALL) NOPASSWD:ALL' in file to enable sudo access

  2. Update all currently installed packages

    - From user 'grader' ran command 'sudo apt-get update' to identify packages available for upgrade
    - Next ran command 'sudo apt-get-upgrade' to install newest versions of all packages

  3.  Disable remote login for root user

    - In /etc/ssh/sshd_config changed 'PermitRootLogin without-password' to 'PermitRootLogin no'
    - Restart sshd  

  4. Change SSH port from 22 to 2200
  
    - In /etc/ssh/sshd_config changed 'Port 22' to Port '2200'

  5.  Setup Uncomplicated Firewall (UFW) incoming for SSH, HTTP and NTP
    
    Following commands were executed:
    
      - sudo ufw default deny incoming
      - sudo ufw default allow outgoing
      - sudo ufw allow 2200/tcp     (For SSH)
      - sudo ufw allow www          (For HTTP)
      - sudo ufw allow ntp
      - sudo ufw enable

  6.  Install and configure PostgreSQL
  
    - Remote connections are not allowed by default
    - Created new user 'catalog' with 'createuser' command and provided limited access
    - Limited access for 'catalog' user was created by the following:
        postgres@ip-10-20-24-232:~$ createuser --interactive catalog
        Shall the new role be a superuser? (y/n) n
        Shall the new role be allowed to create databases? (y/n) n
        Shall the new role be allowed to create more new roles? (y/n) n

  7. Setting up Catalog App Project
    
    Commands to enable Flask applications
    - cd /var/www
    - sudo mkdir catalog
    
    Installing Flask
      
      - sudo apt-get install python-pip
      - sudo pip install virtualenv
      
    Setup Virtualenv
      - sudo virtualenv test  (or catalog)
      - source test/bin/activate
    
    Install Flask
      - sudo pip install Flask

    Configure the Virtual Host
        - created /etc/apache2/sites-available/catalog.conf file
        
    Create WSGI File to serve the application
        - create /var/www/catalog/catalog.wsgi
        
  8. Setup Automatic Package Updates

    - ran 'sudo apt-get update'
    - ran 'sudo apt-get install unattended-upgrades' (package was already installed)
    - in file '/etc/apt/apt.conf.d/50unattended-upgrades' uncommented line '"${distro_id}:${distro_codename}-updates";
    - in file '/etc/apt/apt.conf.d/10periodic'
        - changed Download-Upgradeable-Packages to "1"
        - added line 'APT::Periodic::Unattended-Upgrade "1";'
        
        
### Third-party resources used

  - Disable Remote Login for root [here](http://askubuntu.com/questions/27559/how-do-i-disable-remote-ssh-login-as-root-from-a-server)
  - Change SSH port from 22 to 2200 [here](https://help.ubuntu.com/community/SSH/OpenSSH/InstallingConfiguringTesting)
  - Install PostgreSQL and confirm no remote connections [here](https://www.digitalocean.com/community/tutorials/how-to-secure-postgresql-on-an-ubuntu-vps)
  - Setup Flask and serve Catalog App [here](https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps)
  - Automatic Package Updates [here](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)

