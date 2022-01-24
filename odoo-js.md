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

