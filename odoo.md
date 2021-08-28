# Odoo install for development

sudo apt update && sudo apt upgrade
	
sudo apt install git python3-pip build-essential wget python3-dev python3-venv python3-wheel libxslt-dev libzip-dev libldap2-dev libsasl2-dev python3-setuptools node-less

** Install Wkhtmltopdf **

wget https://builds.wkhtmltopdf.org/0.12.1.3/wkhtmltox_0.12.1.3-1~bionic_amd64.deb

sudo apt install ./wkhtmltox_0.12.1.3-1~bionic_amd64.deb

git clone https://www.github.com/odoo/odoo --depth 1 --branch 13.0 /opt/odoo13/odoo

python3 -m venv odoo-venv
source odoo-venv/bin/activate
pip3 install wheel
pip3 install -r odoo/requirements.txt
pip3 install ptvsd
WIP

# Odoo debugin in vs code

python -m ptvsd --host localhost --port 5678 ./odoo/odoo-bin -d odoo13db --xmlrpc-port=8069 -c ./odoo-extra-addons/odoo-dev.conf  
# ORM
TODO: _name,_dicription,_inherit,_inherits,_custom,_table,_sequence,_sql_constraints, _rec_name, _order, _parent_name, _parent_store, _date_name, _fold_name <br>
TODO(method): user_has_groups, _fields_view_get, _compute_display_name, name_get
### Common ORM Method
|   Name     | Short Description           |
| ------------- |:-------------:|
| create      | Create New Record |
| write      | Update Existing Record      |
| copy | Create new record from existing record    |
| unlink | Delete record     |
| default_get | Set default value while create new record      |
| name_create | Create record with name field     |

### Search/Read related ORM Method
|   Name     | Short Description           |
| ------------- |:-------------:|
| browse      | Create New Record |
| search      | Update Existing Record      |
| search_count | Create new record from existing record    |
| name_search | Delete record     |
| read | Set default value while create new record      |
| read_group | Create record with name field     |

## API
type of api:
* @api.constrains()
* @api.onchange()
* @api.depends()
* @api.returns()
* @api.model
* @api.model_create_multi
* @api.multi
* @api.one

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
This decorator will trigger the call to the decorated function if any of the fields specified in the decorator is changed in the form. Here scope is limited to the same screen / model.<br>
Let's say on form we have fields "DOB" and "Age", so we can have @api.onchange decorator for "DOB", where as soon as you change the value of "DOB", you can calculate the "age" field.<br>

You may field similarities in @api.depends and @api.onchange, but the some differences are that scope of onchange is limited to the same screen / model while @api.depends works other related screen / model also.
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
This decorator is specifically used for "fields.function" in odoo. For a "field.function", you can calculate the value and store it in a field, where it may possible that the calculation depends on some other field(s) of same table or some other table, in that case you can use '@api.depends' to keep a 'watch' on a field of some table.<br>
So, this will trigger the call to the decorated function if any of the fields in the decorator is 'altered by ORM or changed in the form'.<br>
Let's say there is a table 'A' with fields "x,y & z" and table 'B' with fields "p", where 'p' is a field.function depending upon the field 'x' from table 'A', so if any of the change is made in the field 'x', it will trigger the decorated function for calculating the field 'p' in table 'B'.<br>

Make sure table "A" and "B" are related in some way.

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
### MODEL Inheritance
There are three type of model inheritance:<br>
	* Extend (Inheritance) same model
	* Traditional (Classical) Inheritance
	* Delegation Inheritance
**TODO**

Inheritance Types1
Extend (Inheritance) same model
Here we will use the only _inherit, we will not use the _name model attribute. The child model replaces the existing one. This inheritance is used when we want to add new fields or methods to the existing model.
```
class ModelA
_name = model_a
+fieldA

# No extra table will create
class ModelB:
_inherit = ModelA
+fieldB
```
In DB: Table model_a: fieldA,fieldB

Inheritance Types2
Traditional (Classical) Inheritance
In this inheritance, we use both _name and _inherit model attributes together. The new models get all methods, fields from its base.
```python
class Screen(models.Model):
    _name = 'delegation.screen'
    _description = 'Screen'

    size = fields.Float(string='Screen Size in inches')

class Keyboard(models.Model):
    _name = 'delegation.keyboard'
    _description = 'Keyboard'

    layout = fields.Char(string='Layout')

class Laptop(models.Model):
    _name = 'delegation.laptop'
    _description = 'Laptop'

    _inherits = {
        'delegation.screen': 'screen_id',
        'delegation.keyboard': 'keyboard_id',
    }

    name = fields.Char(string='Name')
    maker = fields.Char(string='Maker')

    # a Laptop has a screen
    screen_id = fields.Many2one('delegation.screen', required=True, ondelete="cascade")
    # a Laptop has a keyboard
    keyboard_id = fields.Many2one('delegation.keyboard', required=True, ondelete="cascade")

>>>
record = env['delegation.laptop'].create({
    'screen_id': env['delegation.screen'].create({'size': 13.0}).id,
    'keyboard_id': env['delegation.keyboard'].create({'layout': 'QWERTY'}).id,
})
record.size	# -> 13.0
record.layout	# -> 'QWERTY'
record.write({'size': 14.0})
>>>

```
In DB: Table model_a: fieldA
	Table model_b: fieldB, fieldA

Inheritance Types3
Delegation Inheritance
In delegation inheritance, we use the **_inherits** model attribute. This is used if you want to use another model in your current model.
NOTE: <br>
	* When ModelB data create it also create data on ModelA.
	* The delegation inheritance inherits only fields and methods are not inherited i.e all the field of ModelA can access by ModelB object.
	* **It can be useful, when we need to embed a model in our current model without affecting the existing views, but we want to have the fields of inherited objects.**
Example: when res.user create it also create res.partner for it.
```python
class ModelA
_name = model_a
+fieldA

# No extra table will create
class ModelB:
_name = model_b
_inherits = {"ModelA":"model_a_id"}
+fieldB
```
In DB: Table model_a: fieldA
	Table model_b: fieldB, model_a_id
### WIZARD
**model** : TransientModel <br>
#### How to open WIZARD
* Using Button Click(Object Type)
* Using Button Click(Action Type)
* Using Menu Click(Object Type)
* Using Action Menu

##### Using Button Click(Object Type)
wizard/student_fees_update_wizard.py<br>
```python
from odoo import api, models, fields


class StudentFeesUpdateWizard(models.TransientModel):
    _name = "student.feees.update.wizard"

    total_fees = fields.Float(string="Fees")


    def update_student_fees(self):
        # print("Yeah successfully click on update_student_fees method........")
	print(self._context) # output :> dict object
        self.env['school.student'].browse(self._context.get("active_ids")).update({'total_fees': self.total_fees})
        return True
```
security/ir.model.access.csv <br>
```
............
access_student_fees_update,student_fees_update,model_student_fees_update_wizard,base.group_user,1,1,1,1
............
```
wizard/student_fees_update_wizard_view.xml<br>
```xml
<odoo>
    <data>

        <record id="student_fees_update_form_view_wiz" model="ir.ui.view">
            <field name="name">student.fees.update.form.view.wiz</field>
            <field name="model">student.feees.update.wizard</field>
            <field name="arch" type="xml">
                <form>
                    <group>
                        <group>
                            <field name="total_fees" />
                        </group>
                    </group>

                    <footer>
                        <button string="Update Fees" name="update_student_fees" type="object"  />
                        <button string="Cancel" special="cancel" class="btn btn-secondary" />
                    </footer>
                </form>
            </field>
        </record>
    </data>
</odoo>
```
Using Button Click(Action Type)
views/views.xml<br>
```xml
<button name="school_student.student_fees_update_action" string="Wizard open using action" type="action" />
```
wizard/student_fees_update_wizard_view.xml<br>
```xml
<!--  Action-->
        <record id="student_fees_update_action" model="ir.actions.act_window">
            <field name="name">Student Fees Update</field>
            <field name="res_model">student.feees.update.wizard</field>
            <field name="view_mode">form</field>
            <field name="target">new</field>
        </record>
```
**TODO**<br>
Using Button Click(Action Type)
button
xml action

Using Menu Click(Object Type) contextual menu
button
xml action
binding_model_id
binding_view_types

Using Action Menu

menuitem> action

### Menu
tag: menuitem<br>
attr: id*, name, parent, action, sequence, active, web_icon, groups<br>
* id: unique id off meuitem
* name: Name/String of menuitem.
* **parent**: If the menuitem is a submenu then the value will be parent/root menuitem id. If no parent set then the menuitem will appear as top/root menu.
* action: id of action i.e. what happend when user click the menuitem.
* sequence: position value i.e. which position the menuitem will appear.
* active:
* web_icon: In odoo enterprice version, if the menuitem is root menu then it will show in homepage with icon image.
	TODO: screenshot
* groups: 

```xml
	<!-- Top menu item -->
        <menuitem id="sale_menu_root"
            name="Sales"
            web_icon="sale,static/description/icon.png"
            active="False"
            sequence="7"/>
	<!-- Submenu of Orders, it will show in navbar	  -->
	  <menuitem id="sale_order_menu"
            name="Orders"
            parent="sale_menu_root"
            sequence="2"/>
	<!-- 	Submenu of ORDERS, it will show as dropdown  -->
	 <menuitem id="menu_sale_quotations"
                action="action_quotations_with_onboarding"
                parent="sale_order_menu"
                sequence="1" groups="sales_team.group_sale_salesman"/>
	   
```
### Speical Command
|  Command Name     | Short Description           |
| ------------- |:-------------:|
| (0,0, {})      | Create New Record |
| (1, id, {})      | Edit Existing Record      |
| (2, id, False) | Remove record from database  Completly    |
| (3, id, False) | Remove record from Relational field but no permenent i.e unlink the relation     |
| (4, id, False) | Add exising record in Relational field      |
| (5, False, False) | Remove all Relational Record field but not permenet      |
| (6, False, [ ids ]) | Replace existing M2M field with new existing records   |

Example of **(0,0,{}}**:
```python
# It will create two new attribute line record of product template
product_template = self.env['product.template'].create({
            'name': 'Sofa',
            'attribute_line_ids': [
                (0, 0, {
                    'attribute_id': att_color.id,
                    'value_ids': [(6, 0, [att_color_red.id, att_color_blue.id])] # ignore it
                }),
                (0, 0, {
                    'attribute_id': att_size.id,
                    'value_ids': [(6, 0, [att_size_big.id, att_size_medium.id])] # ignore it
                })
            ]
        })
```
Example of **(1, id, {})**:
```python
# It will Write/Update record of given id value. Like in product template we update attribute line of 12.
product_template = product_template_obj.write({
            'attribute_line_ids': [
                (0, 12, {
                    'attribute_id': another_att_color_id.id,
                    'value_ids': [(6, 0, [att_color_red.id, att_color_blue.id])] # ignore it
                }),
            ]
        })
```
### Action
view_ref = which view want to open up when menu button click
### Tree
* Tree view is a list view.<br>
* User can create records through list view
* Aggregation function can use list view. Example: total sum/avarage of numeric column
* Add recordset to color/style
* user can priotize record by drag/drop feature
* User can invisible/visible speicific column
* Control some action(create,delete,edit,import,duplicate,export_xlsx)


tag: tree<br>
attr: string, decoration-info,decoration-bf, decoration-muted, js_class, editable<br>
Syntax:
```xml
	<!--  Tree view of accout.cash.rounding model	 -->
	<record id="rounding_tree_view" model="ir.ui.view">
            <field name="name">account.cash.rounding.tree</field>
            <field name="model">account.cash.rounding</field>
            <field name="arch" type="xml">
                <tree string="Rounding Tree">
                    <field name="name"/>
                    <field name="rounding"/>
                    <field name="rounding_method"/>
                </tree>
            </field>
        </record>
```
### Form TODO
tag: form<br>
header, sheet, group, notebook,field, 
Syntax:
```xml
	<record id="view_order_form" model="ir.ui.view">
            <field name="name">sale.order.form</field>
            <field name="model">sale.order</field>
            <field name="arch" type="xml">
	    <form string="Sales Order" class="o_sale_order">
		    <header>
			    ......................
		    </header>
		    <sheet>
		    </sheet>
```

### Button
## Type
	1. type='object':
	2. type='action':
### QWeb website
## QWeb Operation
Reference Link: https://www.cybrosys.com/blog/advanced-qweb-operations-in-odoo-14, 
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

### Inheritance view in xml
#### Add field in form view
```javascript
<odoo>
    <data>
	<!-- Add field in form view -->
	<record model="ir.ui.view" id="view_sale_order_inherited">
	    <field name="name">bis.inherited.sale.order.form</field>
	    <field name="model">sale.order</field>
	    <field name="inherit_id" ref="sale.view_order_form"/>
	    <field name="arch" type="xml">
		<xpath expr="//field[@name='partner_id']" position="before">
		    <field name='customer_verification_status' widget="selection"/>
		</xpath>
	    </field>
	</record>
    </data>
</odoo>
```
#### Add field in tree/list view
```javascript
<odoo>
    <data>
	<!-- Add field in tree/list view -->
	<record id="bis_sale_view_order_tree" model="ir.ui.view">
	    <field name="name">bis.sale.order.tree</field>
	    <field name="model">sale.order</field>
	    <field name="priority">100</field>
	    <field name="inherit_id" ref="sale.view_quotation_tree_with_onboarding"/>
	    <field name="arch" type="xml">
		<xpath expr="//tree[1]/field[@name='partner_id']" position="before">
		    <button icon="fa-check-circle text-success"  title="Verified Customer" 
		    attrs="{'invisible': ['|',('customer_verification_status','=','non-verified'),('customer_verification_status','=',False)]}" />
		    <field name='customer_verification_status' invisible="1"/>
		</xpath>
	    </field>
	</record>
    </data>
</odoo>
```
#### Add new filter in search view
**Reference** :
* https://www.odoo.yenthevg.com/adding-filters-existing-search-views/
```javascript
<odoo>
    <data>
	<record id="bis_sale_order_view_search_inherit_quotation" model="ir.ui.view">
	    <field name="name">bis.sale.order.search.inherit.quotation</field>
	    <field name="model">sale.order</field>
	    <field name="mode">extension</field>
	    <field name="inherit_id" ref="sale.sale_order_view_search_inherit_quotation"/>
	    <field name="arch" type="xml">
		<filter name="my_quotation" position="after">
		    <filter string="Verified Qutation" 
			    domain="[('customer_verification_status', '=', 'verified')]"
			    name="verified_customer"
			    help="Filter qutation by verified customer"
		    />
		    <filter string="Non Verified Qutation" 
			    domain="['|',('customer_verification_status', '=', 'non-verified'),('customer_verification_status', '=', False)]"
			    name="non_verified_customer"
			    help="Filter qutation by Non verified customer"
		    />
		</filter>
	    </field>
	</record>
    </data>
</odoo>
```
### t-name & t-extend:
When we need a template which will be call from js and show to front end then we will use t-name for that.
Syntax:
XML file:
```
<?xml version="1.0" encoding="UTF-8"?>
<templates id="template" xml:space="preserve">
	<t t-name="id1">
		<!-- 	HTML/XML code -->
	</t>
	<t t-name="id2">
		<!-- 	HTML/XML code -->
	</t>
	.....
</template>
```
**NOTE**: id="template" fixed we always use id="template" for t-name
How to modify/extend t-name field?
By using t-extend.
```
<t t-extend="id1" t-name="optional_name">
        <t t-jquery="selector" t-operation="after">
		<!--        xml code     -->
        </t>
    </t>
```
### Using domain what is displayed/filtering
* <button icon="fa-check-circle text-success"  title="Verified Customer" 
		    attrs="{'invisible': ['|',('customer_verification_status','=','non-verified'),('customer_verification_status','=',False)]}" /><br>
Explain: When all condition True, button will be hide/invisible/readonly.
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
var sAnimations = require('website.content.snippets.animation');
var ajax = require('web.ajax');
var core = require('web.core');
var weContext = require("web_editor.context");
var QWeb = core.qweb;
var session = require('web.session');
#### require('web.ajax')
Use this for api call where return data type json.
INFO: Odoo has some default path for Dataset when we can read/write/update db by call those path
Location of those path:odoo/addons/web/controllers/main/
Tricks: which method had http.route as type="json" we can call them from js using ajax.rpc
Example:
```javascript
	ajax.rpc('/web/dataset/call_kw/res_partner', {
                          "model": "res.partner",
                          "method": "read",
                          "args": [res_partner_id],
                          "kwargs": {'fields': ['name', 'zip']}
                    }).then(function (res) {
                        zipcode = res[0].zip;
                        if(zipcode){
                            sessionStorage.setItem('zipcode', zipcode);
                            location.reload();
                        }
                    });
```

#### require('web.session')
For read and write session value

Example:<br>
For read: sessionStorage.getItem('zipcode');<br>
For write: sessionStorage.setItem('state', state);<br>

### Website 
## Snippet & Snippet options
Reference:
* https://www.odoo.com/documentation/14.0/developer/howtos/themes.html#create-snippets
* https://soft29.ru/blog/entry/defining-a-snippet-option-in
### Tricks
user is logged in or not: t-if="user_id._is_public()" if true then public or logged in user. ANother way, groups="base.group_public"

#### When use store=True?
The store keyword used to enhance the performance; since the functional field will be computed each time it'll be visible to the user.<br>
**Assume, fieldA whose value come from a caculated function. So, if this field only show in form view, we can use store=False because in form view we are working only one record<br>
If we show fieldA in Tree/List view there should have multiple record, so every time the compute function called for generate the record. So if we use store=True then the value serve form db which was time saving**

without the store keyword [store=False], the functional field will not be stored in the database [e.g you can just access it by a browse record].<br>
If you use store=True [the default is False]; your functional field will be stored in the DB and will be calculated for just one time.

#### Create db from commandline
-d db_name --stop-after-init <br>
By default db create without demo data.If we need to create db with demo data then in dev.conf file paste below line<br>
without_demo = False

#### Restore large backup zip file: If backup zip filegreater then 1gb 
Then first we extract the zip...there sould be a *.sql file anf a filestore folder
#### CHange admin password from backend
https://www.odoo.com/es_ES/forum/ayuda-1/how-to-reset-the-odoo-admin-user-password-a-summary-for-different-odoo-versions-131992
**First we create a db**<br/>
sudu su - postgres<br/>
createdb testdb<br/>
psql<br/>
grant all privileges on database testdb to testuser;<br/>
exit;<br/>
exit<br>
then we restore db by using this command(main user): psql -U db_user db_name < dump.sql

After db create then we create db folder in .local/share/Odoo/filestore/ (Where we paste our filestore folde)
Finish

How to fix database expiration date issue:
Turn on odoo debug mode<br>
Settings>Tecnical>System Properties>database.expiration_date then change the date.

If the database already expired then what we will do
Turn on odoo debug mode<br>
Go to Settings.Then you can see a shadow block the frontend.we can't do anything

For remove that shado, open chrome console panel and write it "$.unblockUI();"  <br>
Reference: https://www.snippetbucket.com/odoo-database-has-expired-enterprise/


 
