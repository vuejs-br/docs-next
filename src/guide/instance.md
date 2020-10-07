# Instâncias de Aplicação e de componentes

## Criando uma Instância de Aplicação

Toda aplicação Vue começa com a criação de uma nova **instância** com a função `createApp`:

```js
const app = Vue.createApp({ /* opções */ })
```

A instância de aplicação é usada para registrar 'globais' que podem ser usados pelos componentes dentro da aplicação. Discutiremos isso em detalhes posteriormente no guia, mas como um exemplo rápido:

```js
const app = Vue.createApp({})
app.component('SearchInput', SearchInputComponent)
app.directive('focus', FocusDirective)
app.use(LocalePlugin)
```

A maioria dos métodos expostos pela instância de aplicação retornam a própria instância, permitindo encadeamentos:

```js
Vue.createApp({})
  .component('SearchInput', SearchInputComponent)
  .directive('focus', FocusDirective)
  .use(LocalePlugin)
```

Você pode navegar na API completa da aplicação na [documentação da API](../api/application-api.html).

## O Componente Raiz

As opções passadas para `createApp` são usadas para configurar o  **componente raiz**. Esse componente é usado como ponto de partida para renderização quando nós **montamos** a aplicação.

Uma aplicação precisa ser montada em um elemento DOM. Por exemplo, se quisermos montar um aplicação Vue em `<div id="app"></div>`, devemos passar `#app`:

```js
const RootComponent = { /* opções */ }
const app = Vue.createApp(RootComponent)
const vm = app.mount('#app')
```

Diferente da maioria dos métodos de aplicação, `mount` não retorna uma instância da aplicação. Em vez disso, retorna a instância do componente raiz.

Embora não seja estritamente associado ao [padrão MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel), o design do Vue foi parcialmente inspirado por ele. Como convenção, costumamos usar a variável `vm` (abreviação de ViewModel) para se referir a uma instância de componente.

Embora todos os exemplos nesta página precisem apenas de um único componente, a maioria das aplicações reais são organizadas em uma árvore de componentes reutilizáveis aninhados. Por exemplo, a árvore de componentes de uma aplicação Todo pode ter a seguinte aparência:

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

Cada componente terá sua própria instância de componente, `vm`. Para alguns componentes, como `TodoItem`, provavelmente haverão várias instâncias renderizadas a qualquer momento. Todas as instâncias de componente nesta aplicação irão compartilhar a mesma instância de aplicação.

Falaremos sobre [o sistema de componentes](component-basics.html) em detalhes depois. Por enquanto, apenas esteja ciente de que o componente raiz não é realmente diferente de qualquer outro componente. As opções de configuração são as mesmas, assim como o comportamento da instância do componente correspondente.

## Propriedades da Instância do Componente

No início do guia que conhecemos as propriedades do `data`. Propriedades definidas no `data` são expostas por meio da instância do componente:

```js
const app = Vue.createApp({
  data() {
    return { count: 4 }
  }
})

const vm = app.mount('#app')

console.log(vm.count) // => 4
```

Existem várias outras opções de componentes que adicionam propriedades definidas pelo usuário à instância do componente, tais como `methods`, `props`, `computed`, `inject` e `setup`. Discutiremos cada um deles em detalhes posteriormente no guia. Todas as propriedades da instância do componente, não importa como sejam definidas, estará acessível no template do componente.

O Vue também expõe algumas propriedades integradas por meio da instância do componente, tais como `$attrs` e `$emit`. Todas essas propriedades têm um `$` prefixo para evitar conflito com nomes de propriedade definidas pelo usuário.

## Gatilhos de Ciclo de Vida

Cada instância do componente passa por uma série de etapas de inicialização quando é criada - por exemplo, é preciso configurar a observação de dados, compilar o template, montar a instância no DOM, e atualizar o DOM quando os dados mudam. Ao longo desse caminho, também executa funções chamadas **gatilhos de ciclo de vida**, dando aos usuários a oportunidade de adicionar seu próprio código em estágios específicos.

Por exemplo, o gatilho [`created`](../api/options-lifecycle-hooks.html#created) pode ser utilizado para executar código logo após a instância ser criada:

```js
Vue.createApp({
  data() {
    return { count: 1 }
  },
  created() {
    // `this` aponta para a instância vm
    console.log('count is: ' + this.count) // => "count is: 1"
  }
})
```

Existem também outros gatilhos que serão chamados em diferentes etapas do ciclo de vida da instância, como [`mounted`](../api/options-lifecycle-hooks.html#mounted), [`updated`](../api/options-lifecycle-hooks.html#updated), e [`unmounted`](../api/options-lifecycle-hooks.html#unmounted). Todos os gatilhos de ciclo de vida são executados com seu contexto `this` apontando para a atual instância ativa que o invoca.

::: tip Nota
Não utilize [_arrow functions_](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Functions/Arrow_functions) em propriedades de opções ou _callback_, como em `created: () => console.log(this.a)` ou `vm.$watch('a', newValue => this.myMethod())`. Como as _arrow functions_ não possuem um `this`,`this` será tratado como qualquer outra variável e lexicalmente pesquisada através de escopos parentais até ser encontrada, frequentemente resultando em erros como `Uncaught TypeError: Cannot read property of undefined` ou `Uncaught TypeError: this.myMethod is not a function`.
:::

## Diagrama do Ciclo de Vida

Abaixo está um diagrama para o ciclo de vida da instância. Neste momento você não precisa entender completamente tudo o que está acontecendo, mas conforme você for aprendendo e construindo mais coisas, este diagrama se tornará uma referência útil.

<img src="/images/lifecycle.png" width="840" height="auto" style="margin: 0px auto; display: block; max-width: 100%;" loading="lazy" alt="Diagrama do ciclo de vida">
