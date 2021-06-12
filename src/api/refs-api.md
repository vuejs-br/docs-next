# Refs

> Esta seção usa a sintaxe [single-file component](../guide/single-file-component.html) para os exemplos de código

## `ref`

Assume um valor interno e retorna um objeto "ref" reativo e mutável. O objeto "ref" tem uma única propriedade `.value` que aponta para o valor interno

**Exemplo:**

```js
const count = ref(0)
console.log(count.value) // 0

count.value++
console.log(count.value) // 1
```

Se um objeto for atribuído como um valor "ref", o objeto se tornará profundamente reativo pelo método [reativo](./basic-reactivity.html#reactive).

**Tipando:**

```ts
interface Ref<T> {
  value: T
}

function ref<T>(value: T): Ref<T>
```

Às vezes, podemos precisar especificar tipos complexos para o valor interno de um "ref". Podemos fazer isso de forma sucinta passando um argumento genérico ao chamar `ref` para substituir a inferência padrão:

```ts
const foo = ref<string | number>('foo') // tipo foo: Ref<string | number>

foo.value = 123 // ok!
```

Se o tipo da "generic" for desconhecido, é recomendado converter `ref` em` Ref<T>`:

```js
function useState<State extends string>(initial: State) {
  const state = ref(initial) as Ref<State> // state.value -> State estende string
  return state
}
```

## `unref`

Retorna o valor interno se o argumento for um [`ref`](#ref), caso contrário, retorna o próprio argumento. Esta é uma "sugar function" para `val = isRef(val) ? val.value : val`.

```js
function useFoo(x: number | Ref<number>) {
  const unwrapped = unref(x) // unwrapped é garantido ser number agora
}
```

## `toRef`

Pode ser usado para criar um [`ref`](#ref) para uma propriedade em um objeto reativo de origem. O "ref" pode então ser passado, mantendo a conexão reativa com sua propriedade de origem.

```js
const state = reactive({
  foo: 1,
  bar: 2
})

const fooRef = toRef(state, 'foo')

fooRef.value++
console.log(state.foo) // 2

state.foo++
console.log(fooRef.value) // 3
```

`toRef` é útil quando você deseja passar o "ref" de um objeto para uma função de composição:

```js
export default {
  setup(props) {
    useSomeFeature(toRef(props, 'foo'))
  }
}
```

## `toRefs`

Converte um objeto reativo em um objeto simples onde cada propriedade do objeto resultante é um [`ref`](#ref) apontando para a propriedade correspondente do objeto original.

```js
const state = reactive({
  foo: 1,
  bar: 2
})

const stateAsRefs = toRefs(state)
/*
Tipo de stateAsRefs:

{
  foo: Ref<number>,
  bar: Ref<number>
}
*/

// O ref e a propriedade original estão "vinculados"
state.foo++
console.log(stateAsRefs.foo.value) // 2

stateAsRefs.foo.value++
console.log(state.foo) // 3
```

`toRefs` é útil ao retornar um objeto reativo de uma função de composição para que o componente de consumo possa desestruturar/espalhar o objeto retornado sem perder a reatividade:

```js
function useFeatureX() {
  const state = reactive({
    foo: 1,
    bar: 2
  })

  // lógica operando no estado

  // converter para "refs" ao retornar
  return toRefs(state)
}

export default {
  setup() {
    // pode-se desestruturar sem perder a reatividade
    const { foo, bar } = useFeatureX()

    return {
      foo,
      bar
    }
  }
}
```

## `isRef`

Verifica se um valor é um objeto "ref".

## `customRef`

Cria uma "ref" personalizada com controle explícito sobre seu rastreamento de dependência e acionamento de atualizações. Ele espera uma função de fábrica, que recebe as funções `track` e` trigger` como argumentos e deve retornar um objeto com `get` e` set`.

- Exemplo usando uma "ref" personalizada para implementar debounce (prática que visa diminuir a quantidade de eventos disparados) com `v-model`:

  ```html
  <input v-model="text" />
  ```

  ```js
  function useDebouncedRef(value, delay = 200) {
    let timeout
    return customRef((track, trigger) => {
      return {
        get() {
          track()
          return value
        },
        set(newValue) {
          clearTimeout(timeout)
          timeout = setTimeout(() => {
            value = newValue
            trigger()
          }, delay)
        }
      }
    })
  }

  export default {
    setup() {
      return {
        text: useDebouncedRef('hello')
      }
    }
  }
  ```

**Tipando:**

```ts
function customRef<T>(factory: CustomRefFactory<T>): Ref<T>

type CustomRefFactory<T> = (
  track: () => void,
  trigger: () => void
) => {
  get: () => T
  set: (value: T) => void
}
```

## `shallowRef`

Cria uma "ref" que rastreia sua própria mutação `.value`, mas não torna seu valor reativo.

```js
const foo = shallowRef({})
// mutating the ref's value is reactive
foo.value = {}
// but the value will not be converted.
isReactive(foo.value) // false
```

**Veja também**: [Criação de valores reativos autônomos como `refs`](../guide/reactivity-fundamentals.html#creating-standalone-reactive-values-as-refs)

## `triggerRef`

Execute quaisquer efeitos vinculados a um [`shallowRef`](#shallowref) manualmente.

```js
const shallow = shallowRef({
  greet: 'Olá, mundo!'
})

// Mostra "Olá, mundo!" uma vez para a primeira execução
watchEffect(() => {
  console.log(shallow.value.greet)
})

// Isso não acionará o efeito porque o "ref" é shallow
shallow.value.greet = 'Olá, universo!'

// Mostra "Olá, universo!"
triggerRef(shallow)
```

**Veja também:** [Computado e Assistido - watchEffect](./computed-watch-api.html#watcheffect)