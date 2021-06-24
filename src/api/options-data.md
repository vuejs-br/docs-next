# Dados

## data

- **Tipo:** `Function`

- **Detalhes:**

  A função que retorna um objeto de dados para a instância do componente. Em `data`, não recomendamos observar objetos com seu próprio comportamento com estado, como objetos de API do navegador e propriedades de protótipo. Uma boa ideia seria ter aqui apenas um objeto simples que representa os dados do componente.

  Uma vez observado, você não pode mais adicionar propriedades reativas ao objeto de dados raiz. Portanto, é recomendável declarar antecipadamente todas as propriedades reativas de nível raiz, antes da criação da instância.

  Depois da instância ser criada, o objeto de dados original pode ser acessado como `vm.$data`. A instância do componente também faz proxy de todas as propriedades encontradas no objeto de dados, então `vm.a` será equivalente a `vm.$data.a`.

  Propriedades que comecem com `_` ou `$` **não** será feito proxy na instância do componente porque eles podem entrar em conflito com as propriedades internas do Vue ou métodos de API. Você terá que acessá-las como `vm.$data._property`.

- **Exemplo:**

  ```js
  // Criação direta de instância
  const data = { a: 1 }

  // O objeto é adicionado a uma instância do componente
  const vm = Vue.createApp({
    data() {
      return data
    }
  }).mount('#app')

  console.log(vm.a) // => 1
  ```

  Note que se usar uma *arrow function* com a propriedade `data`,` this` não será a instância do componente, mas você poderá continuar tendo acesso à instância através do argumento da função:

  ```js
  data: vm => ({ a: vm.myProp })
  ```

- **Ver também:** [Reatividade em profundidade](../guide/reactivity.html)

## props

- **Tipo:** `Array<string> | Object`

- **Detalhes:**

  Uma lista/*hash* de atributos que são expostos para aceitar dados do componente pai. Possui tanto uma sintaxe simples baseada em Array como, alternativamente, uma sintaxe baseada em Object, que permite configurações avançadas como verificação de tipos, validações personalizadas e valores padrão.

  Com a sintaxe baseada em Object, você pode usar as seguintes opções:

  - `type`: pode ser um dos seguintes construtores nativos: `String`, `Number`, `Boolean`, `Array`, `Object`, `Date`, `Function`, `Symbol`, qualquer função construtora personalizada ou um array delas. Irá verificar se um prop tem um determinado tipo, e irá lançar um aviso se não tiver. [Mais informação](../guide/component-props.html#prop-types) em tipos de props.
  - `default`: `any`
    Especifica um valor padrão para o prop. Se o prop não é passado, este valor será usado em seu lugar. Valores padrão de tipo Object ou Array devem ser retornados de uma função *factory*.
  - `required`: `Boolean`
    Define se o prop é necessário. Em ambiente de não-produção, um aviso de console será lançado se esse valor for verdadeiro e o prop não for passado.
  - `validator`: `Function`
    Função validadora personalizada que usa o valor do prop como único argumento. Exceto em ambiente de produção, um aviso de console será lançado se essa função retornar um valor falso (ou seja, a validação falhar). Pode ler mais sobre validação de prop [aqui](../guide/component-props.html#prop-validation).

- **Exemplo:**

  ```js
  const app = Vue.createApp({})

  // sintaxe simples
  app.component('props-demo-simple', {
    props: ['size', 'myMessage']
  })

  // sintaxe de objeto com validação
  app.component('props-demo-advanced', {
    props: {
      // verificação de tipo
      height: Number,
      // verificação de tipo com outras validações
      age: {
        type: Number,
        default: 0,
        required: true,
        validator: value => {
          return value >= 0
        }
      }
    }
  })
  ```

- **Ver também:** [Propriedades](../guide/component-props.html)

## computed

- **Tipo:** `{ [key: string]: Function | { get: Function, set: Function } }`

- **Detalhes:**

  Propriedades computadas a serem combinadas na instância do componente. Todos os getters e setters tem o seu contexto `this` vinculado automaticamente à instância do componente.

  Note que se usar *arrow function* com uma propriedade computada, `this` não será a instância do componente, mas você poderá ter acesso a instância através do primeiro argumento da função:

  ```js
  computed: {
    aDouble: vm => vm.a * 2
  }
  ```

  Propriedades computadas são cacheadas, e apenas recalculados quando dependências reativas mudam. Note que se uma certa dependência está fora do escopo da instância (ex.: não reativa), o dado computado **não** será atualizado.

- **Exemplo:**

  ```js
  const app = Vue.createApp({
    data() {
      return { a: 1 }
    },
    computed: {
      // apenas get
      aDouble() {
        return this.a * 2
      },
      // ambos get e set
      aPlus: {
        get() {
          return this.a + 1
        },
        set(v) {
          this.a = v - 1
        }
      }
    }
  })

  const vm = app.mount('#app')
  console.log(vm.aPlus) // => 2
  vm.aPlus = 3
  console.log(vm.a) // => 2
  console.log(vm.aDouble) // => 4
  ```

- **Ver também:** [Dados Computados](../guide/computed.html)

## methods

- **Tipo:** `{ [key: string]: Function }`

- **Detalhes:**

  Métodos a serem combinados na instância do componente. Você pode acessar esses métodos diretamente na instância VM ou usá-los em expressões de diretiva. Todos os métodos terão o seu contexto `this` automaticamente vinculado à instância do componente.

  :::tip Note
  Note que **você não deve usar *arrow function* para definir um método** (ex. `plus: () => this.a++`). A razão é que *arrow functions* fazem bind do contexto pai, então `this` não será a instância Vue como você está esperando e `this.a` será undefined.
  :::

- **Exemplo:**

  ```js
  const app = Vue.createApp({
    data() {
      return { a: 1 }
    },
    methods: {
      plus() {
        this.a++
      }
    }
  })

  const vm = app.mount('#app')

  vm.plus()
  console.log(vm.a) // => 2
  ```

- **Ver também:** [Manipulação de Eventos](../guide/events.html)

## watch

- **Tipo:** `{ [key: string]: string | Function | Object | Array}`

- **Detalhes:**

  Um objeto onde as chaves são expressões para observar e os valores são os callbacks correspondentes. O valor também pode ser uma string de um nome de método ou um Object que contém opções adicionais. A instância do componente chamará `$watch()` para cada entrada no objeto na inicialização. Veja [$watch](instance-methods.html#watch) para mais informações sobre as opções `deep`, `immediate` e `flush`.

- **Exemplo:**

  ```js
  const app = Vue.createApp({
    data() {
      return {
        a: 1,
        b: 2,
        c: {
          d: 4
        },
        e: 'teste',
        f: 5
      }
    },
    watch: {
      a(val, oldVal) {
        console.log(`new: ${val}, old: ${oldVal}`)
      },
      // nome do método como string
      b: 'someMethod',
      // o callback será chamado sempre que qualquer uma das propriedades do objeto observado mudar,
      // independentemente de sua profundidade aninhada
      c: {
        handler(val, oldVal) {
          console.log('c mudou')
        },
        deep: true
      },
      // o callback será chamado imediatamente após o início da observação
      e: {
        handler(val, oldVal) {
          console.log('e mudou')
        },
        immediate: true
      },
      // você pode passar uma array de callbacks, eles serão chamados um por um
      f: [
        'handle1',
        function handle2(val, oldVal) {
          console.log('handle2 acionado')
        },
        {
          handler: function handle3(val, oldVal) {
            console.log('handle3 acionado')
          }
          /* ... */
        }
      ]
    },
    methods: {
      someMethod() {
        console.log('b mudou')
      },
      handle1() {
        console.log('handle1 acionado')
      }
    }
  })

  const vm = app.mount('#app')

  vm.a = 3 // => new: 3, old: 1
  ```

  ::: tip Note
  Note que _você não deve usar arrow function para definir um observador_ (ex. `searchQuery: newValue => this.updateAutocomplete(newValue)`). A razão é que *arrow functions* fazem bind do contexto pai, então `this` não será a instância do componente como você espera e `this.updateAutocomplete` será undefined.
  :::

- **Ver também:** [Observadores](../guide/computed.html#watchers)

## emits

- **Tipo:** `Array<string> | Object`

- **Detalhes:**

  Uma lista/*hash* de eventos personalizados pode ser emitida do componente. Possui tanto uma sintaxe simples baseada em Array como, alternativamente, uma sintaxe baseada em Object que permite configurar uma validação de evento.

  Na sintaxe baseada em Object, o valor de cada propriedade pode ser `null` ou uma função validadora. A função de validação receberá os argumentos adicionais passados para a chamada `$emit`. Por exemplo, se `this.$emit('foo', 1)` for chamado, o validador correspondente para `foo` receberá o argumento `1`. A função validadora deve retornar um booleano para indicar se os argumentos do evento são válidos.

- **Uso:**

  ```js
  const app = Vue.createApp({})

  // Sintaxe de Array
  app.component('todo-item', {
    emits: ['check'],
    created() {
      this.$emit('check')
    }
  })

  // Sintaxe de Object
  app.component('reply-form', {
    emits: {
      // sem validação
      click: null,

      // com validação
      submit: payload => {
        if (payload.email && payload.password) {
          return true
        } else {
          console.warn(`Evento de envio payload inválido!`)
          return false
        }
      }
    }
  })
  ```

  ::: tip Note
  Os eventos listados na opção `emits` **não** serão herdados pelo elemento raiz do componente e também serão excluídos da propriedade `$attrs`.
  :::

* **Ver também:** [Herança de Atributo](../guide/component-attrs.html#attribute-inheritance)
