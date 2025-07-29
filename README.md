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
</br>

Install Carton
```
sudo apt install -y carton
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

### WhiteHouse/petitions (https://github.com/WhiteHouse/petitions/blob/7.x-3.x/INSTALL.md)
Update hosts
```
sudo apt update && sudo apt install -y git
```
</br>
Install components

```
sudo apt install -y apache2
```
If more are needed, use this list.
sudo apt install -y apache2 mysql-server php php-mysql libapache2-mod-php php-cli php-gd php-curl php-xml php-mbstring unzip
sudo systemctl enable --now apache2 mysql

1) Install Drush
```
sudo apt install -y composer
composer global require drush/drush
export PATH="$HOME/.composer/vendor/bin:$PATH"
```

2) Download Drupal 7.x
```
cd /var/www
drush dl drupal-7 --drupal-project-rename=drupal
cd drupal
```
3) Place this petitions directory inside Drupal's profiles directory.

4) Use Drush make to download contrib projects like this:

```
drush -y make --no-core --contrib-destination=. drupal-org.make
```

5) Follow the normal Drupal installation process. When prompted to select
     a profile, select "Petitions." Drupal will rewrite your settings.php file.

6) **IMPORTANT!** Configure second database for signature processing:

For production sites, add databases for processing and archiving the signature
queue in your settings.php file. Use 'signatures_processing' and
'signatures_archive' as the keys for the configuration. For example, the
database configuration in your settings.php should look similar to this:

```php
    $databases = array (
      'default' =>
        array (
          'default' =>
          array (
            'database' => 'petitions',
            'username' => 'dbuser',
            'password' => '******',
            'host' => 'localhost',
            'port' => '',
            'driver' => 'mysql',
            'prefix' => '',
        ),
      ),
      'signatures_processing' =>
        array (
          'default' =>
          array (
            'database' => 'signatures_processing',
            'username' => 'dbuser',
            'password' => '******',
            'host' => 'localhost',
            'port' => '',
            'driver' => 'mysql',
            'prefix' => '',
        ),
      ),
      'signatures_archive' =>
        array (
          'default' =>
          array (
            'database' => 'signatures_archive',
            'username' => 'dbuser',
            'password' => '******',
            'host' => 'localhost',
            'port' => '',
            'driver' => 'mysql',
            'prefix' => '',
        ),
      ),
    );
```

(Further documentation on multiple databases: https://drupal.org/node/18429)

7) Enable needed modules.

Here is a list of modules that should be enabled for the system to run properly:
<table>
  <tr>
     <th><B>Module Name</B></th>
     <th><B>Module</B></th>
     <th><B>Status</B></th>
  </tr>
  <tr>
    <td>Petitions - LoginToboggan Settings</td>
    <td>petitions_logintoboggan_settings</td>
    <td>Enabled</td>
  </tr>
  <tr>
    <td>Taxonomy Sync</td>
    <td>taxonomy_sync</td>
    <td>Enabled</td>
  </tr>
  <tr>
    <td>Page</td>
    <td>wh_petition_page</td>
    <td>Enabled</td>
  </tr>
  <tr>
    <td>Response</td>
    <td>wh_response_feature</td>
    <td>Enabled Overridden</td>
  </tr>
  <tr>
    <td>WH User SS Data</td>
    <td>wh_user_ss_data</td>
    <td>Enabled</td>
  </tr>
  <tr>
    <td>Whitehouse User Profile</td>
    <td>wh_user_profile</td>
    <td>Enabled Overridden</td>
  </tr>
  <tr>
    <td>Contexts</td>
    <td>wtp_contexts</td>
    <td>Enabled</td>
  </tr>
  <tr>
    <td>Misc</td>
    <td>wh_misc</td>
    <td>Enabled</td>
  </tr>
</table>

8) The "main" profile should have these fields: First Name, Last Name, City,
     State, Zip, Country. To confirm, check here:

* `admin/structure/profiles`
* `admin/structure/profiles/manage/main/fields`

If required profile fields are missing, revert Whitehouse User Profile (wh_user_profile) to default here: `admin/structure/features`

9) By default petitions are not made public on the site until they clear a certain threshold of signatures. To collect these first signatures, signers must go directly to the petition's URL. Set the signature threshold here:
        `admin/config/system/petitions`

10) Users won't be able to create accounts until CAPTCHA is configured. Just to get things working, all you need to do is go here and follow the link on the config page to get an API key for your site:
        `admin/config/people/captcha/recaptcha`

11) For development, you may want to add this to settings.php:

```php
        $conf['error_level'] = 2;         // Show all messages on your screen.
        ini_set('display_errors', TRUE);  // These lines give you content on
                                          // "white screen of death" (WSOD) pages.
       ini_set('display_startup_errors', TRUE);
```
Necessary Setup
--------------
- create taxonomy with machine name petition_type and populate with terms
- create a menu link to add petition entities (/petition/create)
- add terms to media type taxonomy used in Response nodes

- setup signature form
  -  create api key node and set key value to accepted.
  - add api key to /admin/config/services/petitionssignatureform
  - add signature form block to a page region /admin/structure/block/manage/petitionssignatureform/petitionssignatureform_form/configure

Known Issues
--------
- signature form displays a Contact Administrator message at present.
- /responses page gives 403
- error presented when viewing responses

```
Error: Call to undefined function wh_petition_tool_twitter_link() in wh_response_preprocess_node() (line 868 of /var/www/petitions/docroot/profiles/petitions/modules/custom/wh_response/wh_response.module
```
