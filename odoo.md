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
        # Trigger when from_date value change or add(when save button click)
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

### onchange(when field value change)
**constrains vs onchange**: constrains trigger when save button click but onchange trigger when field value change.
* @api.onchange():<br>
    * Example:<br>
        ```python 
        # Trigger when from_date value change or add
        @api.onchange('from_date')
            def _check_amount_to(self):
                print('to_date')

        ```
### depends(Return a decorator that specifies the field dependencies of a "compute" method (for new-style function fields).)
* @api.depends():<br>
    * Example:<br>
        ```python 
        pname = fields.Char(compute='_compute_pname')
        # Trigger when patner_id value change
	    @api.depends('partner_id.name', 'partner_id.is_company')
	    def _compute_pname(self):
		for record in self:
		    if record.partner_id.is_company:
			record.pname = (record.partner_id.name or "").upper()
		    else:
			record.pname = record.partner_id.name

        ```
		
### QWeb website
## API
* t-if
* t-elif
* t-else
* t-esc
* t-field
* t-call: call an xml template by it. Is's like include django template.
* t-call-assets
* t-extend
* t-jquery
* t-js
* t-name
* t-set t-value: declare a variable using t-set and set the value of this variable by t-value.In t-value we can calculate/call data from db.
* t-att vs t-attf
* t-log
* t-debug
* t-ignore
* t-foreach t-as
* t-raw
* groups

### Oddo Javascript
Syntax
```javascript
odoo.define('moduleName.id_name_for_this_js_module', function (require) {
	'use strict'
	// strict ensure that below js code compitable for every js version and device(IE,chrome);
	//require: require use for call another js module so that we can inherit them and modify them.
	//example of require: var core = require('web.core');
  	//			var _t = core._t;
	$(document).ready(function(){
	//custom code
	)};
)};
```

### Tricks
user is logged in or not: t-if="user_id._is_public()" if true then public or logged in user. ANother way, groups="base.group_public"

#### Restore large backup zip file: If backup zip filegreater then 1gb 
Then first we extract the zip...there sould be a *.sql file anf a filestore folder
First we create a db from /web/database/manager. Note: during db create don't select demo data.
After db create you can found you db folder in .local/share/Odoo/filestore/ (Where we paste our filestore folder later)
then we restore db by using this command: psql -U db_user db_name < dump_name.sql
 
