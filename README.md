# NCI_Project_CivLink - CivLink

CivLink is a project to help the public engage with local and national government 
in Ireland, by reporting common street problems such as potholes and broken street 
lights to the appropriate authority, and create petitions which can be submitted 
to the relevant department.

### Reporting issues
Users report problems using a combination of address and selectig a location
on a map, and the report is automatically sent to the correct authority, by email 
or using a web service such as Open311.
Reported problems are visible to everyone so they can see if something has
already been reported and leave updates. Users can also subscribe to email or
RSS alerts of problems in their area.

####
CivLink contains a submodule of [FixMyStreet](https://github.com/mysociety/fixmystreet), 
an open source project to help people run websites for reporting common street 
problems such as potholes and broken street lights to the appropriate authority.

### Petitions
Site visitors can create a user account, log in, and create petitions. Petition 
creators can share the URL for their petition to generate signatures. When the 
petition crosses a certain threshold, the petition becomes "public" and is listed 
as an open petition on the site's "open petitions" page.

####
CivLink uses a copy of [Pytition](https://github.com/pytition/Pytition), 
a project for self-hosted privacy-friendly online petitions.

## Installation

### Landing Page & Reverse Proxy in Nginx

Update hosts
```
sudo apt update && sudo apt upgrade -y
```

Install Nginx
```
sudo apt install -y nginx
```

Create Nginx cofig for Landing Page
```
sudo nano /etc/nginx/sites-available/civlink.kravemedia.ie
```
Enter into file
```
server {
    listen 80;
    server_name civlink.kravemedia.ie;

    root /home/ubuntu/NCI_Project_CivLink/civlink;
    index index.html;
}
```
Enable site
```
sudo ln -s /etc/nginx/sites-available/civlink.kravemedia.ie /etc/nginx/sites-enabled/
```
(temp) Create HTML file for landing page (replace with Repo path to landing page site)
```
sudo mkdir -p /home/ubuntu/NCI_Project_CivLink/civlink
echo "<h1>Welcome to CivLink</h1>" | sudo tee /home/ubuntu/NCI_Project_CivLink/civlink/index.html
```

Create Nginx config file for Petition site
```
sudo nano /etc/nginx/sites-available/petitions.civlink.kravemedia.ie
```
Enter into file
```
server {
    listen 80;
    server_name petitions.civlink.kravemedia.ie;

    location / {
        proxy_pass http://localhost:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
Enable site
```
sudo ln -s /etc/nginx/sites-available/petitions.civlink.kravemedia.ie /etc/nginx/sites-enabled/
```

Create Nginc config file for FixMyStreet
```
sudo nano /etc/nginx/sites-available/fixmystreet.civlink.kravemedia.ie
```
Enter into file
```
server {
    listen 80;
    server_name fixmystreet.civlink.kravemedia.ie;

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
Enable site
```
sudo ln -s /etc/nginx/sites-available/fixmystreet.civlink.kravemedia.ie /etc/nginx/sites-enabled/
```
Update hosts file
```
/etc/hosts
```
Add
```
127.0.0.1 civlink.kravemedia.ie
127.0.0.1 fixmystreet.civlink.kravemedia.ie
127.0.0.1 petitions.civlink.kravemedia.ie
```
---

### FixMyStreet (https://fixmystreet.org/install/manual-install)

Update hosts
```
sudo apt update && sudo apt upgrade -y
```
</br>

Install Git & Carton
```
sudo apt install -y carton git libplack-perl
```
</br>

1) Get the code (Changed to project repo rather than FixMyStreet)
```
git clone --recursive https://github.com/jkinlan/NCI_Project_CivLink.git
cd NCI_Project_CivLink/fixmystreet
```
</br>

2) Install prerequisite packages
```
sudo bin/install_packages
```
</br>

3) Create a new PostgreSQL database
```
sudo -u postgres psql <<EOF
CREATE USER fms WITH PASSWORD 'somepassword';
CREATE DATABASE fms WITH OWNER fms;
\c fms
CREATE LANGUAGE plpgsql;
\q
EOF
```
</br>

4) Install required dependencies, and other setup
```
sudo script/setup
```
Issue where Image::PNG::QRCode not installing
Run:
```
rm cpanfile.snapshot
carton install
```
</br>

5) Set up config
```
cp conf/general.yml-example conf/general.yml
```
Modify config
```
nano conf/general.yml
```
Set configuration as below:

FMS_DB_PASS: 'somepassword'

BASE_URL: 'http://localhost:3000'

STAGING_SITE: 0

MAPIT_URL: e.g. 'http://localhost:3000/fakemapit/'

</br>

Rerun setup to allow complete
```
script/setup
```
</br>


6) Set up some required data
```
bin/update-all-reports
```
Create Superuser
```
bin/createsuperuser jkinlan@kravemedia.ie P@55w0rd
```
</br>

7) Run
```
plackup -E production -s FCGI --listen 127.0.0.1 script/fixmystreet_app_server.pl
```
</br>

Set the locale to Ireland.
```
sudo locale-gen en_IE.UTF-8
sudo update-locale LANG=en_IE.UTF-8
export LANG=en_IE.UTF-8
export LC_ALL=en_IE.UTF-8
```

---
</br>

### Pytition (https://pytition.readthedocs.io/en/latest/installation.html)
Update hosts
```
sudo apt update && sudo apt -y upgrade -y
```
</br>

1. System Preparation

Install system-level dependencies including database, Python headers, and WSGI tools:

```
sudo apt install -y python3-full python3-pip python3-dev python3-venv build-essential \
                    libssl-dev libffi-dev libmysqlclient-dev mysql-server nginx \
                    uwsgi uwsgi-plugin-python3 gettext
```
</br>

2. Change Directory to Pytition Repository
```
cd NCI_Project_CivLink/pytition
```
</br>

3. Set Up a Python Virtual Environment
```
python3 -m venv ~/pytition_venv
source ~/pytition_venv/bin/activate
```
</br>

4. Install Python Dependencies
```
pip install --upgrade pip
pip install -r requirements.txt
```
</br>

5. Create MySQL Database and User
Start MySQL:
```
sudo systemctl start mysql
sudo systemctl enable mysql
```
</br>
Login to MySQL:

```
sudo mysql
```
</br>
Inside MySQL shell:

```
CREATE DATABASE pytition_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
CREATE USER 'pytition_user'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON pytition_db.* TO 'pytition_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
</br>
6. Create MySQL Config File for Django
Create a MySQL client config file for Django at:

```
nano pytition_my.cnf
```
Add:
```
[client]
database = pytition_db
user = pytition_user
password = your_secure_password
host = localhost
```
</br>

This file should only be readable by root. Check with:
```
chmod 600 pytition_my.cnf
```
</br>
7. Configure Django to Use MySQL
Edit:

```
cp pytition/pytition/settings/config_example.py pytition/pytition/settings/config.py
nano pytition/pytition/settings/config.py
```
</br>

Find the DATABASES section and update it:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'OPTIONS': {
            'read_default_file': '/root/pytition_my.cnf',
            'init_command': "SET sql_mode='STRICT_TRANS_TABLES'",
        },
    }
}
```

Also set secret key.

Replace ~ with /root — Django does not expand ~.
</br>

8. Set Django Environment Variable
In your shell (or in ~/.bashrc):
```
export DJANGO_SETTINGS_MODULE="pytition.settings.config"
```
<br>

9. Run Migrations and Collect Static Files
Make sure you're in the Django project root (manage.py is here):
```
cd pytition
```
</br>

Run:
```
python3 manage.py migrate
python3 manage.py collectstatic --noinput
python3 manage.py compilemessages
python3 manage.py createsuperuser
```
</br>

10. Test the Development Server (Optional)
```
python3 manage.py runserver 0.0.0.0:8000
```
</br>
