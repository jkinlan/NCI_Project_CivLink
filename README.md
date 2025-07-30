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
CivLink uses a copy of [e-petitions](https://github.com/alphagov/e-petitions), 
a MIT License project that lets users create and sign petitions.

## Installation
### FixMyStreet (https://fixmystreet.org/install/manual-install)

Update hosts
```
sudo apt update && sudo apt upgrade -y
```
</br>

Install Git & Carton
```
sudo apt install -y carton git
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
script/server
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

## Installation
### Pytition (https://pytition.readthedocs.io/en/latest/installation.html)

```
sudo apt install git virtualenv python3-dev build-essential mariadb-server gettext libzip-dev libssl-dev
```
```
sudo apt install libmariadb-dev-compat
```
```
apt install -y python3-full
apt install -y python3-pip
apt install -y python3-django
```
```
curl -sSL https://pdm-project.org/install-pdm.py | python3 -
```
```
curl -sSLO https://pdm-project.org/install-pdm.py
curl -sSL https://pdm-project.org/install-pdm.py.sha256 | shasum -a 256 -c -
# Run the installer
python3 install-pdm.py
```
```
pdm self update
```
```
version=$(curl -s https://api.github.com/repos/pytition/pytition/releases/latest | grep "tag_name" | cut -d : -f2,3 | tr -d \" | tr -d ,)
```
```
mkdir -p www/static www/mediaroot
```
```
cd www
git clone https://github.com/pytition/pytition
cd pytition
git checkout $version
```
```
pdm self update
pdm sync --clean
eval $(pdm venv activate)
```
```
password="ENTER_A_SECURE_PASSWORD_YOU_WILL_REMEMBER_HERE"
sudo mysql -h localhost -u root -Bse "CREATE USER pytition@localhost IDENTIFIED BY '${password}'; CREATE DATABASE pytition; GRANT USAGE ON *.* TO 'pytition'@localhost; GRANT ALL privileges ON pytition.* TO pytition@localhost; FLUSH PRIVILEGES;"
```
</br>
Write your SQL credential file in my.cnf outside of www:

```
[client]
database = pytition
user = pytition
password = YOUR_PASSWORD_HERE
default-character-set = utf8
```
```
cd www/pytition
cp pytition/pytition/settings/config_example.py pytition/pytition/settings/config.py
```
```
nano pytition/pytition/settings/config.py
```
Set:
SECRET_KEY = 'YV&(876(*&^g(*T9&^F97GFYUJgKjgkjguy&*G'
STATIC_URL = '/static/'
STATIC_ROOT = '/home/pytition/www/static'
MEDIA_URL = '/mediaroot/'
MEDIA_ROOT = ''
DATABASES = {}
ALLOWED_HOSTS = ['127.0.0.1', 'localhost', '[::1]']
```
cd pytition
export DJANGO_SETTINGS_MODULE="pytition.settings.config"
python3 manage.py migrate
python3 manage.py collectstatic
python3 manage.py compilemessages
python3 manage.py createsuperuser
```
