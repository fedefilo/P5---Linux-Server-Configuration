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

Source: Udacity Linux Web Servers Course

*Create a new user named grader*

`adduser grader`

*Give the grader the permission to sudo*
`cd /etc/sudoers.d`
`nano grader`

*Generate key-pair with `ssh-keygen` for user grader and copy private key as `~/.ssh/grader.rsa` in local machine*
Then on server shell copy public key in the user's .ssh directory
`mkdir .ssh`
`nano .ssh/authorized_keys`
`sudo ssh reload`

Source: Udacity Linux Web Servers Course

*Disable key-based authentication in SSH config*
`nano /etc/ssh/sshd_config`
`sudo service ssh reload`

Source: Udacity Linux Web Servers Course

*Update all repository lists and then upgrade currently installed packages*
`sudo apt-get update`
`sudo apt-get upgrade`

Source: Udacity Linux Web Servers Course

*Change the SSH port from 22 to 2200*
Edit `/etc/ssh/sshd_config` to change the SSH port.
Restart ssh service to check the connection is available in port 2200.

Source: Udacity Linux Web Servers Course

*Configure the Uncomplicated Firewall (UFW) to only allow incoming connections for SSH (port 2200), HTTP (port 80), and NTP (port 123)*

`sudo ufw default deny incoming`
`sudo ufw default allow outgoing`
`sudo ufw allow www`
`sudo ufw allow ntp`
`sudo ufw allow 2200`
`sudo ufw enable`

Source: Udacity Linux Web Servers Course

*Configure the local timezone to UTC*
Zone was already configured to UTC by default
To check and confirm it I ran `sudo dpkg-reconfigure tzdata`

Source: http://www.christopherirish.com/2012/03/21/how-to-set-the-timezone-on-ubuntu-server/

*Install and configure Apache to serve a Python mod_wsgi application*
`sudo apt-get install apache2`
`sudo apt-get install libapache2-mod-wsgi`
Tested with a simple mod-wsgi application as suggested for example in http://www.shellhacks.com/en/modwsgi-Hello-World-Example

Source: Udacity Linux Web Servers Course

*Install and configure PostgreSQL*
`sudo apt-get install postgresql`
Checked in `/etc/postgresql/9.3/main/pg_hba.conf` that no remote connections are allowed to postgresql server.
Using the `createuser` command line tool (wrapper for SQL CREATE USER) I created user catalog with no database creation privilege.
Then logged in as root to local postgresql server using `sudo -u postgres psql postgres` and created database `catalog2`. Granted all privileges to user `catalog` in database `catalog2`.

Source: http://www.postgresql.org/docs/current/static/sql-grant.html
https://help.ubuntu.com/community/PostgreSQL

*Install git* 

`sudo apt-get install git`
`git config --global user.name "fedefilo"`
`git config --global user.email "federico.vasen@gmail.com"`
`git config --list`

Source: https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-14-04

*Clone repository* 

git clone https://github.com/fedefilo/journal_guide.git

Source: Udacity Git&GitHub Course

*Make .git directory world and group unavailable*
`sudo chmod 700 .git`

*Create a virtual environment to run the Flask app*
sudo apt-get install python-virtualenv
`virtualenv venv`
`source venv/bin/activate`

Source: https://www.digitalocean.com/community/tutorials/how-to-deploy-a-flask-application-on-an-ubuntu-vps

*Install all the needed packages for running the app within the virtual env*
`pip install Flask`
`pip install SQLAlchemy`
`pip install six`
`pip install flask-seasurf`
`pip install oauth2client`
`pip install Requests`
`pip install httplib2`
`pip install Werkzeug`
`pip install google_api_python_client`

For the psycopg2 library, I followed adivse found on stackoverflow and installed it first outside de venv and then inside
`sudo apt-get build-dep python-psycopg2`
`source venv/bin/activate`
`pip install psycopg2`

Source: http://stackoverflow.com/questions/5420789/how-to-install-psycopg2-with-pip-on-python

*Configuring the app*

Made changes in the app code to tell SQLAlchemy the DBMS is now a postgresql and not SQLite. I had also to make changes to the code, since datatypes are more strict in postgres and 'None' values had to be included in a couple of cases. I also had to add `session.commit()` commands to save the changes in places that were not needed by sqlite.
http://docs.sqlalchemy.org/en/latest/dialects/postgresql.html#module-sqlalchemy.dialects.postgresql.psycopg2

Made changes in the app regarding the navigation of the filesystem and changed relative paths for absolute ones. 

To make FB authentication work, I added the URL of the server at the app page in developers.facebook.com

To configure apache I added the virtualhost configuration for the site in `/etc/apache2/sites-available/journal_guide.conf` and referenced it in the main apache config file.

Added a wsgi file to enable error logs and activate the virtual env and run the app. File is referenced it the apache sites-available folder.

Changed permissions to the app files to be readable by apache user `www-data`. The `static/pictures` folder also had to be writable by that user.

Edited `etc/hosts` to disbale annoying error message when using sudo.

### Extra credit

*Configuring cron jobs for automated updates*

I used the package unattended-updates and followed the instructions at https://help.ubuntu.com/lts/serverguide/automatic-updates.html

`sudo apt-get unattended-upgrades`
`sudo apt-get install unattended-upgrades`
`sudo nano /etc/apt/apt.conf.d/50unattended-upgrades`
`sudo nano /etc/apt/apt.conf.d/10periodic`

Logs can be checked in the folder `/var/log/unattended-upgrades`

*Preventing login abuse / banning users* 
I installed and configured fail2ban using the tutorial found at
https://www.digitalocean.com/community/tutorials/how-to-protect-ssh-with-fail2ban-on-ubuntu-14-04

*Performance monitoring*

I installed Glances, a command line tool for monitoring server CPU usage, memory, etc
To access the stats, just type `glances` at the shell.

I also installed munin, a package that allows monitoring from the webbrowser.
I followed the guide at https://www.digitalocean.com/community/tutorials/how-to-install-the-munin-monitoring-tool-on-ubuntu-14-04
Stats can be accessed at http://52.36.146.146/munin/MuninMonitor/MuninMonitor/index.html


#### Final remarks

The project was interesting and time consuming. I ran into a lot of issues configuring the app, mainly related to permissions. It helped me strengthen my abilities in the shell and learned a lot. Servers were once something completely strange to me and now I feel more confident. I hope exaplantions are clear!