##Odoo install for development

sudo apt update && sudo apt upgrade
	
sudo apt install git python3-pip build-essential wget python3-dev python3-venv python3-wheel libxslt-dev libzip-dev libldap2-dev libsasl2-dev python3-setuptools node-less

** Install Wkhtmltopdf **

wget https://builds.wkhtmltopdf.org/0.12.1.3/wkhtmltox_0.12.1.3-1~bionic_amd64.deb

sudo apt install ./wkhtmltox_0.12.1.3-1~bionic_amd64.deb

git clone https://www.github.com/odoo/odoo --depth 1 --branch 13.0 /opt/odoo13/odoo

WIP

## Odoo deugin in vs code

python -m ptvsd --host localhost --port 5678 ./odoo/odoo-bin -d odoo13db --xmlrpc-port=8069 -c ./odoo-extra-addons/odoo-dev.conf  


##API
