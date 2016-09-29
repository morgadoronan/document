# UAU-Componentes

## Instalação

Inclua os arquivos abaixo na sua página
```html
<!--angularjs scripts/modules -->
<script src="<%=ResolveUrl("~/")%>Scripts/angularjs/angular.js"></script>
<script src="<%=ResolveUrl("~/")%>Scripts/angularjs/angular-sanitize.min.js"></script>
<script src="<%=ResolveUrl("~/")%>UAUComponente/lib/ngModule/angular.ng-modules.js"></script>
<script src="<%=ResolveUrl("~/")%>UAUComponente/lib/ngStorage/ngStorage.min.js"></script>
<script src="<%=ResolveUrl("~/")%>Scripts/angularjs/angular-touch.js"></script>
<script src="<%=ResolveUrl("~/")%>Scripts/angularjs/angular-animate.js"></script>    

<!-- ui-select files -->
<script src="<%=ResolveUrl("~/")%>UAUComponente/lib/select/js/select.js"></script>

<!-- themes -->
<link href="<%=ResolveUrl("~/")%>Content/bootstrap/css/bootstrap.css" rel="stylesheet" />
<link href="<%=ResolveUrl("~/")%>UAUComponente/lib/select/css/select2.css" rel="stylesheet" />
<link href="<%=ResolveUrl("~/")%>UAUComponente/lib/select/css/selectize.default.css" rel="stylesheet" />
<link href="<%=ResolveUrl("~/")%>UAUComponente/lib/select/css/select.css" rel="stylesheet" />
<link href="<%=ResolveUrl("~/")%>UAUComponente/src/uau-componente.css" rel="stylesheet" />

<!--scripts uau -->
<script src="<%=ResolveUrl("~/")%>UAUComponente/src/uau-common.js"></script>
<script src="<%=ResolveUrl("~/")%>UAUComponente/src/uau-componente.js"></script>
```

Adicione a dependência do `uau-componente` no seu módulo
```javascript
angular.module('meuApp', ['uau-componente']);
```

Crie um `controller` para seu módulo
```javascript
.controller('meuControle', function ($scope) {
...
});
```

Referencie o módulo adicionando a diretiva `ng-app` na sua página
```html
<div ng-app="meuApp">
...
...
</div>
```

Utilize a diretiva `uau-componente` na sua página juntamente com o input que irá armazenar os dados quando selecionado:

```html
<input type="hidden" ng-model="exemplo" value="{{exemplo}}" id="txtMeuExemplo" runat="server" />
<uau-componente 
	controle="meuControle"
	modelo="exemplo"
	titulo="Selecione o exemplo"
	webservice="/api/Exemplo/ConsultarMeuExemplo"
	parametros="cliente_cod;status_cli"
	campo="empresa_cod;obra_cod">
</uau-componente>
```

## Atributos
### diretiva uau-componente
* `controle`      — Nome do controle criado para o módulo (obrigatorio)
* `modelo`        — Nome do scope da diretiva. Vincula o dado selecionado ao input do uau-componente (obrigatorio)
* `titulo`        — Texto padrão exibido no componente, semelhante ao placeholder do html
* `webservice`    — Endereço do webservice que retornará os dados para o uau-componente  (deve retornar JSON)
* `parametros`    — Nome dos parâmetros que compõe o webservice. Caso seja mais de um, separar por `;`. 
	* `exemplo da url:` `/api/Exemplo/ConsultarMeuExemplo?cliente_cod=x&status_cli=y`
* `campo`         — Define o nome da propriedade que irá ver visualizada uau-componente. 
 
## Usage Example

[Live demo](https://rawgithub.com/g00fy-/angular-datepicker/master/app/index.html)

## New features

This fork of angular-datepicker contains several features.

### Timezone Support

* The directive will work with or without a specified timezone. 
* If the timezone is known, it can be assigned to the datepicker via the `timezone` attribute. 
* If no timezone is provided, then the local time will be used.

##### No timezone information

```html
<div date-picker></div>
```

##### Specific timezone (London, UK)

```html
<div date-picker timezone="Europe/London"></div>
```


##### Specific timezone (Hong Kong, CN)

```html
<div date-picker timezone="Asia/Hong_Kong"></div>
```


### Maximum / minimum dates:

* These attributes restrict the dates that can be selected. 
* These work differently from the original `min-date` and `max-date` attributes, which they replace. 
* The original attributes allow selecting any dates and just mark the input as invalid. 
* With these attributes, if a date in the picker is outside of the valid range, then it will not be selectable.

##### Minimum date:

```html
<input date-time min-date="minDate">
```

##### Maximum date:

```html
<input date-time max-date="maxDate">
```

##### Minimum and maximum date:

```html
<input date-time min-date="minDate" max-date="maxDate">
```

### Date format (for input fields):

* A custom format for a date can be assigned via the `format` atribute.
  * This format will be used to display the date on an input field.
  * If not provided, a default format will be used.
  * See: [format options](http://momentjs.com/docs/#/displaying/format/)

```html
<input date-time format="yyyy-MM-dd HH:mm">
```


### Callback on date change

* Adding a `date-change` attribute containing a function name will cause this function to be called when the date changes in the picker.

```html
<input date-time date-change="changeDate">
```

### Update events

* An event can be broadcast from the parent scope which will update specific pickers with new settings. The settings which can be changed are:
  * `minDate`: Earliest selectable date for this picker. Disabled if this value is falsy.
  * `maxDate`: Latest selectable date for this picker. Disabled if this value is falsy.
  * `minView`: Minimum zoom level for date/time selection. Disabled if this value is falsy.
  * `maxView`: Maximum zoom level for date/time selection. Disabled if this value is falsy.
  * `view`: Default zoom level for date/time selection. Set to default value if this value is falsy.
  * `format`: Format string used to display dates on the input field. Set to default value if this value is falsy. 
    * See: [format options](http://momentjs.com/docs/#/displaying/format/)
	* This option cannot be used on the `date-picker` directive directly, it must be used on a `date-time` input field.
* The possible for the `view`, `minView` and `maxView` fields are:
  * `year`, `month`, `date`, `hours`, `minutes`.
* The event is targeted at specific pickers using their `ID` attributes.
  * If a picker exists with the same `ID` then the information in this picker will be updated.
  * A single `ID` can be used, or an array of `ID`s

#### Create picker with ID

```html
<input date-time id="pickerToUpdate">
```

#### Update one picker.

```javascript
$scope.$broadcast('pickerUpdate', 'pickerToUpdate', {
	format: 'D MMM YYYY HH:mm',
	maxDate: maxSelectableDate, //A moment object, date object, or date/time string parsable by momentjs
	minView: 'hours',
	view: false //Use default
});
```

#### Update multiple pickers.

```javascript
$scope.$broadcast('pickerUpdate', ['pickerToUpdate', 'secondPickerToUpdate'], {
	format: 'lll'
});
```
