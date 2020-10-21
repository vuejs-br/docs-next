# Sintaxe de Templates

O Vue.js utiliza uma sintaxe de _templates_ baseada em HTML que permite que você vincule declarativamente o DOM renderizado aos dados da instância do componente que existe por baixo. Todos os _templates_ do Vue.js são compostos por HTML válido que pode ser interpretado por navegadores compatíveis com as especificações e também por _parsers_ de HTML.

Internamente, o Vue compila os _templates_ em funções de renderização do Virtual DOM. Combinado com o sistema de reatividade, Vue é capaz de identificar de forma inteligente a menor quantidade possível de componentes a serem “re-renderizados” e aplica o mínimo possível de manipulações do DOM quando o estado da aplicação muda.

Se você é familiarizado com os conceitos de Virtual DOM e prefere o poder do JavaScript puro, você também pode escrever [funções de renderização](render-function.html) ao invés de _templates_, com suporte opcional ao JSX.

## Interpolações

### Texto

A forma mais básica de vinculação de dados é interpolação de texto utilizando a sintaxe _"Mustache"_ (chaves duplas):

```html
<span>Mensagem: {{ msg }}</span>
```

As chaves duplas serão substituídas pelo valor da propriedade `msg` da instância do componente correspondente. Esse texto também será atualizado sempre que a propriedade `msg` for modificada.

Você também pode realizar interpolações únicas que não são atualizadas quando os dados mudam através da [diretiva `v-once`](../api/directives.html#v-once), mas mantenha em mente que isso afetará qualquer vinculação realizada no mesmo nó:

```html
<span v-once>Este valor nunca será modificado: {{ msg }}</span>
```

### HTML Puro

As chaves duplas interpretam os dados como texto simples, não HTML. Para que você exiba HTML, utilize a [diretiva `v-html`](../api/directives.html#v-html):

```html
<p>Interpolação textual: {{ rawHtml }}</p>
<p>Diretiva v-html: <span v-html="rawHtml"></span></p>
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="yLNEJJM" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Renderizando v-html">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/MWyZLyQ">
  Renderizando v-html</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Os conteúdos do `span` serão substituídos pelo valor da propriedade `rawHtml`, interpretada como HTML puro - vínculos de dados são ignorados. Note que você não pode utilizar a diretiva `v-html` para compor templates parciais, porque o Vue não é uma _engine_ de _templates_ baseada em Strings. Ao invés disso, componentes são a maneira indicada como unidade fundamental de composição e reutilização de elementos de interface.

::: tip
Renderizar dinamicamente HTML arbitrário no seu _site_ pode ser muito perigoso, pois pode facilmente levar a [ataques XSS](https://pt.wikipedia.org/wiki/Cross-site_scripting). Utilize a interpolação de HTML apenas em conteúdos que você confia e **nunca** em conteúdos enviados por usuários.
:::

### Atributos

Chaves duplas não podem ser usadas em atributos HTML. Para isso, utilize a [diretiva `v-bind`](../api/directives.html#v-bind):

```html
<div v-bind:id="dynamicId"></div>
```

No caso de atributos _booleanos_, onde sua mera existência implica em `true`, `v-bind` funciona um pouco diferente. Neste exemplo:

```html
<button v-bind:disabled="isButtonDisabled">Botão</button>
```

Se `isButtonDisabled` possui um valor `null` ou `undefined`, o atributo `disabled` nem mesmo será incluído no elemento `<button>` renderizado.

### Usando Expressões JavaScript

Até aqui nós apenas vinculamos valores a chaves simples em nossos _templates_. Mas o Vue.js suporta todo o poder das expressões JavaScript dentro de todo tipo de vinculação de dados:

```html
{{ number + 1 }} {{ ok ? 'SIM' : 'NÃO' }} {{ message.split('').reverse().join('')
}}

<div v-bind:id="'list-' + id"></div>
```

Estas expressões serão compiladas como JavaScript no escopo de dados da atual instância ativa do Vue. A única restrição é que cada vínculo pode conter **apenas uma expressão** - desta forma, os códigos a seguir são exemplos de coisas que **NÃO** funcionarão:

```html
<!-- isso é uma atribuição, não uma expressão: -->
{{ var a = 1 }}

<!-- ifs também não funcionarão, use expressões ternárias -->
{{ if (ok) { return message } }}
```

## Diretivas

Diretivas são atributos especiais com o prefixo `v-`. Espera-se que os valores atribuídos às diretivas sejam **uma única expressão JavaScript** (com a exceção do `v-for` e do `v-on`, que serão discutidos posteriormente). O trabalho de uma diretiva é aplicar reativamente efeitos colaterais ao DOM quando o valor de sua expressão muda. Vamos revisar o exemplo que vimos na introdução:

```html
<p v-if="seen">Agora você me vê</p>
```

Aqui, a diretiva `v-if` irá remover/inserir o elemento `<p>` baseado na veracidade do valor da expressão `seen`.

### Parâmetros

Algumas diretivas podem aceitar um parâmetro, denotado pelo símbolo de dois pontos após o nome da diretiva. Por exemplo, a diretiva `v-bind` é utilizada para atualizar um atributo HTML reativamente:

```html
<a v-bind:href="url"> ... </a>
```

Aqui, `href` é o parâmetro que informará a diretiva `v-bind` para interligar o atributo `href` do elemento ao valor da expressão `url`.

Outro exemplo é a diretiva `v-on`, que observa eventos do DOM:

```html
<a v-on:click="doSomething"> ... </a>
```

Aqui, o argumento é o nome do evento do DOM que ela deverá escutar. Falaremos sobre gerenciamento de eventos em mais detalhes também.

### Argumentos Dinâmicos

Também é possível usar uma expressão JavaScript no argumento de uma diretiva envolvendo-a em colchetes:

```html
<!--
Note que existem algumas restrições à expressão do argumento, como explicado na seção
"Restrições da Expressão de Argumento Dinâmico" abaixo.
-->
<a v-bind:[attributeName]="url"> ... </a>
```

Aqui, `attributeName` será dinamicamente processado como uma expressão JavaScript, e seu valor processado será usado como o valor final para o argumento. Por exemplo, se a instância o seu componente possui uma propriedade de dados `attributeName` cujo valor é `"href"`, então essa ligação irá ser equivalente a `v-bind:href`.

Da mesma forma, você pode usar argumentos dinâmicos para vincular um manipulador (_handler_) a um nome de evento dinâmico:

```html
<a v-on:[eventName]="doSomething"> ... </a>
```

Neste exemplo, quando o valor de `eventName` for `"focus"`, por exemplo, `v-on:[eventName]` será equivalente a `v-on:focus`.

### Modificadores

Modificadores são sufixos especiais denotados por um ponto `.`, que indicam que a diretiva deve ser aplicada de alguma maneira especial. Por exemplo, o modificador `.prevent` diz para a diretiva `v-on` chamar `event.preventDefault()` no evento desencadeado:

```html
<form v-on:submit.prevent="onSubmit">...</form>
```

Você verá outros exemplos de modificadores posteriormente, [para a diretiva `v-on`](events.md#event-modifiers) e [para a diretiva `v-model`](forms.md#modifiers), quando explorarmos tais recursos.

## Abreviações

O prefixo `v-` serve como uma dica visual para identificar atributos específicos do Vue em seus _templates_. Isso é útil quando você está utilizando Vue.js para aplicar comportamentos dinâmicos a alguma marcação existente, mas pode ser verboso para algumas diretivas usadas com frequência. Ao mesmo tempo, a necessidade do prefixo `v-` se torna menos importante quando você está construindo uma [SPA](https://en.wikipedia.org/wiki/Single-page_application), onde o Vue gerencia todas os _templates_. Desta forma, o Vue fornece abreviações especiais para duas das diretivas mais usadas, `v-bind` e `v-on`:

### Abreviação de `v-bind`

```html
<!-- sintaxe completa -->
<a v-bind:href="url"> ... </a>

<!-- forma abreviada -->
<a :href="url"> ... </a>

<!-- forma abreviada com argumento dinâmico -->
<a :[key]="url"> ... </a>
```

### Abreviação de `v-on`

```html
<!-- sintaxe completa -->
<a v-on:click="doSomething"> ... </a>

<!-- forma abreviada -->
<a @click="doSomething"> ... </a>

<!-- forma abreviada com argumento dinâmico -->
<a @[event]="doSomething"> ... </a>
```

Eles podem parecer um pouco diferentes de HTML normal, mas `:` e `@` são caracteres válidos para nomes de atributos e todos os navegadores que suportam o Vue podem lê-los corretamente. Além do mais, eles não aparecem na marcação renderizada final. A sintaxe abreviada é totalmente opcional, mas você provavelmente vai preferir utilizá-la quando aprender mais sobre seu uso posteriormente.

> A partir da próxima página e em diante, iremos utilizar as formas abreviadas em nossos exemplos, já que é o uso mais comum para os desenvolvedores Vue.

### Ressalvas

#### Restrições de Valores de Argumentos Dinâmicos

Espera-se que argumentos dinâmicos sejam avaliados resultando-se em uma String, com exceção do `null`. O valor especial `null` pode ser usado para explicitamente remover um vínculo. Qualquer outro valor que não seja uma String acionará um aviso.

#### Restrições de Expressões de Argumentos Dinâmicos

Expressões de argumentos dinâmicos possuem algumas restrições de sintaxe por causa de determinados caracteres, como espaços e aspas, os quais são inválidos dentro de nomes de atributos HTML. Por exemplo, o seguinte é inválido:

```html
<!-- Isto irá provocar um aviso do compilador. -->
<a v-bind:['foo' + bar]="value"> ... </a>
```

Nós recomendamos trocar quaisquer expressões complexas por [dados computados](computed.html), uma das partes mais fundamentais do Vue, que cobriremos em breve.

Quando estiver usando _templates_ no DOM (_templates_ escritos diretamente no arquivo HTML), você também deve evitar nomear suas chaves de argumentos com caracteres maiúsculos, já que os navegadores forçarão os nomes dos atributos a ficarem em minúsculas:

```html
<!--
Isso será convertido em v-bind:[someattr] em templates no DOM.
A menos que você tenha uma propriedade "someattr" (totalmente em minúsculas) em sua instância, tal código não funcionará.
-->
<a v-bind:[someAttr]="value"> ... </a>
```

#### Expressões JavaScript

Expressões de _template_ são isoladas e possuem acesso apenas à uma [lista de variáveis globais permitidas](https://github.com/vuejs/vue-next/blob/master/packages/shared/src/globalsWhitelist.ts#L3), tais como `Math` e `Date`. Você não deve tentar acessar variáveis globais definidas pelo usuário em expressões de _template_.
