# UAU-Componentes

#### Materiais de apoio

-  [Vídeo aula sobre AngularJS](https://www.youtube.com/watch?v=_y7rKxqPoyg&list=PLQCmSnNFVYnTD5p2fR4EXmtlR6jQJMbPb)
-  [Exemplo prático UAU-Componente](http://globaltec.uau.com.br/sites/Globaltec/)

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
<!--na página-->
<div ng-app="meuApp">
...
...
</div>
```

Utilize a diretiva `uau-componente` na sua página juntamente com o input que irá armazenar os dados quando selecionado:

```html
<!--na página-->
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
* `controle` — Nome do controle criado para o módulo (obrigatorio).
* `modelo` — Nome do scope da diretiva. Vincula o dado selecionado ao input do uau-componente (obrigatorio).
* `titulo` — Texto padrão exibido no componente, semelhante ao placeholder do html.
* `webservice` — Endereço do webservice que retornará os dados para o uau-componente  (deve retornar JSON).
* `parametros` — Nome dos parâmetros que compõe o webservice. Caso seja mais de um, separar por `;`.
	* exemplo de como ficaria a url: `/api/Exemplo/ConsultarMeuExemplo?cliente_cod=xxx&status_cli=yyy`
* `campo` — Define o nome da propriedade que irá ser visualizada no uau-componente. Normalmente um campo retornado da consulta do webservice.
* `evento` — Cria um evento com o nome informado no atributo, que será disparado ao selecionar algum item do componente. Pode ser utilizado para carregar os dados de um componente a partir da seleção de outro componente.
	* O evento pode ser recebido nos controllers da sua aplicação. Por exemplo: evento = "SELECIONAROBRA"
		O evento disparado poderá ser recebido da seguinte forma:
		
		```javascript
		//no controller
		$rootScope.$on('SELECIONAROBRA', function (event, data) {
			...
			//data, contém o valor da seleção
			...
		});
		```
		
* `disable` — true/false. Desabilita/Habilita o componente. O padrão é false (habilitado).
* `usegrid` — true/false. Cria um grid vinculado ao componente. As especificações e propriedades do grid podem ser criadas no controller do seu componente, [clique aqui para detalhes.](https://github.com/angular-ui/ui-grid/wiki).
* `min-caracteres-request` — Quantidade de caracteres requeridos antes que seja realizada uma requisição no webservice. O padrão é 3 caracteres. `Caso necessite que os dados sejam carregados ao carregar a página, infome a quantidade 0`.
* `delay-request` — Tempo em milisegundos após a digitação para que seja realizada a requisição no webservice. O padrão é 500
* `useviewbag` — true/false. O padrão é false. Utilize SOMENTE para recuperar os dados selecionados em outra página. Todos os casos de utilização desse atributo serão analisado pela QS.
	* Salva a seleção do componente para ser recupedara por outra página. Após ser recuperado, os dados são destruídos.
	* Para obter o dado utilize sessao.get("seuModelo"), onde `seuModelo` é o modelo do componente (atributo modelo acima).

### Evento `refresh`

O UAU-Componente realizam um `refresh` dos dados em duas situações:
* Quando são carregados com a página, bastando informar o atributo `min-caracteres-request` igual a `0`
* Quando é realizado filtro de busca no componente:
	* para receber o evento que dispara ao digitar no componente (obedecendo os atributos `min-caracteres-request` e `delay-request`) é necessário acrescentar no controller a função escopo `$scope.buscarItemNOMEDOMODELO`. Por exemplo: modelo = "empreendimento"
	
	```javascript
	//No controller
	$scope.buscarItemempreendimento = function (filtroItem) {
		//filtroItem contém o texto digitado no componente
		//Caso precise passar mais de um parâmetro para o webservice, os mesmos devem ser adicionados em um array.
		
		var valores = [];
		 
        	valores.push(filtroItem);		 
		valores.push($scope.empresa_cod);
		valores.push($scope.obra_obr);
		
		//a url do webservice será montada de acordo com os parâmetros informados
		//no atributo parametros (veja acima) e dos valores informados no array "valores"
		//e automaticamente recarregar a combobox com os dados retornados da consulta
		
		$scope.PreencherComponente(valores, "empreendimento");
	};
	```
	
