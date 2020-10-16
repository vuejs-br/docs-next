# Eventos Personalizados

> Esta página pressupõe que você já leu o [Básico sobre Componentes](component-basics.md). Leia isso antes se você está iniciando com componentes.

## Nomes de Eventos

Diferente de componentes e propriedades, os nomes de eventos não convertem automaticamente maiúsculas e minúsculas. Em vez disso, o nome de um evento emitido deve corresponder exatamente ao nome usado para ouvir este evento. Por exemplo, se emitir um evento com nome em camelCase:

```js
this.$emit('myEvent')
```

Ouvir com kebab-case não funcionará:

```html
<!-- Não funcionará -->
<my-component @my-event="doSomething"></my-component>
```

Considerando que os nomes de eventos nunca serão usados como nomes de variáveis ou de propriedades no JavaScript, não há razão para usar camelCase ou PascalCase. Além disso, os ouvintes de eventos `v-on` dentro dos templates DOM serão automaticamente transformados em minúsculas (devido ao HTML ser insensível a maiúsculas e minúsculas), então `@myEvent` se tornará `@myevent` -- fazendo `myEvent` impossível de ser ouvido.

Por estas razões, nós recomendamos que você **sempre use kebab-case para nomes de eventos**.

## Definindo Eventos Personalizados

<VideoLesson href="https://vueschool.io/lessons/defining-custom-events-emits?friend=vuejs" title="Aprenda a definir quais eventos um componente pode emitir com a Vue School (em inglês)">Assista a um vídeo grátis sobre como definir Eventos Personalizados na Vue School (em inglês)</VideoLesson>

Os eventos emitidos podem ser definidos no componente através da opção `emits`.

```js
app.component('custom-form', {
  emits: ['in-focus', 'submit']
})
```

Quando um evento nativo (por exemplo, `click`) for definido na opção `emits`, o evento do componente será uaado __ao invés__ de um escutador de evento nativo.

::: tip DICA
Recomenda-se definir todos os eventos emitidos para documentar melhor como o componente deve funcionar.
:::

### Validar Eventos Emitidos

Semelhante à validação de propriedades, um evento emitido pode ser validado se for definido com a sintaxe Object em vez da sintaxe Array.

Para adicionar validação, o evento recebe uma função que recebe os argumentos passados ​​para a chamada `$emit` e retorna um booleano para indicar se o evento é válido ou não.

```js
app.component('custom-form', {
  emits: {
    // Sem validação
    click: null,

    // Validar evento submit
    submit: ({ email, password }) => {
      if (email && password) {
        return true
      } else {
        console.warn('Invalid submit event payload!')
        return false
      }
    }
  },
  methods: {
    submitForm() {
      this.$emit('submit', { email, password })
    }
  }
})
```

## Argumentos `v-model`

Por padrão, em um componente `v-model` usa `modelValue` como propriedade e `update:modelValue` como evento. Podemos modificar esses nomes passando um argumento para `v-model`:

```html
<my-component v-model:title="bookTitle"></my-component>
```

Nesse caso, o componente filho espera a propriedade `title` e emite o evento `update:title` para sincronizar:

```js
const app = Vue.createApp({})

app.component('my-component', {
  props: {
    title: String
  },
  template: `
    <input 
      type="text"
      :value="title"
      @input="$emit('update:title', $event.target.value)">
  `
})
```

```html
<my-component v-model:title="bookTitle"></my-component>
```

## Vários vínculos `v-model`

Aproveitando a capacidade de direcionar uma determinada propriedade e evento como aprendemos antes em [argumentos `v-model`](#argumentos-v-model), agora podemos criar vários vínculos de v-model em uma única instância do componente.

Cada v-model será sincronizado com uma propriedade diferente, sem a necessidade de opções extras no componente:

```html
<user-name
  v-model:first-name="firstName"
  v-model:last-name="lastName"
></user-name>
```

```js
const app = Vue.createApp({})

app.component('user-name', {
  props: {
    firstName: String,
    lastName: String
  },
  template: `
    <input 
      type="text"
      :value="firstName"
      @input="$emit('update:firstName', $event.target.value)">

    <input
      type="text"
      :value="lastName"
      @input="$emit('update:lastName', $event.target.value)">
  `
})
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="lucianotonet" data-slug-hash="bGpZrLy" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Múltiplos v-models">
  <span>Veja o exemplo <a href="https://codepen.io/lucianotonet/pen/bGpZrLy">
  Múltiplos v-models</a> por L. Tonet (<a href="https://codepen.io/lucianotonet">@lucianotonet</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Manipulando modificadores `v-model`

Quando estávamos aprendendo sobre interligações em elementos _input_, vimos que `v-model` tem [modificadores embutidos](/guide/forms.html#modifiers) - `.trim`, `.number` e `.lazy`. Em alguns casos, entretanto, você também pode querer adicionar seus próprios modificadores personalizados.

Vamos criar um modificador personalizado de exemplo, `capitalize`, que coloca em maiúscula a primeira letra da string fornecida pela vinculação `v-model`.

Modificadores adicionados a um componente `v-model` serão fornecidos ao componente por meio da propriedade `modelModifiers`. No exemplo abaixo, criamos um componente que contém uma propriedade `modelModifiers` cujo padrão é um objeto vazio.

Observe que quando o gancho do ciclo de vida `created` do componente é acionado, a propriedade `modelModifiers` contém `capitalize` e seu valor é `true` - devido ao fato de ser definido na vinculação `v-model.capitalize="bar"`.

```html
<my-component v-model.capitalize="bar"></my-component>
```

```js
app.component('my-component', {
  props: {
    modelValue: String,
    modelModifiers: {
      default: () => ({})
    }
  },
  template: `
    <input type="text" 
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)">
  `,
  created() {
    console.log(this.modelModifiers) // { capitalize: true }
  }
})
```

Agora que temos nossa propriedade configurada, podemos verificar as chaves do objeto `modelModifiers` e escrever um manipulador para alterar o valor emitido. No código a seguir, colocaremos a string em maiúscula sempre que o elemento `<input />` disparar um evento `input`.

```html
<div id="app">
  <my-component v-model.capitalize="myText"></my-component>
  {{ myText }}
</div>
```

```js
const app = Vue.createApp({
  data() {
    return {
      myText: ''
    }
  }
})

app.component('my-component', {
  props: {
    modelValue: String,
    modelModifiers: {
      default: () => ({})
    }
  },
  methods: {
    emitValue(e) {
      let value = e.target.value
      if (this.modelModifiers.capitalize) {
        value = value.charAt(0).toUpperCase() + value.slice(1)
      }
      this.$emit('update:modelValue', value)
    }
  },
  template: `<input
    type="text"
    :value="modelValue"
    @input="emitValue">`
})

app.mount('#app')
```

Para as ligações `v-model` com argumentos, o nome da propriedade gerada será `arg + "Modifiers"`:

```html
<my-component v-model:foo.capitalize="bar"></my-component>
```

```js
app.component('my-component', {
  props: ['foo', 'fooModifiers'],
  template: `
    <input type="text" 
      :value="foo"
      @input="$emit('update:foo', $event.target.value)">
  `,
  created() {
    console.log(this.fooModifiers) // { capitalize: true }
  }
})
```
