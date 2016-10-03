# Exemplo prático UAU-Componentes

Nesse tutorial será apresentado a estrutura do UAU-Componentes, como adicionar os arquivos dependentes, a funcionalidade da tag <uau-componente> e como carregar e recuperar dados no componente.

#### Materiais de apoio

-  [Vídeo aula sobre AngularJS](https://www.youtube.com/watch?v=_y7rKxqPoyg&list=PLQCmSnNFVYnTD5p2fR4EXmtlR6jQJMbPb)
-  [Documentação UAU-Componente](https://github.com/diogoucg/document/blob/master/README.md)

## Estrutura
![Estrutura do UAU-Componente](https://github.com/diogoucg/document/blob/master/estrutura.png)
* diretivas: diretivas personalizadas
* lib: todas as bibliotecas usadas pelo uau-componente
* src
	* uau-common.js: factory's e filter's utilizadas no uau-componente
	* uau-componente.css: estilo dos templates do uau-componente
	* uau-componente.js: core do uau-componente

## Adicione as dependências

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

## Criação do módulo referente à pagina

Crie um arquivo *.js na mesma pasta de sua página e coloque o nome seguindo os conceitos do MVCUAU.
![Estrutura do UAU-Componente](https://github.com/diogoucg/document/blob/master/estrutura2.png)

Por exemplo: `CadastroAtendimento.js`

No arquivo .js, adicione o módulo e a depedência do `uau-componente`.
Crie um nome para seu módulo que referencie a página. Por exemplo: `cadastroatendimento`

```javascript
angular.module('cadastroatendimento', ['uau-componente'])
```

Em seguida crie um `controller` para seu módulo.

```javascript
.controller('ctrlCadastroatendimento', function ($scope) {
...
});
```

Voltando para a página html/aspx, referencie o módulo `cadastroatendimento` adicionando a diretiva `ng-app` na sua página.
```html
<!--na página-->
<div ng-app="cadastroatendimento">
...
`aqui está todo o corpo de sua página`
...
</div>
```
## Utilização da diretiva uau-componente

Utilize a diretiva `uau-componente` na sua página juntamente com o input que irá armazenar os dados quando selecionado:

```html
<!--na página-->
<input type="hidden" ng-model="empreendimento" value="{{empreendimento}}" id="txtEmpObr" runat="server" />
<uau-componente 
	controle="ctrlCadastroatendimento"
	modelo="empreendimento"
	titulo="Selecione o empreendimento"
	webservice="/api/EmpObra/ConsultarEmpresasObrasDoCliente"
	parametros="filtro"
	campo="empresa_cod;obra_cod"
	min-caracteres-request ="0">
</uau-componente>
```

* controle: Nome do controle criado em seu módulo.
* modelo: Nome do scopo da diretiva.
* titulo: Texto padrão exibido no componente, semelhante ao placeholder do html.
* webservice: Endereço do webservice que retornará os dados para o uau-componente  (o webservice deve retornar JSON).
* parametros — Nome dos parâmetros que compõe o webservice. Caso seja mais de um, separar por `;`.
* campo — Define o nome da propriedade que irá ser visualizada no uau-componente. Normalmente um campo retornado da consulta do webservice (Será exibido `1-00000`, código da empresa - código da obra).
* min-caracteres-request: Informando `0` os dados serão carregados ao carregar a página.

### Atualizando os dados no uau-componente

O UAU-Componente realiza um `refresh` dos dados em duas situações:
* Quando são carregados com a página, bastando informar o atributo `min-caracteres-request` igual a `0`
* Quando é realizado filtro de busca no componente:
	* para receber o evento que dispara ao digitar no componente (obedecendo os atributos `min-caracteres-request` e `delay-request`) é necessário acrescentar no controller a função escopo `$scope.buscarItemNOMEDOMODELO`. No nosso exemplo: modelo = "empreendimento"
	
	```javascript
	//Continuando o controller
	$scope.buscarItemempreendimento = function (filtroItem) {
		//filtroItem contém o texto digitado no componente
		
		//repare que é necessário passar o nome do modelo na função `PreencherComponente`
		$scope.PreencherComponente(filtroItem, "empreendimento");
	};
	```
## CadastroAtendimento.js completo

```javascript
angular.module('cadastroatendimento', ['uaucomponente'])

.controller('ctrlCadastroatendimento', function ($scope) {
	$scope.buscarItemempreendimento = function (filtroItem) {
		//filtroItem contém o texto digitado no componente
		
		//repare que é necessário passar o nome do modelo na função `PreencherComponente`
		$scope.PreencherComponente(filtroItem, "empreendimento");
	};
});
```
