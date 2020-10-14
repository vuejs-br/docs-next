# Application & Component Instances

## Creating an Application Instance

Toda aplicação Vue começa com a criação de uma nova **instância** com a função `createApp`:

```js
const app = Vue.createApp({ /* options */ })
```

The application instance is used to register 'globals' that can then be used by components within that application. We'll discuss that in detail later in the guide but as a quick example:

```js
const app = Vue.createApp({})
app.component('SearchInput', SearchInputComponent)
app.directive('focus', FocusDirective)
app.use(LocalePlugin)
```

Most of the methods exposed by the application instance return that same instance, allowing for chaining:

```js
Vue.createApp({})
  .component('SearchInput', SearchInputComponent)
  .directive('focus', FocusDirective)
  .use(LocalePlugin)
```

You can browse the full application API in the [API reference](../api/application-api.html).

## The Root Component

The options passed to `createApp` are used to configure the **root component**. That component is used as the starting point for rendering when we **mount** the application.

An application needs to be mounted into a DOM element. For example, if we want to mount a Vue application into `<div id="app"></div>`, we should pass `#app`:

```js
const RootComponent = { /* options */ }
const app = Vue.createApp(RootComponent)
const vm = app.mount('#app')
```

Unlike most of the application methods, `mount` does not return the application. Instead it returns the root component instance.

Although not strictly associated with the [MVVM pattern](https://en.wikipedia.org/wiki/Model_View_ViewModel), Vue's design was partly inspired by it. As a convention, we often use the variable `vm` (short for ViewModel) to refer to a component instance.

While all the examples on this page only need a single component, most real applications are organized into a tree of nested, reusable components. For example, a Todo application's component tree might look like this:

```
Root Component
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

Each component will have its own component instance, `vm`. For some components, such as `TodoItem`, there will likely be multiple instances rendered at any one time. All of the component instances in this application will share the same application instance.

We'll talk about [the component system](component-basics.html) in detail later. For now, just be aware that the root component isn't really any different from any other component. The configuration options are the same, as is the behavior of the corresponding component instance.

## Component Instance Properties

Earlier in the guide we met `data` properties. Properties defined in `data` are exposed via the component instance:

```js
const app = Vue.createApp({
  data() {
    return { count: 4 }
  }
})

const vm = app.mount('#app')

console.log(vm.count) // => 4
```

There are various other component options that add user-defined properties to the component instance, such as `methods`, `props`, `computed`, `inject` and `setup`. We'll discuss each of these in depth later in the guide. All of the properties of the component instance, no matter how they are defined, will be accessible in the component's template.

Vue also exposes some built-in properties via the component instance, such as `$attrs` and `$emit`. These properties all have a `$` prefix to avoid conflicting with user-defined property names.

## Lifecycle Hooks

Each component instance goes through a series of initialization steps when it's created - for example, it needs to set up data observation, compile the template, mount the instance to the DOM, and update the DOM when data changes. Along the way, it also runs functions called **lifecycle hooks**, giving users the opportunity to add their own code at specific stages.

Por exemplo, o gatilho [`created`](../api/options-lifecycle-hooks.html#created) pode ser utilizado para executar código logo após a instância ser criada:

```js
Vue.createApp({
  data() {
    return { count: 1 }
  },
  created() {
    // `this` points to the vm instance
    console.log('count is: ' + this.count) // => "count is: 1"
  }
})
```

Existem também outros gatilhos que serão chamados em diferentes etapas do ciclo de vida da instância, como [`mounted`](../api/options-lifecycle-hooks.html#mounted), [`updated`](../api/options-lifecycle-hooks.html#updated), e [`unmounted`](../api/options-lifecycle-hooks.html#unmounted). Todos os gatilhos de ciclo de vida são executados com seu contexto `this` apontando para a atual instância ativa que o invoca.

::: tip Nota
Não utilize [_arrow functions_](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Functions/Arrow_functions) em propriedades de opções ou _callback_, como em `created: () => console.log(this.a)` ou `vm.$watch('a', newValue => this.myMethod())`. Como as _arrow functions_ não possuem um `this`,`this` será tratado como qualquer outra variável e lexicamente pesquisada através de escopos parentais até ser encontrada, frequentemente resultando em erros como `Uncaught TypeError: Cannot read property of undefined` ou `Uncaught TypeError: this.myMethod is not a function`.
:::

## Diagrama do Ciclo de Vida

Abaixo está um diagrama para o ciclo de vida da instância. Neste momento você não precisa entender completamente tudo o que está acontecendo, mas conforme você for aprendendo e construindo mais coisas, este diagrama se tornará uma referência útil.

<img src="/images/lifecycle.png" width="840" height="auto" style="margin: 0px auto; display: block; max-width: 100%;" loading="lazy" alt="Diagrama do ciclo de vida">
