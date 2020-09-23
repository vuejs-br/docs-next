# Básico sobre Componentes

## Exemplo Base

Aqui está um exemplo de um componente Vue:

```js
// Criando uma aplicação Vue
const app = Vue.createApp({})

// Definindo um novo componente global chamado button-counter
app.component('button-counter', {
  data() {
    return {
      count: 0
    }
  },
  template: `
    <button @click="count++">
      Você me clicou {{ count }} vezes.
    </button>`
})
```

::: info
Estamos mostrando um exemplo simples aqui, mas em uma típica aplicação Vue, usamos Componentes Single File em vez de _template strings_. Você pode encontrar mais informações sobre eles [nesta seção](single-file-component.html).
:::

Componentes são instâncias reutilizáveis ​​com um nome: neste caso, `<button-counter>`. Podemos usar este componente como um elemento personalizado dentro de uma instância raiz:

```html
<div id="components-demo">
  <button-counter></button-counter>
</div>
```

```js
app.mount('#components-demo')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="Vue" data-slug-hash="abORVEJ" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Básico sobre Componentes">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/yLOGZKE">
  Básico sobre Componentes</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Uma vez que componentes são instâncias reutilizáveis, eles aceitam as mesmas opções que a instância raiz, como `data`, `computed`, `watch`, `methods` e gatilhos de ciclo de vida. As únicas exceções são as poucas opções específicas da raiz, como `el`.

## Reutilizando Componentes

Componentes podem ser reutilizados quantas vezes você quiser:

```html
<div id="components-demo">
  <button-counter></button-counter>
  <button-counter></button-counter>
  <button-counter></button-counter>
</div>
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="Vue" data-slug-hash="rNVqYvM" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Básico sobre Componentes: Reutilizando Componentes">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/GRZPzBN">
  Básico sobre Componentes: Reutilizando Componentes</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Perceba que ao clicar nos botões, cada um mantêm seu próprio e único `count`. Isso acontece porque cada vez que você usa um componente, uma nova **instância** dele é criada.

## Organizando Componentes

É comum que um aplicativo seja organizado em uma árvore de componentes aninhados:

![Árvore de Componentes](/images/components.png)

Por exemplo, você pode ter componentes para o cabeçalho, barra lateral e área de conteúdo, cada um normalmente contendo outros componentes para navegação, como links, postagens de _blog_, etc.

Para usar esses componentes em _templates_, eles devem ser registrados para que o Vue saiba deles. Há dois tipos de registro de componentes, sendo eles: **global** e **local**. Até agora, nós apenas registramos componentes globalmente, usando o método `component` do app criado:

```js
const app = Vue.createApp({})

app.component('my-component-name', {
  // ... opções ...
})
```

Componentes registrados globalmente podem ser usados ​​no _template_ da instância do `app` criado posteriormente - e até mesmo dentro de todos os subcomponentes da árvore de componentes da instância raiz.

Isso é tudo que você precisa saber sobre registro por hora, mas assim que terminar de ler esta página e se sentir confortável com seu conteúdo, recomendamos voltar mais tarde para ler o guia completo sobre [Registro de Componentes](component-registration.md).

## Passando Dados aos Filhos com Propriedades

Anteriormente, mencionamos a criação de um componente para postagens de _blog_. O problema é que esse componente não será útil a menos que você possa passar dados para ele, como o título e o conteúdo da postagem específica que queremos exibir. É aí que entram as propriedades (comumente chamadas apenas de _props_, já que este é o termo abreviado adotado pelo Vue).

Propriedades são atributos personalizados que você pode registrar em um componente. Quando um valor é passado para uma propriedade de atributo, ele se torna uma propriedade naquela instância do componente. Para passar um título para o componente de postagem do nosso _blog_, podemos incluí-lo na lista de propriedades que este componente aceita, usando a opção `props`:

```js
const app = Vue.createApp({})

app.component('blog-post', {
  props: ['title'],
  template: `<h4>{{ title }}</h4>`
})

app.mount('#blog-post-demo')
```

Um componente pode ter quantas propriedades você quiser e, por padrão, qualquer valor pode ser passado para qualquer propriedade. No modelo acima, você verá que podemos acessar esse valor na instância do componente, assim como acontece com `data`.

Uma vez que uma propriedade é registrada, você pode passar dados para ela como um atributo personalizado, dessa forma:

```html
<div id="blog-post-demo" class="demo">
  <blog-post title="Minha jornada com Vue"></blog-post>
  <blog-post title="Escrevendo sobre o Vue"></blog-post>
  <blog-post title="Porquê Vue é tão divertido"></blog-post>
</div>
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="Vue" data-slug-hash="PoqyOaX" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Básico sobre Componentes: Usando props">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/wvGRNbP">
  Básico sobre Componentes: Usando props</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Em um aplicação comum, no entanto, você provavelmente terá uma série de postagens em `data`:

```js
const App = {
  data() {
    return {
      posts: [
        { id: 1, title: 'Minha jornada com Vue' },
        { id: 2, title: 'Escrevendo sobre o Vue' },
        { id: 3, title: 'Porquê Vue é tão divertido' }
      ]
    }
  }
}

const app = Vue.createApp(App)

app.component('blog-post', {
  props: ['title'],
  template: `<h4>{{ title }}</h4>`
})

app.mount('#blog-posts-demo')
```

E, em seguida, poderá querer renderizar um componente para cada uma delas:

```html
<div id="blog-posts-demo">
  <blog-post
    v-for="post in posts"
    :key="post.id"
    :title="post.title"
  ></blog-post>
</div>
```

Acima, você viu que podemos usar `v-bind` para passar propriedades dinamicamente. Isso é especialmente útil quando você não sabe o conteúdo exato que irá renderizar com antecedência.

Por enquanto, isso é tudo que você precisa saber sobre propriedades, mas assim que terminar de ler esta página e se sentir confortável com seu conteúdo, recomendamos voltar mais tarde para ler o guia completo sobre [Propriedades](component-props.html).

## Escutando Eventos dos Filhos

Conforme desenvolvemos nosso componente `<blog-post>`, alguns recursos podem exigir comunicação de volta com o componente pai. Por exemplo, podemos decidir incluir um recurso de acessibilidade para ampliar o texto das postagens do _blog_, deixando o resto da página com o tamanho padrão.

No componente pai, podemos oferecer suporte a esse recurso adicionando uma propriedade `postFontSize`:

```js
const App = {
  data() {
    return {
      posts: [
        /* ... */
      ],
      postFontSize: 1
    }
  }
}
```

A qual poderia ser usada no _template_ para controlar o tamanho da fonte de todas as postagens do _blog_:

```html
<div id="blog-posts-events-demo">
  <div v-bind:style="{ fontSize: postFontSize + 'em' }">
    <blog-post v-for="post in posts" :key="post.id" :title="title"></blog-post>
  </div>
</div>
```

Agora, vamos adicionar um botão para ampliar o texto após o título de cada postagem:

```js
app.component('blog-post', {
  props: ['title'],
  template: `
    <div class="blog-post">
      <h4>{{ title }}</h4>
      <button>
        Aumentar texto
      </button>
    </div>
  `
})
```

O problema é que este botão não executa nada:

```html
<button>
  Aumentar texto
</button>
```

Ao clicar no botão, precisamos comunicar ao componente pai que deve ampliar o texto de todas as postagens. Felizmente, as instâncias de componente fornecem um sistema de eventos personalizados para resolver esse problema. O componente pai pode escolher ouvir qualquer evento na instância do componente filho com `v-on` ou `@`, assim como faríamos com um evento DOM nativo:

```html
<blog-post ... @enlarge-text="postFontSize += 0.1"></blog-post>
```

Então, o componente filho pode emitir um evento por si próprio chamando o [método **`$emit`**](../api/instance-methods.html#emit), passando o nome do evento que o pai poderá escutar:

```html
<button @click="$emit('enlarge-text')">
  Aumentar texto
</button>
```

Graças à escuta `v-on:enlarge-text="postFontSize += 0.1"`, o componente pai receberá o evento e atualizará o valor de `postFontSize`.

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="Vue" data-slug-hash="KKpGyrp" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Básico sobre Componentes: Emitindo Eventos">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/jOqXJEv">
  Básico sobre Componentes: Emitindo Eventos</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Para sermos mais explícitos, podemos listar os eventos emitidos na opção `emits` do componente.

```js
app.component('blog-post', {
  props: ['title'],
  emits: ['enlarge-text']
})
```

Isso permitirá que você verifique todos os eventos emitidos pelo componente e, opcionalmente, [validá-los](component-custom-events.html#validate-emitted-events).

### Emitindo um Valor com um Evento

Às vezes é útil emitir um valor específico com um evento. Por exemplo, nós talvez queiramos que o componente `<blog-post>` seja responsável por definir de quanto em quanto aumentar a fonte. Nesses casos, podemos usar um segundo parâmetro no método `$emit` para prover tal valor:

```html
<button @click="$emit('enlarge-text', 0.1)">
  Aumentar texto
</button>
```

Então, quando escutarmos o evento no componente pai, podemos acessar o valor emitido com `$event`:

```html
<blog-post ... @enlarge-text="postFontSize += $event"></blog-post>
```

Ou, se o manipulador de eventos for um método:

```html
<blog-post ... @enlarge-text="onEnlargeText"></blog-post>
```

Então o valor será passado como o primeiro parâmetro desse método:

```js
methods: {
  onEnlargeText(enlargeAmount) {
    this.postFontSize += enlargeAmount
  }
}
```

### Usando `v-model` em Componentes

Eventos personalizados podem também ser usados para criar _inputs_ personalizados que funcionam com `v-model`. Lembre-se que:

```html
<input v-model="searchText" />
```

Tem a mesma funcionalidade que:

```html
<input :value="searchText" @input="searchText = $event.target.value" />
```

No entanto, quando usado em um componente, `v-model` faria isso:

```html
<custom-input
  :model-value="searchText"
  @update:model-value="searchText = $event"
></custom-input>
```

::: warning
Observe que usamos `model-value` com _kebab-case_ aqui porque estamos trabalhando com um _template_ diretamente no DOM. Você pode encontrar uma explicação detalhada sobre os atributos _kebab-cased_ vs. _camelCased_ na seção [Ressalvas na Análise do _template_ DOM](#ressalvas-na-analise-do-template-dom)
:::

Para realmente funcionar, o `<input>` dentro do componente precisa:

- Vincular o atributo `value` com a propriedade `modelValue`
- No `input`, emitir um evento `update:modelValue` com o novo valor

Então, aqui está isso em ação:

```js
app.component('custom-input', {
  props: ['modelValue'],
  template: `
    <input
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
    >
  `
})
```

Agora o `v-model` deve funcionar perfeitamente com esse componente:

```html
<custom-input v-model="searchText"></custom-input>
```

Outra maneira de se habilitar a funcionalidade do `v-model` em um componente personalizado é usar a o recurso dos dados computados em definir um _getter_ e _setter_ separadamente.

No exemplo a seguir, nós refatoramos o componente de `custom-input` usando um dado computado.

Lembre-se de que o método `get` deve retornar a propriedade `modelValue` (ou qualquer outro nome de propriedade que esteja sendo usado para vinculação), e o método `set` deve disparar o `$emit` correspondente para essa propriedade ser alterada.

```js
app.component('custom-input', {
  props: ['modelValue'],
  template: `
    <input v-model="value">
  `,
  computed: {
    value: {
      get() {
        return this.modelValue
      },
      set(value) {
        this.$emit('update:modelValue', value)
      }
    }
  }
})
```

Isso é tudo que você precisa saber sobre eventos personalizados em componentes por hora, mas assim que você terminar de ler essa página e se sentir confortável com o conteúdo, recomendamos retornar mais tarde para ler o guia completo de [Eventos Personalizados](component-custom-events.md).

## Distribuição de Conteúdo com _Slots_

Assim como em elementos HTML, muitas vezes é útil ser capaz de passar conteúdo dentro de um componente, dessa forma:

```html
<alert-box>
  Algo ruim aconteceu.
</alert-box>
```

Que renderizaria algo assim:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="jOPeaob" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Básico sobre Componentes: slots">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/WNwLmJP">
  Básico sobre Componentes: slots</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Felizmente, isso é facilmente realizado com um elemento `<slot>`:

```js
app.component('alert-box', {
  template: `
    <div class="demo-alert-box">
      <strong>Erro!</strong>
      <slot></slot>
    </div>
  `
})
```

Como você viu acima, nós só adicionamos o _slot_ para aonde queremos que o conteúdo vá – e é isso!

Isso é tudo que você precisa saber sobre _slots_ por hora, mas assim que você terminar de ler essa página e se sentir confortável com o conteúdo, recomendamos retornar mais tarde para ler o guia completo sobre [_Slots_](component-slots.md).

## Componentes Dinâmicos

Às vezes, é útil alternar dinamicamente entre componentes, como em uma interface de abas:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="oNXaoKy" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Básico sobre Componentes: Componentes Dinâmicos">
  <span>Veja o exemplo <a href="https://codepen.io/vuejs-br/pen/ZEWVPqY">
  Básico sobre Componentes: Componentes Dinâmicos</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

O exemplo acima é possível por causa do elemento `<component>` com o atributo especial `is`:

```html
<!-- O componente atualiza quando currentTabComponent muda -->
<component :is="currentTabComponent"></component>
```

No exemplo acima, `currentTabComponent` pode conter:

- o nome do componente registrado, ou
- o objeto de opções de inicialização um componente

Veja [esse exemplo](https://codepen.io/vuejs-br/pen/ZEWVPqY) para experimentar com este código por sua conta, ou veja [essa versão](https://codepen.io/vuejs-br/pen/ZEWVPZb) para um exemplo vinculando ao objeto de opções de inicialização de um componente, ao invés de vincular ao seu nome registrado.

Tenha em mente que esse atributo pode ser utilizado com elementos HTML comuns, porém, eles serão tratados como componentes, o que significa que todos os atributos **serão vinculados como atributos do DOM**. Para algumas propriedades como `value` funcionarem da maneira esperada, você terá que vinculá-las usando o [modificador `.prop`](../api/directives.html#v-bind).

Isso é tudo que você precisa saber sobre componentes dinâmicos por hora mas, assim que você terminar de ler essa página e se sentir confortável com o conteúdo, recomendamos retornar mais tarde para ler o guia completo sobre [Componentes Dinâmicos & Assíncronos](./component-dynamic-async.html).

## Ressalvas na Análise do _template_ DOM

Alguns elementos HTML, como `<ul>`, `<ol>`, `<table>` e `<select>` têm restrições do que pode aparecer dentro deles, e alguns elementos como `<li>`, `<tr>`, e `<option>` podem aparecer apenas dentro de certos elementos.

Isso nos leva a problemas quando usamos componentes com elementos que tem tais restrições. Por exemplo:

```html
<table>
  <blog-post-row></blog-post-row>
</table>
```

O componente `<blog-post-row>` será removido como um conteúdo inválido, causando erros na eventual renderização. Felizmente, podemos usar a diretiva especial `is` como uma forma de contornar o problema:

```html
<table>
  <tr v-is="'blog-post-row'"></tr>
</table>
```

:::warning
O valor de `v-is` deve ser uma String literal válida no JavaScript:

```html
<!-- Incorreto, nada será renderizado -->
<tr v-is="blog-post-row"></tr>

<!-- Correto -->
<tr v-is="'blog-post-row'"></tr>
```
:::

Além disso, atributos HTML são insensíveis à notação de maiúsculas/minúsculas (_case-insensitive_), então os navegadores interpretarão quaisquer caracteres maiúsculos como minúsculos. Isso significa que, quando você está usando _templates_ diretamente no DOM, nomes de propriedades _camelCased_ e parâmetros de manipuladores de eventos precisarão usar seus equivalentes _kebab-cased_ (ou seja, em minúsculas e delimitados por hífen):

```js
// camelCase em JavaScript
app.component('blog-post', {
  props: ['postTitle'],
  template: `
    <h3>{{ postTitle }}</h3>
  `
})
```

```html
<!-- kebab-case em HTML -->
<blog-post post-title="hello!"></blog-post>
```

Deve-se observar que **essas limitações _não_ se aplicam se você estiver usando _templates_ baseados em Strings, de uma das seguintes fontes**:

- _templates_ diretamente usando Strings (por exemplo, `template: '...'`)
- [Componentes Single File (`.vue`)](single-file-component.html)
- `<script type="text/x-template">`

Isso é tudo que você precisa saber sobre componentes dinâmicos por hora – na verdade, esse é o fim da seção _Essenciais_ do Vue. Parabéns! Ainda há mais a ser aprendido, mas antes, recomendamos que tire um tempo para brincar com o Vue por conta própria e criar alguma coisa divertida.

Assim que você se sentir confortável com o conteúdo que vimos, recomendamos retornar mais tarde para ler o guia completo de [Componentes Dinâmicos & Assíncronos](component-dynamic-async.html), como também as outras páginas na seção Componentes em Detalhes da barra lateral.
