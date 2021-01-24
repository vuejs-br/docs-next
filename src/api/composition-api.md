# API de Composição

> Esta seção usa a sintaxe  de [Componentes Single File](../guide/single-file-component.html) como exemplos de códigos

## `setup`

Uma opção de componente que é executado **antes** do componente ser criado, uma vez que as `props` são resolvidas, e serve como um ponto de entrada para *API* de composição

- **Argumentos:**

  - `{Data} props`
  - `{SetupContext} context`

- **Tipagem**:

```ts
interface Data {
  [key: string]: unknown
}

interface SetupContext {
  attrs: Data
  slots: Slots
  emit: (event: string, ...args: unknown[]) => void
}

function setup(props: Data, context: SetupContext): Data
```

::: tip Dica
Para obter a inferência de tipos para os argumentos passados para o `setup()`, é necessário o uso do [defineComponent](global-api.html#definecomponent).
:::

- **Exemplo**

  Com o template:

  ```vue-html
  <!-- MyBook.vue -->
  <template>
    <div>{{ readersNumber }} {{ book.title }}</div>
  </template>

  <script>
    import { ref, reactive } from 'vue'

    export default {
      setup() {
        const readersNumber = ref(0)
        const book = reactive({ title: 'Vue 3: o guia.' })

        // Expõe para o template
        return {
          readersNumber,
          book
        }
      }
    }
  </script>
  ```

  Com função de renderização:

  ```js
  // MyBook.vue

  import { h, ref, reactive } from 'vue'

  export default {
    setup() {
      const readersNumber = ref(0)
      const book = reactive({ title: 'Vue 3 O Guia' })
      // Observe que precisamos expor explicitamente o valor de *ref* aqui.
      return () => h('div', [readersNumber.value, book.title])
    }
  }
  ```

- **Veja também**: [API de Composição `setup`](../guide/composition-api-setup.html)

## Ciclos de vida

<!-- Lifecycle hooks can be registered with directly-imported `onX` functions: -->
Os gatilhos de ciclos de vida podem ser registrados importando diretamente as funções `onX`:

```js
import { onMounted, onUpdated, onUnmounted } from 'vue'

const MyComponent = {
  setup() {
    onMounted(() => {
      console.log('Montado!')
    })
    onUpdated(() => {
      console.log('Atualizado!')
    })
    onUnmounted(() => {
      console.log('Desmontado!')
    })
  }
}
```

Essas funções de registro de ciclo de vida somente podem ser usadas  de forma síncrona durante o [`setup`](#setup), já que eles dependem do estado global para localizar a instância ativa atual (A instância do componente cujo o `setup()` esta chamando agora). Chamá-los sem uma instância ativa vai resultar em um erro.


O contexto da instância também é definido durante a execução síncrona dos gatilhos de ciclo de vida.
Como resultado, os observadores e os dados computados criados de forma síncrona dentro dos gatilhos de ciclo de vida também são desativados quando o componente é desmontado.

- **Mapeamento dos ciclos de vida entre a API de opções e API de composição**
  - ~~`beforeCreate`~~ -> use `setup()`
  - ~~`created`~~ -> use `setup()`
  - `beforeMount` -> `onBeforeMount`
  - `mounted` -> `onMounted`
  - `beforeUpdate` -> `onBeforeUpdate`
  - `updated` -> `onUpdated`
  - `beforeUnmount` -> `onBeforeUnmount`
  - `unmounted` -> `onUnmounted`
  - `errorCaptured` -> `onErrorCaptured`
  - `renderTracked` -> `onRenderTracked`
  - `renderTriggered` -> `onRenderTriggered`

- **Veja também**: [Gatilhos de ciclo de vida da API de composição](../guide/composition-api-lifecycle-hooks.html)

## Provide / Inject

`provide` e `inject` ativam a injeção de dependência. Ambos só podem ser chamados durante o [`setup()`](#setup) com a instância atual ativa.

- **Tipagem**:

```ts
interface InjectionKey<T> extends Symbol {}

function provide<T>(key: InjectionKey<T> | string, value: T): void

// Sem o valor padrão
function inject<T>(key: InjectionKey<T> | string): T | undefined
// Com o valor padrão
function inject<T>(key: InjectionKey<T> | string, defaultValue: T): T
```

O Vue fornece uma interface `InjectionKey` que é um tipo genérico que estende de `Symbol`. Isso pode ser usado para sincronizar  o tipo do valor injetado entre o fornecedor e consumidor:

```ts
import { InjectionKey, provide, inject } from 'vue'

const key: InjectionKey<string> = Symbol()

provide(key, 'foo') // Fornecer um valor diferente de string resultará em um erro.

const foo = inject(key) // type of foo: string | undefined
```

Se estiver usando chaves do tipo *string* ou *symbols* não tipados, o tipo do valor injetado precisará ser declarado explicitamente:

```ts
const foo = inject<string>('foo') // string | undefined
```

- **Veja também**:
  - [Provide / Inject](../guide/component-provide-inject.html)
  - [API de Composição Provide / Inject](../guide/composition-api-provide-inject.html)

## `getCurrentInstance`

O `getCurrentInstance` permite acesso a uma instância de componente interno, útil para usos mais avançados ou para criadores de bibliotecas.

```ts
import { getCurrentInstance } from 'vue'

const MyComponent = {
  setup() {
    const internalInstance = getCurrentInstance()

    internalInstance.appContext.config.globalProperties // acesso para globalProperties
  }
}
```

`getCurrentInstance` **apenas** funciona durante o [setup](#setup) ou [Gatilhos de Ciclo de vida](#lifecycle-hooks)

> Quando usado fora do [setup](#setup) ou [Gatilhos de Ciclo de vida](#lifecycle-hooks), chame o `getCurrentInstance()` no `setup` e use a instância em seu lugar.

```ts
const MyComponent = {
  setup() {
    const internalInstance = getCurrentInstance() // Funciona

    const id = useComponentId() // Funciona

    const handleClick = () => {
      getCurrentInstance() // Não funciona
      useComponentId() // Não funciona

      internalInstance // Funciona
    }

    onMounted(() => {
      getCurrentInstance() // Funciona
    })

    return () =>
      h(
        'button',
        {
          onClick: handleClick
        },
        `uid: ${id}`
      )
  }
}

// Também funciona se chamado em uma composição
function useComponentId() {
  return getCurrentInstance().uid
}
```
