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

Install Carton
```
sudo apt install -y carton
```
<br></br>

1. Get the code (Changed to project repo rather than FixMyStreet)
```
git clone --recursive https://github.com/jkinlan/NCI_Project_CivLink.git
cd NCI_Project_CivLink/fixmystreet
```
<br></br>

2. Install prerequisite packages
```
sudo bin/install_packages
```
<br></br>

3. Create a new PostgreSQL database
```
sudo -u postgres psql <<EOF
CREATE USER fms WITH PASSWORD 'somepassword';
CREATE DATABASE fms WITH OWNER fms;
\c fms
CREATE LANGUAGE plpgsql;
\q
EOF
```
<br></br>

4. Install required dependencies, and other setup
```
script/setup
```
Issue where Image::PNG::QRCode not installing
Run:
```
carton install
```
<br></br>

5. Set up config
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

<br></br>
Rerun setup to allow complete
```
script/setup
```
<br></br>

6. Set up some required data
```
bin/update-all-reports
```
<br></br>

7. Run
```
script/server
```
<br></br>

---
---
---

Possible additional step
Add extention to SQL??
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
