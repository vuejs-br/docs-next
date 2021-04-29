# Gatilhos de Ciclo de vida

:::tip Dica
Todos os gatilhos de ciclo de vida têm seu contexto `this` vinculado automaticamente à instância, de forma que você pode acessar dados, dados computados e métodos. Isso significa que **você não deve usar uma _arrow function_ para definir um método de ciclo de vida** (exemplo: `created: () => this.fetchTodos()`). A justificativa é que _arrow functions_ vinculam o contexto pai, então o `this` não será a instância que você espera e o `this.fetchTodos` será `undefined`.
:::

## beforeCreate

- **Tipo:** `Function`

- **Detalhes:**

  Invocado sincronamente logo após a instância ser inicializada, antes da observação dos dados e configuração de eventos/observadores.

- **Ver também:** [Diagrama de ciclo de vida](../guide/instance.html#diagrama-do-ciclo-de-vida)

## created

- **Tipo:** `Function`

- **Detalhes:**

  Invocado sincronamente após a instância ser criada. Nesse estágio, a instância finalizou o processamento das opções, o que significa que a observação de dados, dados computados, métodos e _callbacks_ de observadores/eventos foram inicializados. Entretanto, a fase de montagem não foi iniciada, e a propriedade `$el` não estará disponível ainda.

- **Ver também:** [Diagrama de ciclo de vida](../guide/instance.html#diagrama-do-ciclo-de-vida)

## beforeMount

- **Tipo:** `Function`

- **Detalhes:**

  Invocado logo antes de a montagem iniciar: a função `render` está prestes a ser invocada pela primeira vez.

  **Esse gatilho não é invocado durante a renderização do lado do servidor.**

- **Ver também:** [Diagrama de ciclo de vida](../guide/instance.html#diagrama-do-ciclo-de-vida)

## mounted

- **Tipo:** `Function`

- **Detalhes:**

  Invocado após a instância ser montada, onde `el`, passado para o `Vue.createApp({}).mount()`, é substituído pelo recém criado `vm.$el`. Se a instância raiz for montada em um elemento presente no documento, `vm.$el` também estará no documento quando o `mounted` for invocado.

  Perceba que o `mounted` **não** garante que todos componentes filhos também foram montados. Se você quiser esperar até que toda a _view_ tenha sido renderizada, você pode usar o [vm.\$nextTick](../api/instance-methods.html#nexttick) dentro do `mounted`:

  ```js
  mounted() {
    this.$nextTick(function () {
      // Código que só será executado depois
      // de a view inteira ter sido renderizada
    })
  }
  ```

  **Esse gatilho não é invocado durante a renderização do lado do servidor.**

- **Ver também:** [Diagrama de ciclo de vida](../guide/instance.html#diagrama-do-ciclo-de-vida)

## beforeUpdate

- **Tipo:** `Function`

- **Detalhes:**

  Invocado quando há mudança nos dados e antes de o DOM ser atualizado. Esse é um bom momento para acessar a DOM existente antes de uma atualização, por exemplo, para remover escutas de evento adicionadas manualmente.

  **Esse gatilho não é invocado durante a renderização do lado do servidor, porque apenas a renderização inicial é realizada do lado do servidor.**

- **Ver também:** [Diagrama de ciclo de vida](../guide/instance.html#diagrama-do-ciclo-de-vida)

## updated

- **Tipo:** `Function`

- **Detalhes:**

  Invocado após uma mudança nos dados causar uma re-renderização do DOM.

  O DOM do componente terá sido atualizado quando esse gatilho for invocado, de forma que você pode realizar operações dependentes do DOM neste gatilho. Entretanto, na maioria dos casos você deve evitar mudanças de estado dentro do gatilho. Para reagir à mudanças de estado, normalmente é melhor usar uma [dado computado](./options-data.html#computed) ou um [observador](./options-data.html#watch).

  Perceba que o `updated` **não** garante que todos componentes filhos foram também re-renderizados. Se você quiser esperar até que toda _view_ tenha sido re-renderizada, você pode usar o [vm.\$nextTick](../api/instance-methods.html#nexttick) dentro do `updated`:

  ```js
  updated() {
    this.$nextTick(function () {
      // Código que só será executado depois
      // de a view inteira ter sido re-renderizada
    })
  }
  ```

  **Esse gatilho não é invocado durante a renderização do lado do servidor.**

- **Ver também:** [Diagrama de ciclo de vida](../guide/instance.html#diagrama-do-ciclo-de-vida)

## activated

- **Tipo:** `Function`

- **Detalhes:**

  Invocada quando um componente _kept-alive_ é ativado.

  **Esse gatilho não é invocado durante a renderização do lado do servidor.**

- **Ver também:**
  - [Componentes dinâmicos - _keep-alive_](../guide/component-dynamic-async.html#componentes-dinamicos-com-keep-alive)

## deactivated

- **Tipo:** `Function`

- **Detalhes:**

  Invocada quando um componente _kept-alive_ é desativado.

  **Esse gatilho não é invocado durante a renderização do lado do servidor.**

- **Ver também:**
  - [Componentes dinâmicos - _keep-alive_](../guide/component-dynamic-async.html#componentes-dinamicos-com-keep-alive)

## beforeUnmount

- **Tipo:** `Function`

- **Detalhes:**

  Invocada logo antes de uma instância ser desmontada. Nesse estágio, a instância ainda é totalmente funcional.

  **Esse gatilho não é invocado durante a renderização do lado do servidor.**

- **Ver também:** [Diagrama de ciclo de vida](../guide/instance.html#diagrama-do-ciclo-de-vida)

## unmounted

- **Tipo:** `Function`

- **Detalhes:**

  Invocada após uma instância ser desmontada. Quando esse gatilho é invocado, todas as diretivas da instância já foram desligadas, todas escutas de evento foram removidas e todos componentes filhos também foram desmontados.

  **Esse gatilho não é invocado durante a renderização do lado do servidor.**

- **Ver também:** [Diagrama de ciclo de vida](../guide/instance.html#diagrama-do-ciclo-de-vida)

## errorCaptured

- **Tipo:** `(err: Error, instance: Component, info: string) => ?boolean`

- **Detalhes:**

  Invocada quando um erro de qualquer componente descendente foi capturado. O gatilho recebe três argumentos: o erro, a instância que desencadeou o erro e uma _string_ contendo informações sobre onde o erro foi capturado. O gatilho pode retornar `false` para impedir que o erro continue a ser propagado.

  :::tip Dica
  Você pode modificar o estado de um componente nesse gatilho. Entretanto, é importante possuir condicionais em seu template ou função `render` que bloqueiem outras mudanças quando um erro for capturado; caso contrário, o componente entrará em um loop de renderização infinito.
  :::

  **Regras de Propagação de Erro**

  - Por padrão, todos os erros são enviados ao `config.errorHandler` global caso esteja definido, de forma que esses erros possam ainda ser reportados para um serviço de _analytics_ em um lugar só.

  - Caso existam vários gatilhos `errorCaptured` na cadeia de herança de um componente ou na cadeia do componente pai, todos eles serão invocados no mesmo erro.

  - Caso o próprio gatilho `errorCaptured` lance um erro, tanto este erro quanto o que foi capturado originalmente serão enviados ao `config.errorHandler`.

  - Um gatilho `errorCaptured` pode retornar `false` para prevenir que o erro seja propagado, o que basicamente equivale à dizer que "o erro foi tratado e deve ser ignorado". Isso previne que outros gatilhos `errorCaptured` ou que o `config.errorHandler` sejam invocados para o erro.

## renderTracked

- **Tipo:** `(e: DebuggerEvent) => void`

- **Detalhes:**

  Invocado quando a re-renderização do DOM é monitorada. O gatilho recebe um evento de depuração como argumento. Esse evento informa que operação monitorou o componente, o objeto alvo e a chave da operação.

- **Uso:**

  ```html
  <div id="app">
    <button v-on:click="addToCart">Adicionar no carrinho</button>
    <p>Carrinho({{ cart }})</p>
  </div>
  ```

  ```js
  const app = Vue.createApp({
    data() {
      return {
        cart: 0
      }
    },
    renderTracked({ key, target, type }) {
      console.log({ key, target, type })
      /* Isso será exibido quando o componente for renderizado pela primeira vez:
      {
        key: "cart",
        target: {
          cart: 0
        },
        type: "get"
      }
      */
    },
    methods: {
      addToCart() {
        this.cart += 1
      }
    }
  })

  app.mount('#app')
  ```

## renderTriggered

- **Tipo:** `(e: DebuggerEvent) => void`

- **Detalhes:**

  Invocado quando a re-renderização do DOM é disparada. De forma similar ao [`renderTracked`](#rendertracked), ele recebe um evento de depuração como argumento. Esse evento informa que operação disparou a re-renderização, o objeto alvo e a chave da operação.

- **Uso:**

  ```html
  <div id="app">
    <button v-on:click="addToCart">Adicionar no carrinho</button>
    <p>Carrinho({{ cart }})</p>
  </div>
  ```

  ```js
  const app = Vue.createApp({
    data() {
      return {
        cart: 0
      }
    },
    renderTriggered({ key, target, type }) {
      console.log({ key, target, type })
    },
    methods: {
      addToCart() {
        this.cart += 1
        /* Isso invocará o renderTriggered
          {
            key: "cart",
            target: {
              cart: 1
            },
            type: "set"
          }
        */
      }
    }
  })

  app.mount('#app')
  ```
