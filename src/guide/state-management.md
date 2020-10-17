# Gerenciamento de Estado

## Implementação Oficial Estilo Flux

Grandes aplicações podem frequentemente crescer em complexidade, devido à múltiplas partes do estado da aplicação espalhadas em vários componentes e em interações entre eles. Para resolver esse problema, Vue oferece o [Vuex](https://github.com/vuejs/vuex), nossa própria biblioteca de gerenciamento de estado inspirada na arquitetura Elm. Ele é também integrado ao [vue-devtools](https://github.com/vuejs/vue-devtools), permitindo uma [navegação pelo histórico de mudanças de estado](https://raw.githubusercontent.com/vuejs/vue-devtools/master/media/demo.gif) (_time travel debugging_) sem qualquer configuração inicial.

### Informação para Desenvolvedores React

Se você vem do React, deve estar se perguntando como o Vuex se compara ao [Redux](https://github.com/reactjs/redux), a implementação Flux mais popular do ecossistema. O Redux é agnóstico quanto à camada _view_, de forma que ele pode ser usado facilmente com Vue através de [ligações simples](https://classic.yarnpkg.com/en/packages?q=redux%20vue&p=1). O Vuex é diferente por **saber** que está em uma aplicação Vue. Isso permite uma melhor integração com Vue ao fornecer uma API mais intuitiva e uma melhor experiência de desenvolvimento.

## Gerenciamento de Estado Simples do Zero

Frequentemente as pessoas esquecem que a fonte de verdade de aplicações Vue são os objetos reativos `data` - uma instância Vue apenas direciona acesso à eles. Portanto, se você possui uma parte do estado que deveria ser compartilhado entre instâncias múltiplas, você pode usar um método [`reactive`](/guide/reactivity-fundamentals.html#declaring-reactive-state) para tornar um objeto reativo.

```js
const sourceOfTruth = Vue.reactive({
  message: 'Olá'
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

Agora, sempre que `sourceOfTruth` mudar, tanto `appA` e `appB` irão atualizar suas _views_ automaticamente. Possuímos uma única fonte de verdade agora, porém a depuração vira um pesadelo. Qualquer dado pode ser modificado em qualquer parte de nossa aplicação a qualquer momento, sem deixar rastro algum.

```js
const appB = Vue.createApp({
  data() {
    return sourceOfTruth
  },
  mounted() {
    sourceOfTruth.message = 'Adeus' // ambas aplicações irão renderizar "Adeus"
  }
}).mount('#app-b')
```

Para ajudar a resolver esse problema, podemos adotar o padrão **store**:

```js
const store = {
  debug: true,

  state: Vue.reactive({
    message: 'Olá!'
  }),

  setMessageAction(newValue) {
    if (this.debug) {
      console.log('setMessageAction disparado com', newValue)
    }

    this.state.message = newValue
  },

  clearMessageAction() {
    if (this.debug) {
      console.log('clearMessageAction disparado')
    }

    this.state.message = ''
  }
}
```

Perceba que todas as ações que modificam o estado da store são colocadas dentro da própria store. Esse tipo de gerenciamento de estado centralizado facilita a compreensão de que tipo de mutações podem acontecer e como elas são disparadas. Agora, temos um _log_ de mudanças para quando algo der errado.

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
    store.setMessageAction('Adeus!')
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

![Gerenciamento de Estado](/images/state.png)

::: tip Dica
Você jamais deve substituir o objeto `state` original - os componentes e a store precisam compartilhar a referência para o mesmo objeto, de forma que as mutações sejam acompanhadas.
:::

Ao desenvolvermos uma convenção em que componentes não devem modificar diretamente o estado pertencente à store, mas sim disparar eventos que comuniquem a store à realizar ações, nós chegamos na arquitetura [Flux](https://facebook.github.io/flux/). A vantagem dessa convenção é que podemos registrar todas as mudanças de estado e implementar auxiliares avançados de depuração, tais como _logs_ de mutação, _snapshots_ e repetição de histórico (_history re-rolls_) / navegação de histórico (_time travel_).

O que nos leva totalmente de volta ao [Vuex](https://github.com/vuejs/vuex), então se você leu até aqui, provavelmente é hora de testá-lo!
