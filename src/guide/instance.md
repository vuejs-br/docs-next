# A instância da Aplicação

## Criando uma instância

Toda aplicação Vue começa com a criação de uma nova **instância Vue** com a função `Vue`:

```js
Vue.createApp(/* opções */)
```

Após a instância ser criada, podemos _montá-la_, passando um contêiner para o método `mount`. Por exemplo, se quisermos montar um aplicativo Vue em `<div id="app"></div>`, devemos passar `#app`:

```js
Vue.createApp(/* options */).mount('#app')
```

Embora não seja estritamente associado com o [padrão MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel), o _design_ do Vue foi parcialmente inspirado por ele. Como convenção, muitas vezes usamos a variável `vm` (abreviação de _ViewModel_) para se referir à instância Vue.

Quando você cria uma instância Vue, é necessário passar um **objeto de opções**. A maior parte deste guia descreve como você pode utilizar estas opções para criar os comportamentos desejados. Para referência, você também pode navegar pela lista completa de opções na [documentação da API](../api/options-data.html).

Uma aplicação Vue consiste em uma **instância Vue raiz** criada com `createApp`, opcionalmente organizada em uma árvore de componentes reutilizáveis aninhados. Por exemplo, um aplicativo de tarefas a realizar (do tipo _todo list_) poderia ter uma árvore de componentes como esta:

```
Root Instance
└─ TodoList
   ├─ TodoItem
   │  ├─ DeleteTodoButton
   │  └─ EditTodoButton
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

Falaremos sobre [o sistema de componentes](component-basics.html) em detalhes futuramente. Por enquanto, saiba apenas que todos os componentes Vue também são instâncias, e assim aceitam o mesmo objeto de opções.

## Dados e Métodos

Quando uma instância é criada, ela adiciona todas as propriedades encontradas no objeto `data` ao [**sistema de reatividade** do Vue](reactivity.html). Quando os valores dessas propriedades mudam, a camada visual (em inglês, _view_) "reage", atualizando-se para corresponder aos novos valores.

```js
// Nosso objeto de dados
const data = { a: 1 }

// O objeto é adicionado à raiz da instância
const vm = Vue.createApp({
  data() {
    return data
  }
}).mount('#app')

// Acessar a propriedade na instância
// retorna aquilo que está no dado original
vm.a === data.a // => true

// Atribuir à propriedade na instância
// também afeta o dado original
vm.a = 2
data.a // => 2
```

Quando este dado for modificado, a camada visual irá "re-renderizar". Deve-se observar que as propriedades em `data` só são **reativas** se elas já existiam quando a instância foi criada. Isso significa que se você adicionar uma nova propriedade, como:

```js
vm.b = 'hi'
```

Então as mudanças em `b` não irão disparar qualquer atualização na interface. Se você sabe que precisará de uma propriedade no futuro, mas ela inicia vazia ou não existente, você precisará atribuir algum valor inicial. Por exemplo:

```js
data() {
  return {
    newTodoText: '',
    visitCount: 0,
    hideCompletedTodos: false,
    todos: [],
    error: null
  }
}
```

A única exceção é quanto ao uso do `Object.freeze()`, que previne propriedades existentes de serem modificadas, o que também significa que o sistema de reatividade não pode _rastrear_ mudanças.

```js
const obj = {
  foo: 'bar'
}

Object.freeze(obj)

const vm = Vue.createApp({
  data() {
    return obj
  }
}).mount('#app')
```

```html
<div id="app">
  <p>{{ foo }}</p>
  <!-- não irá atualizar mais o `foo`! -->
  <button v-on:click="foo = 'baz'">Alterar</button>
</div>
```

Em adição às propriedades de dados, instâncias expõem uma quantidade relevante de propriedades e métodos. Estes são prefixados com `$` para diferenciá-los das propriedades definidas pelo usuário. Por exemplo:

```js
const vm = Vue.createApp({
  data() {
    return {
      a: 1
    }
  }
}).mount('#example')

vm.$data.a // => 1
```

No futuro, você pode consultar a [documentação da API](../api/#Propriedades-de-Instancia) para a lista completa de propriedades e métodos da instância.

## Ciclo de Vida da Instância

Cada instância Vue passa por uma série de etapas em sua inicialização - por exemplo, é necessário configurar a observação de dados, compilar o _template_, montar a instância no DOM, e atualizar o DOM quando os dados forem alterados. Ao longo do caminho, também ocorre a invocação de algumas funções chamadas de **gatilhos de ciclo de vida** (em inglês, _lifecycle hooks_), oferecendo a oportunidade de executar lógicas personalizadas em etapas específicas.

Por exemplo, o gatilho [`created`](../api/#created) pode ser utilizado para executar código logo após a instância ser criada:

```js
Vue.createApp({
  data() {
    return {
      a: 1
    }
  },
  created() {
    // `this` aponta para a instância
    console.log('a é: ' + this.a) // => "a é: 1"
  }
})
```

There are also other hooks which will be called at different stages of the instance's lifecycle, such as [mounted](../api/options-lifecycle-hooks.html#mounted), [updated](../api/options-lifecycle-hooks.html#updated), and [unmounted](../api/options-lifecycle-hooks.html#unmounted). All lifecycle hooks are called with their `this` context pointing to the current active instance invoking it.
Existem também outros gatilhos que serão chamados em diferentes etapas do ciclo de vida da instância, como [`mounted`](../api/options-lifecycle-hooks.html#mounted), [`updated`](../api/options-lifecycle-hooks.html#updated), e [`unmounted`](../api/options-lifecycle-hooks.html#unmounted). Todos os gatilhos de ciclo de vida são executados com seu contexto `this` apontando para a atual instância ativa que o invoca.

::: tip
Não utilize [_arrow functions_](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Functions/Arrow_functions) em propriedades de opções ou _callback_, como em `created: () => console.log(this.a)` ou `vm.$watch('a', newValue => this.myMethod())`. Como as _arrow functions_ não possuem um `this`,`this` será tratado como qualquer outra variável e lexicamente pesquisada através de escopos parentais até ser encontrada, frequentemente resultando em erros como `Uncaught TypeError: Cannot read property of undefined` ou `Uncaught TypeError: this.myMethod is not a function`.
:::

## Diagrama do Ciclo de Vida

Abaixo está um diagrama para o ciclo de vida da instância. Você não precisa entender completamente tudo o que está acontecendo neste momento, mas conforme você for aprendendo e construindo mais coisas, este diagrama se tornará uma referência útil.

<img src="/images/lifecycle.png" width="840" height="auto" style="margin: 0px auto; display: block; max-width: 100%;" loading="lazy" alt="Diagrama do ciclo de vida">
