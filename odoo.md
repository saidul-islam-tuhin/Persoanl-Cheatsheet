# Odoo install for development

sudo apt update && sudo apt upgrade
	
sudo apt install git python3-pip build-essential wget python3-dev python3-venv python3-wheel libxslt-dev libzip-dev libldap2-dev libsasl2-dev python3-setuptools node-less

** Install Wkhtmltopdf **

wget https://builds.wkhtmltopdf.org/0.12.1.3/wkhtmltox_0.12.1.3-1~bionic_amd64.deb

sudo apt install ./wkhtmltox_0.12.1.3-1~bionic_amd64.deb

git clone https://www.github.com/odoo/odoo --depth 1 --branch 13.0 /opt/odoo13/odoo

WIP

# Odoo debugin in vs code

python -m ptvsd --host localhost --port 5678 ./odoo/odoo-bin -d odoo13db --xmlrpc-port=8069 -c ./odoo-extra-addons/odoo-dev.conf  


# API
type of api:
* @api.constrains()
* @api.onchange()
* @api.depends()
* @api.returns()
* @api.model
* @api.model_create_multi

### constrains(restriction\check validity data before save data)
There are two type of constrains <br>
* _sql_constraints : sql query based.<br>
    * Syntex:<br>
				``` 
				_sql_constraints = [('constrain_name', 'unique(field_name)', 'Error message'),]
				```
				
    * Examaple:<br>
     ```python
		sql_constraints = [
        ('code_company_uniq', 'unique (code,company_id)', 'The code of the account must be unique per company !')
        ]
    ```
* @api.constrains(): python based constrains<br>
    * Example:<br>
        ```python 
        # Trigger when from_date value change or add
        @api.constrains('from_date')
            def _check_amount_to(self):
                print('to_date')

        # Trigger when from_date value change or add
        # Trigger when to_date value change or add
        # Trigger when from_date and to_date value change or add
        @api.constrains('from_date','to_date')
            def _check_amount(self):
                print('from_date to_date')
        ```
		When from_date value change first trigger _check_amount then _check_amount_to<br>
        When from_date and to_date value change first trigger _check_amount then _check_amount_to<br>
