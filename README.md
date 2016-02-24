# P5---Linux-Server-Configuration
## Udacity Full Stack Webdeveloper Nanodegree


####by Federico Vasen

**Server IP**: 52.36.146.146
**URL of Catalog App**: http://ec2-52-36-146-146.us-west-2.compute.amazonaws.com/

*Connection*
Use SSH on port 2200.
Log in as user "grader" using key-based authentication.
RSA private key for user is provided in "notes to reviewer section"

Logged in as root using provided private key.

*Create a new user named grader*

`adduser grader`

Give the grader the permission to sudo
`cd /etc/sudoers.d`
`nano grader`

Generate key-pair with `ssh-keygen` for user grader and copy private key as `~/.ssh/grader.rsa` in local machine
Then on server shell copy public key in the user's .ssh directory
`mkdir .ssh`
`nano .ssh/authorized_keys`
`sudo ssh reload`

Disable key-based authentication in SSH config
`nano /etc/ssh/sshd_config`
`sudo service ssh reload`

Update all repository lists and then upgrade currently installed packages
`sudo apt-get update`
`sudo apt-get upgrade`


Change the SSH port from 22 to 2200
Edit `/etc/ssh/sshd_config` to change the SSH port.
Restart ssh service to check the connection is available in port 2200.


Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)

`sudo ufw default deny incoming`
`sudo ufw default allow outgoing`
`sudo ufw allow www`
`sudo ufw allow ntp`
`sudo ufw allow 2200`

Configure the local timezone to UTC
Zone was already configured to UTC by default
To check and confirm it I ran `sudo dpkg-reconfigure tzdata`

Install and configure Apache to serve a Python mod_wsgi application



Install and configure PostgreSQL:

    Do not allow remote connections
    Create a new user named catalog that has limited permissions to your catalog application database

Install git, clone and setup your Catalog App project (from your GitHub repository from earlier in the Nanodegree program) so that it functions correctly when visiting your server’s IP address in a browser. Remember to set this up appropriately so that your .git directory is not publicly accessible via a browser!

