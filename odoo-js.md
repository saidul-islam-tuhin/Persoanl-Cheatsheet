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
