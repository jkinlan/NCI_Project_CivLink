# NCI_Project_CivLink - CivLink

CivLink is a project to help the public engage with local and national government 
in Ireland, by reporting common street problems such as potholes and broken street 
lights to the appropriate authority, and create petitions which can be submitted 
to the relevant department.

## Reporting issues
Users report problems using a combination of address and selectig a location
on a map, and the report is automatically sent to the correct authority, by email 
or using a web service such as Open311.
Reported problems are visible to everyone so they can see if something has
already been reported and leave updates. Users can also subscribe to email or
RSS alerts of problems in their area.

###
CivLink contains a submodule of [FixMyStreet](https://github.com/mysociety/fixmystreet), 
an open source project to help people run websites for reporting common street 
problems such as potholes and broken street lights to the appropriate authority.

## Petitions
Site visitors can create a user account, log in, and create petitions. Petition 
creators can share the URL for their petition to generate signatures. When the 
petition crosses a certain threshold, the petition becomes "public" and is listed 
as an open petition on the site's "open petitions" page.

###
CivLink uses a copy of [petitions](https://github.com/WhiteHouse/petitions), 
a GNU General Public License project that lets users create and sign petitions.

## Installation
### FixMyStreet (https://fixmystreet.org/install/manual-install)
Update hosts
```
sudo apt update && sudo apt install -y git
```
<br></br>

Install Dependencies
```
apt-get install -y git 
apt-get install -y curl 
apt-get install -y build-essential 
apt-get install -y libexpat1-dev 
apt-get install -y libgmp3-dev 
apt-get install -y imagemagick 
apt-get install -y libimage-magick-perl 
apt-get install -y gettext 
apt-get install -y postgresql 
apt-get install -y postgresql-contrib 
apt-get install -y postgresql-server-dev-all 
apt-get install -y libpq-dev 
apt-get install -y cpanminus
apt-get install -y carton
apt-get install -y postgresql-16-postgis-3

```
<br></br>
Clone Repo
```
git clone --recursive https://github.com/jkinlan/NCI_Project_CivLink.git
```
<br></br>
Change directory to fixmyscreet
```
cd NCI_Project_CivLink/fixmystreet
```
<br></br>
Install prerequisite packages
```
sudo bin/install_packages
```
<br></br>
Create a new PostgreSQL database
```
sudo -u postgres psql <<EOF
CREATE USER fms WITH PASSWORD 'somepassword';
CREATE DATABASE fms WITH OWNER fms;
\c fms
CREATE EXTENSION postgis;
CREATE LANGUAGE plpgsql;
\q
EOF
```
<br></br>
Grant all permissions on DB to fms user???
```
sudo -u postgres psql -c "GRANT ALL PRIVILEGES ON DATABASE fms TO fms;"
sudo -u postgres psql -d fms -c "GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO fms;"
sudo -u postgres psql -d fms -c "GRANT ALL PRIVILEGES ON ALL SEQUENCES IN SCHEMA public TO fms;"
```
<br></br>
Install required dependencies, and other setup
```
script/setup
```
<br></br>
Clean Carton environment (avoid issue with installing Image::PNG::QRCode)
```
rm -rf local/ cpanfile.snapshot
carton install
```
<br></br>
Set up config
```
cp conf/general.yml-example conf/general.yml
nano conf/general.yml
```
Set configuration as below:

FMS_DB_PASS: 'somepassword'

BASE_URL: 'http://localhost:3000'

MAPIT_URL: e.g. 'http://localhost:3000/fakemapit/'

<br></br>
Load initial report data
```
bin/update-all-reports
```
<br></br>
Run
```
script/server
```
