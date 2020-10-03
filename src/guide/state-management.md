# Gerenciamento de estado

## Implementação oficial baseada em Flux

Grandes aplicações podem frequentemente crescer em complexidade, devido à múltiplas partes do estado da aplicação espalhadas em vários componentes e em interações entre eles. Para resolver esse problema, Vue oferece o [Vuex](https://github.com/vuejs/vuex), nossa própria biblioteca de gerenciamento de estado inspirada na arquitetura Elm. Ele é também integrado ao [vue-devtools](https://github.com/vuejs/vue-devtools), permitindo uma [navegação pelo histórico de mudanças de estado](https://raw.githubusercontent.com/vuejs/vue-devtools/master/media/demo.gif) (_time travel debugging_) sem qualquer configuração inicial.

### Informação para Desenvolvedores React

Se você vem do React, deve estar se perguntando como o Vuex se compara ao [Redux](https://github.com/reactjs/redux), a implementação Flux mais popular do ecossistema. O Redux é agnóstico quanto à camada _view_, de forma que ele pode ser usado facilmente com Vue através de [bindings simples](https://classic.yarnpkg.com/en/packages?q=redux%20vue&p=1). O Vuex é diferente por **saber** que está em uma aplicação Vue. Isso permite uma melhor integração com Vue ao fornecer uma API mais intuitiva e uma melhor experiência de desenvolvimento.

## Gerenciamento de estado simples do zero

Frequentemente as pessoas esquecem que a fonte de verdade de aplicações Vue são os objetos `data` - uma instância Vue apenas direciona acesso à eles. Portanto, se você possui uma parte do estado que deveria ser comportilhado entre instâncias múltiplas, você pode usar um método [reativo](/guide/reactivity-fundamentals.html#declaring-reactive-state) para tornar um objeto reativo.

```js
const sourceOfTruth = Vue.reactive({
  message: 'Hello'
})

const appA = Vue.createApp({
  data() {
    return sourceOfTruth
  }
}).mount('#app-a')

const appB = Vue.createApp({
  data() {
    return sourceOfTruth
  }
}).mount('#app-b')
```

```html
<div id="app-a">App A: {{ message }}</div>

<div id="app-b">App B: {{ message }}</div>
```

Agora, sempre que `sourceOfTruth` mudar, tanto `appA` e `appB` irão atualizar suas _views_ automaticamente. Possuímos uma fonte de verdade única agora, porém a depuração vira um pesadelo. Qualquer dado pode ser modificado em qualquer parte de nossa aplicação a qualquer momento, sem deixar rastro algum.

```js
const appB = Vue.createApp({
  data() {
    return sourceOfTruth
  },
  mounted() {
    sourceOfTruth.message = 'Goodbye' // ambas aplicações irão renderizar "Goodbye"
  }
}).mount('#app-b')
```

Para ajudar a resolver esse problema, podemos adotar o padrão **store**.

```js
const store = {
  debug: true,

  state: Vue.reactive({
    message: 'Hello!'
  }),

  setMessageAction(newValue) {
    if (this.debug) {
      console.log('setMessageAction triggered with', newValue)
    }

    this.state.message = newValue
  },

  clearMessageAction() {
    if (this.debug) {
      console.log('clearMessageAction triggered')
    }

    this.state.message = ''
  }
}
```

Perceba que todas ações que modificam o estado da store são colocadas dentro da própria store. Esse tipo de gerenciamento de estado centralizado facilita a compreensão de que tipo de mutações podem acontecer e como elas são disparadas. Agora, temos um _log_ de mudanças para quando algo der errado.

Além disso, cada instância/componente pode ainda possuir e gerenciar seu estado privado:

```html
<div id="app-a">{{sharedState.message}}</div>

<div id="app-b">{{sharedState.message}}</div>
```

```js
const appA = Vue.createApp({
  data() {
    return {
      privateState: {},
      sharedState: store.state
    }
  },
  mounted() {
    store.setMessageAction('Goodbye!')
  }
}).mount('#app-a')

const appB = Vue.createApp({
  data() {
    return {
      privateState: {},
      sharedState: store.state
    }
  }
}).mount('#app-b')
```

![Gerenciamento de estado](/images/state.png)

::: Dica
Você não deve substituir o objeto de estado original - os componentes e a store precisam compartilhar a referência para o objeto, de forma que as mutações sejam acompanhadas.
:::

Ao desenvolvermos uma convenção em que componentes não devem modificar diretamente o estado pertencente à store, mas sim disparar eventos que comuniquem à store que realize ações, nós chegamos na arquitetura [Flux](https://facebook.github.io/flux/). A vantagem dessa convenção é que podemos registrar todas mudanças de estado e implementar _helpers_ avançados de depuração, tais como _logs_ de mutação, _snaptshots_ e repetição de histórico (_history re-rolls_) / navegação de histórico (_time travel_).

O que nos leva de volta ao [Vuex](https://github.com/vuejs/vuex), então se você leu até aqui, provavelmente é hora de testá-lo!
