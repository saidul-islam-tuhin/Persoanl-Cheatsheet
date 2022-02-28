### Autocomplete/Search Dropdown
## Using jquery
Basic Syntax
```js
$("input#postal_code").autocomplete({
     source: ['Python','Java','C','C++'],
     select: function (event, ui) {
         var label = ui.item.label;
         var value = ui.item.value;
         alert(value)
     },
   minLength: 1,
});
```
Get data from backend as json
```js
this._fetch().then(data => {
        $("input#postal_code").autocomplete({
            source: $.parseJSON(data),
            select: function (event, ui) {
                var label = ui.item.label;
                var value = ui.item.value;
                alert(value)
            },
            minLength: 1,

        });
    });
 
 _fetch: function () {
        return this._rpc({
            route: '/api/get_places',
            params: {},
        });
    },
```
Get data from backend as json **based on searching value**
```js
this.$input.autocomplete({
            source: function (request, response) {
                console.log(request.term);
                ajax.jsonRpc('/api/get_places', 'call', {
                    search: request.term, // term contain search value
                    namespace: 0,
                    limit: 8,
                }).then(function (data) {
                    debugger;
                    response($.parseJSON(data));
                });
            },
            select: function (event, ui) {
                var label = ui.item.label;
                var value = ui.item.value;
                alert(value)
            },
            minLength: 1,

            open: function( event, ui ) {}

        });
 ```
### Dialog example
## Open form in dialog and save it in website(without using formview)
```
js
var TicketDialog =  Dialog.extend({
        template: 'bista_website_livechat.ticket_dialog',
        xmlDependencies: Dialog.prototype.xmlDependencies.concat([
            '/bista_website_livechat/static/src/xml/helpdesk_ticket_form.xml', ]),

            init: function (parent, options) {
                var self = this;
                options = options || {};
        
                options.title = options.title || _t("Ticket");
                options.size = options.size || 'medium';
                if (!options.buttons) {
                    options.buttons = [];
                    options.buttons.push({text: _t("Confirm"), classes: "btn-primary", click: function (e) {
                        self._onConfirm();
                    }});
                    options.buttons.push({text: _t("Cancel"), close: true});
                }
                if (options._chatWindow) {
                    this._chatWindow = options._chatWindow
                }
                this._super(parent, options);
        
            },
            start: function () {
                var self = this;
                this.$el.find("input[name='csrf_token']").val(odoo.csrf_token);
                return this._super.apply(this, arguments);
            },
            _onConfirm: function (ev) {
                this.$form = this.$el.find("form");
                var self = this;
                var error_found = false;
                // Error check
                this.$form.find('.o_website_form_required').each(function (k, field){
                    var $field = $(field).find('.o_website_form_input');
                    if (!$field.val()){
                        $field.addClass("is-invalid");
                        error_found = true
                    }
                    else {
                        $field.removeClass("is-invalid");
                    }
                })
                if (error_found) {
                    return
                }
                this.form_fields = this.$form.serializeArray();
                $.each(this.$form.find('input[type=file]'), function (outer_index, input) {
                    $.each($(input).prop('files'), function (index, file) {
                        // Index field name as ajax won't accept arrays of files
                        // when aggregating multiple files into a single field value
                        self.form_fields.push({
                            name: input.name + '[' + outer_index + '][' + index + ']',
                            value: file
                        });
                    });
                });
                var form_values = {};
                _.each(this.form_fields, function (input) {
                    if (input.name in form_values) {
                        // If a value already exists for this field,
                        // we are facing a x2many field, so we store
                        // the values in an array.
                        if (Array.isArray(form_values[input.name])) {
                            form_values[input.name].push(input.value);
                        } else {
                            form_values[input.name] = [form_values[input.name], input.value];
                        }
                    } else {
                        if (input.value !== '') {
                            form_values[input.name] = input.value;
                        }
                    }
                });
                self.post_form(form_values)
            },

            post_form: function(form_values) {
                var self = this;
                ajax.post(this.$form.attr('action') + (this.$form.data('force_action')||this.$form.data('model_name')), form_values)
                .then(function (result_data) {
                    result_data = JSON.parse(result_data);
                    if (!result_data.id) {
                        alert("Sorry!! we can't create right now. Please Try later.")
                    } else {
                        // Success, close chat window and redirect
                        if (self._chatWindow) {
                            self._chatWindow.destroy();
                            utils.set_cookie('im_livechat_session', "", -1);
                        }
                        
                        var success_page = self.$form.attr('data-success_page');
                        if (success_page) {
                            $(window.location).attr('href', success_page);
                        }
                    }
                })

            },
    });
    
    // open dialog
    var ticket_dialog = new TicketDialog(self, {_chatWindow});
    ticket_dialog.open();
```
 
Include/Extend existing class of js

```js
import { patch } from 'web.utils';
import { Activity } from '@mail/components/activity/activity';
// REFERENCE: odoo/addons/calendar/static/src/components/activity/activity.js
```
Patching async function
```js
patch(object, "async _super patch", {
  async myAsyncFn() {
    const _super = this._super.bind(this);
    await Promise.resolve();
    await _super(...arguments);
    // await this._super(...arguments); // this._super is undefined.
  },
});
```

Extend owl component
There are two types of inherit mode
```xml
<?xml version="1.0" encoding="UTF-8"?>
<templates id="template" xml:space="preserve">

    <t t-inherit="mail.Activity" t-inherit-mode="extension">
        <xpath expr="//button[hasclass('o_Activity_editButton')]" position="attributes">
            <attribute name="t-if">!activity.calendar_event_id</attribute>
        </xpath>
    </t>

</templates>
// REFERENCE: odoo/addons/calendar/static/src/components/activity/activity.xml
```

