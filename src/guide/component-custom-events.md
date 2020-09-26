# Eventos customizados

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

## Definindo Eventos Customizados

<VideoLesson href="https://vueschool.io/lessons/defining-custom-events-emits?friend=vuejs" title="Aprenda a definir quais eventos um componente pode emitir com a Vue School (em inglês)">Assista a um vídeo grátis sobre como definir Eventos Customizados na Vue School (em inglês)</VideoLesson>

Os eventos emitidos podem ser definidos no componente através da opção `emits`.

```js
app.component('custom-form', {
  emits: ['in-focus', 'submit']
})
```

No caso de um evento nativo (por exemplo, `click`) ser definido na opção `emits`, ele será substituído pelo evento do componente em vez de ser tratado como um ouvinte nativo.

::: tip DICA
Recomenda-se definir todos os eventos emitidos para documentar melhor como um componente deve funcionar.
:::

### Validate Emitted Events

Similar to prop type validation, an emitted event can be validated if it is defined with the Object syntax instead of the Array syntax.

To add validation, the event is assigned a function that receives the arguments passed to the `$emit` call and returns a boolean to indicate whether the event is valid or not.

```js
app.component('custom-form', {
  emits: {
    // No validation
    click: null,

    // Validate submit event
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

## `v-model` arguments

By default, `v-model` on a component uses `modelValue` as the prop and `update:modelValue` as the event. We can modify these names passing an argument to `v-model`:

```html
<my-component v-model:foo="bar"></my-component>
```

In this case, child component will expect a `foo` prop and emits `update:foo` event to sync:

```js
const app = Vue.createApp({})

app.component('my-component', {
  props: {
    foo: String
  },
  template: `
    <input 
      type="text"
      :value="foo"
      @input="$emit('update:foo', $event.target.value)">
  `
})
```

```html
<my-component v-model:foo="bar"></my-component>
```

## Multiple `v-model` bindings

By leveraging the ability to target a particular prop and event as we learned before with [`v-model` arguments](#v-model-arguments), we can now create multiple v-model bindings on a single component instance.

Each v-model will sync to a different prop, without the need for extra options in the component:

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

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="Vue" data-slug-hash="GRoPPrM" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Multiple v-models">
  <span>See the Pen <a href="https://codepen.io/team/Vue/pen/GRoPPrM">
  Multiple v-models</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Handling `v-model` modifiers

When we were learning about form input bindings, we saw that `v-model` has [built-in modifiers](/guide/forms.html#modifiers) - `.trim`, `.number` and `.lazy`. In some cases, however, you might also want to add your own custom modifiers.

Let's create an example custom modifier, `capitalize`, that capitalizes the first letter of the string provided by the `v-model` binding.

Modifiers added to a component `v-model` will be provided to the component via the `modelModifiers` prop. In the below example, we have created a component that contains a `modelModifiers` prop that defaults to an empty object.

Notice that when the component's `created` lifecycle hook triggers, the `modelModifiers` prop contains `capitalize` and its value is `true` - due to it being set on the `v-model` binding `v-model.capitalize="bar"`.

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

Now that we have our prop set up, we can check the `modelModifiers` object keys and write a handler to change the emitted value. In the code below we will capitalize the string whenever the `<input />` element fires an `input` event.

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

For `v-model` bindings with arguments, the generated prop name will be `arg + "Modifiers"`:

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
