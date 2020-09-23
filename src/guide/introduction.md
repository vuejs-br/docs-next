# Introdução

::: tip NOTE
Já conhece o Vue 2 e só quer saber o que há de novo no Vue 3? Confira o [Gia de Migração](/guide/migration/introduction.html)!
:::

## O que é Vue.js?

Vue (pronuncia-se /vjuː/, como **view**, em inglês) é um **framework progressivo** para a construção de interfaces de usuário. Ao contrário de outros _frameworks_ monolíticos, Vue foi projetado desde sua concepção para ser adotável incrementalmente. A biblioteca principal é focada exclusivamente na camada visual (_view layer_), sendo fácil adotar e integrar com outras bibliotecas ou projetos existentes. Por outro lado, Vue também é perfeitamente capaz de dar poder a sofisticadas _Single-Page Applications_ quando usado em conjunto com [ferramentas modernas](../guide/single-file-component.html) e [bibliotecas de apoio](https://github.com/vuejs/awesome-vue#components--libraries).

Se você gostaria de saber mais sobre Vue antes de mergulhar nele, nós <a id="modal-player" class="vuemastery-trigger" href="#">criamos um vídeo</a> passeando pelos princípios básicos e um exemplo de projeto.

<common-vuemastery-video-modal/>

## Primeiros Passos

<p>
  <ActionLink class="primary" url="installation.html">
    Instalação
  </ActionLink>
</p>

::: tip
O guia oficial supõe um nível intermediário em HTML, CSS e JavaScript. Se você é totalmente novo no mundo do _frontend_, mergulhar diretamente em um _framework_ pode não ser a melhor ideia para começar - compreenda primeiro o básico e depois volte! Experiência anterior com outros _frameworks_ ajuda, mas não é obrigatória.
:::

A forma mais simples de testar o Vue é usando o [exemplo de Olá Mundo](https://codepen.io/vuejs-br/pen/yLOGGvV). Sinta-se à vontade para abrí-lo em outra aba e acompanhar conosco durante os próximos exemplos básicos.

A página [Instalação](installation.md) deste guia oferece mais opções para instalar o Vue. Nota: nós **não** recomendamos que iniciantes comecem com o `vue-cli`, especialmente se você não está familiarizado com ferramentas de _build_ baseadas em Node.js.

## Renderização Declarativa

No núcleo do Vue está um sistema que nos permite declarativamente renderizar dados no DOM (_Document Object Model_) usando uma sintaxe de _template_ simples:

```html
<div id="counter">
  Contador: {{ counter }}
</div>
```

```js
const Counter = {
  data() {
    return {
      counter: 0
    }
  }
}

Vue.createApp(Counter).mount('#counter')
```

Acabamos de criar nosso primeiro aplicativo Vue! Isso parece muito similar a renderizar uma _template string_, mas o Vue fez bastante trabalho interno. Os dados e o DOM estão agora interligados e tudo se tornou **reativo**. Como podemos ter certeza? Dê uma olhada no exemplo a seguir, onde a propriedade `counter` incrementa a cada segundo, e você verá como o DOM renderizado muda:

```js{8-10}
const CounterApp = {
  data() {
    return {
      counter: 0
    }
  },
  mounted() {
    setInterval(() => {
      this.counter++
    }, 1000)
  }
}
```

<FirstExample />

Além de simples interpolação de texto, podemos interligar atributos de elementos:

```html
<div id="bind-attribute">
  <span v-bind:title="message">
    Pare o mouse sobre mim e veja a dica interligada dinamicamente!
  </span>
</div>
```

```js
const AttributeBinding = {
  data() {
    return {
      message: 'Você carregou esta página em ' + new Date().toLocaleString()
    }
  }
}

Vue.createApp(AttributeBinding).mount('#bind-attribute')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="KKpRVvJ" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Interligação dinâmica de atributos">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/BaKqxbR">
  Interligação dinâmica de atributos</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Aqui nos deparamos com algo novo. O atributo `v-bind` que você está vendo é chamado de **diretiva**. Diretivas são prefixadas com `v-` para indicar que são atributos especiais providos pelo Vue, e como você deve ter percebido, aplicam comportamento especial de reatividade ao DOM renderizado. Neste caso, basicamente estamos dizendo: "_mantenha o atributo `title` do elemento sempre atualizado em relação à propriedade `message` da instância atualmente ativa_".

## Tratando Interação do Usuário

Para permitir aos usuários interagir com o aplicativo, podemos usar a diretiva `v-on` para anexar escutas a eventos (_event listeners_) que invocam métodos em nossas instâncias:

```html
<div id="event-handling">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Inverter Mensagem</button>
</div>
```

```js
const EventHandling = {
  data() {
    return {
      message: 'Olá Vue.js!'
    }
  },
  methods: {
    reverseMessage() {
      this.message = this.message
        .split('')
        .reverse()
        .join('')
    }
  }
}

Vue.createApp(EventHandling).mount('#event-handling')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="dyoeGjW" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Escutas em eventos">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/LYNgmKY">
  Escutas em eventos</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>


Observe que neste método atualizamos o estado da aplicação sem tocar no DOM - todas as manipulações são tratadas pelo Vue e o código que você escreve é focado na lógica de manipulação de dados.

Vue também provê a diretiva `v-model`, que torna a interligação de mão dupla (_two-way binding_) entre a caixa de texto e o estado da aplicação uma moleza:

```html
<div id="two-way-binding">
  <p>{{ message }}</p>
  <input v-model="message" />
</div>
```

```js
const TwoWayBinding = {
  data() {
    return {
      message: 'Olá Vue.js!'
    }
  }
}

Vue.createApp(TwoWayBinding).mount('#two-way-binding')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="poJVgZm" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Interligação de mão dupla">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/JjXmvQg">
  Interligação de mão dupla</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Condicionais e Repetições

Também é fácil alternar a presença de um elemento:

```html
<div id="conditional-rendering">
  <span v-if="seen">Agora você me viu</span>
</div>
```

```js
const ConditionalRendering = {
  data() {
    return {
      seen: true
    }
  }
}

Vue.createApp(ConditionalRendering).mount('#conditional-rendering')
```

Este exemplo demonstra que nós podemos interligar dados não apenas ao texto e aos atributos, mas também à **estrutura** do DOM. Mais do que isso, Vue também provê um poderoso sistema de transições que pode automaticamente aplicar [efeitos de transição](transitions-enterleave.md) quando elementos são inseridos/atualizados/removidos pelo Vue.

Você pode alterar `seen` de `true` para `false` no exemplo abaixo para ver em ação:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="Vue" data-slug-hash="oNXdbpB" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Renderização condicional">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/yLOGzVK">
  Renderização condicional</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Existem mais algumas diretivas, cada uma com sua própria funcionalidade. Por exemplo, a diretiva `v-for` pode ser usada para exibir uma lista de itens usando dados de um Array:

```html
<div id="list-rendering">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```

```js
const ListRendering = {
  data() {
    return {
      todos: [
        { text: 'Aprender JavaScript' },
        { text: 'Aprender Vue' },
        { text: 'Criar algo incrível' }
      ]
    }
  }
}

Vue.createApp(ListRendering).mount('#list-rendering')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="mdJLVXq" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Renderização de listas">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/GRZPMrZ">
  Renderização de listas</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Composição com Componentes

O sistema de componentes também é outro importante conceito no Vue, por ser uma abstração que proporciona a construção de aplicações de larga escala compostas por pequenos componentes, auto-contidos e frequentemente reutilizáveis. Se nós pensarmos sobre isso, quase qualquer tipo de interface de uma aplicação pode ser abstraída em uma árvore de componentes:

![Árvore de Componentes](/images/components.png)

No Vue, um componente é essencialmente uma instância com opções pré-definidas. Registrar um componente no Vue é trivial: criamos um objeto para o componente assim como fizemos com os objetos `App` e o utilizamos na opção `components` de seu elemento pai:

```js
// Cria uma aplicação Vue
const app = Vue.createApp(...)

// Define um novo componente chamado todo-item
app.component('todo-item', {
  template: `<li>Isto é uma tarefa</li>`
})

// Monta a aplicação Vue
app.mount(...)
```

Agora você pode compor com ele no _template_ de outro componente:

```html
<ol>
  <!-- Cria uma instância do componente todo-item -->
  <todo-item></todo-item>
</ol>
```

Mas isto renderizaria o mesmo texto toda vez que um item fosse utilizado, o que não é lá muito interessante. Devemos poder passar os dados do escopo superior (_parent_) para os componentes filhos. Vamos modificar o componente para fazê-lo aceitar uma [prop](component-basics.html#passing-data-to-child-components-with-props):

```js
app.component('todo-item', {
  props: ['todo'],
  template: `<li>{{ todo.text }}</li>`
})
```

Agora podemos passar o dado `todo` em cada repetição de componente usando `v-bind`:

```html
<div id="todo-list-app">
  <ol>
    <!--
      Agora provemos cada todo-item com o objeto todo que ele
      representa, de forma que seu conteúdo possa ser dinâmico.
      Também precisamos prover cada componente com uma "chave",
      a qual será explicada posteriormente.
    -->
    <todo-item
      v-for="item in groceryList"
      v-bind:todo="item"
      v-bind:key="item.id"
    ></todo-item>
  </ol>
</div>
```

```js
const TodoList = {
  data() {
    return {
      groceryList: [
        { id: 0, text: 'Vegetais' },
        { id: 1, text: 'Queijo' },
        { id: 2, text: 'Qualquer outra coisa que humanos podem comer' }
      ]
    }
  }
}

const app = Vue.createApp(TodoList)

app.component('todo-item', {
  props: ['todo'],
  template: `<li>{{ todo.text }}</li>`
})

app.mount('#todo-list-app')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="VwLxeEz" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Introdução a componentes">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/gOrZZXJ">
  Introdução a componentes</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Este é um exemplo fictício, mas conseguimos separar nossa aplicação em duas pequenas unidades, sendo que o componente filho está razoavelmente bem desacoplado do componente pai graças à funcionalidade de `props`. Podemos agora melhorar nosso componente `<todo-item>` com _template_ e lógica mais complexos, sem afetar o restante.

Em uma aplicação grande, é essencial dividir todo o aplicativo em componentes para tornar o desenvolvimento gerenciável. Falaremos mais sobre componentes [futuramente neste guia](component-basics.md), mas aqui está um exemplo (imaginário) da aparência que o _template_ de um aplicativo poderia ter com o uso de componentes:

```html
<div id="app">
  <app-nav></app-nav>
  <app-view>
    <app-sidebar></app-sidebar>
    <app-content></app-content>
  </app-view>
</div>
```

### Relação com Elementos Customizados

Você pode ter notado que componentes Vue são similares aos **Elementos Customizados**, parte da [Especificação de Web Components](https://www.w3.org/wiki/WebComponents/). Isto ocorre pois a sintaxe de componentes Vue foi vagamente modelada a partir da especificação. Por exemplo, implementamos a [Slot API](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md) e o atributo especial `is`. Entretanto, existem algumas diferenças fundamentais:

1. A Especificação de Web Components foi finalizada, mas não está nativamente implementada em todos os navegadores. O Safari 10.1+, Chrome 54+ e Firefox 63+ suportam nativamente os componentes da Web. Em comparação, componentes Vue funcionam consistentemente em todos os navegadores suportados (IE11 com o _build_ de compatibilidade e superiores). Quando necessário, componentes Vue também podem ser envolvidos dentro de um elemento customizado nativo.

[//]: # 'TODO: vincular a _build_ de compatibilidade'

2. Componentes Vue oferecem importantes recursos não disponíveis em elementos customizados tradicionais, mais notavelmente: fluxo de dados entre componentes, comunicação com eventos customizados e integração com ferramentas de _build_.

Embora o Vue não use elementos personalizados internamente, ele tem [ótima interoperabilidade](https://custom-elements-everywhere.com/#vue) quando se trata de consumir ou distribuir como elementos personalizados. O Vue CLI também suporta a criação de componentes Vue que se registram como elementos personalizados nativos.

## Pronto para Mais?

Nós introduzimos brevemente os recursos mais básicos do núcleo do Vue - o resto deste guia se aprofundará neles e em outros recursos avançados em um nível muito maior de detalhes, portanto certifique-se de ler tudo!
