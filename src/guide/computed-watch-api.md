# Computado e Observador

> Esta secção usa a sintaxe de [Componentes Single File](../guide/single-file-component.html) para exemplos de código.

## `computado`

Pega uma função getter e retorna um objecto [ref](./refs-api.html#ref) reativo imútavel para o valor retornado do getter.

```js
const count = ref(1)
const plusOne = computed(() => count.value + 1)

console.log(plusOne.value) // 2

plusOne.value++ // erro
```

Como alternativa, ele pode usar um objeto com as funções `get` e `set` para criar um objecto ref gravável.

```js
const count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: val => {
    count.value = val - 1
  }
})

plusOne.value = 1
console.log(count.value) // 0
```

**Digitando:**

```ts
// somente leitura
function computed<T>(getter: () => T): Readonly<Ref<Readonly<T>>>

// gravável
function computed<T>(options: { get: () => T; set: (value: T) => void }): Ref<T>
```

## `watchEffect`

Executa uma função imediatamente enquanto rastreia reativamente suas dependências e a executa novamente sempre que as dependências são alteradas.

```js
const count = ref(0)

watchEffect(() => console.log(count.value))
// -> logs 0

setTimeout(() => {
  count.value++
  // -> logs 1
}, 100)
```

**Digitando:**

```ts
function watchEffect(
  effect: (onInvalidate: InvalidateCbRegistrator) => void,
  options?: WatchEffectOptions
): StopHandle

interface WatchEffectOptions {
  flush?: 'pre' | 'post' | 'sync' // default: 'pre'
  onTrack?: (event: DebuggerEvent) => void
  onTrigger?: (event: DebuggerEvent) => void
}

interface DebuggerEvent {
  effect: ReactiveEffect
  target: any
  type: OperationTypes
  key: string | symbol | undefined
}

type InvalidateCbRegistrator = (invalidate: () => void) => void

type StopHandle = () => void
```

**Veja também**: [Guia `watchEffect`](../guide/reactivity-computed-watchers.html#watcheffect)

## `watch`

A API `watch` é o equivalente exato da API de opções [this.\$Watch](./instance-methods.html#watch) (e a opção [watch](./options-data.html#watch) correspondente ). `watch` requer a observação de uma fonte de dados específica e aplica efeitos colaterais em uma função de retorno de chamada separada. Também é preguiçoso por padrão - ou seja, o retorno de chamada só é chamado quando a fonte monitorada é alterada.

- Comparado com [watchEffect](#watcheffect), `watch` nos permite:

  - Execute o efeito colateral preguiçosamente;
  - Seja mais específico sobre qual estado deve fazer com que o observador seja executado novamente;
  - Acesse o valor anterior e o atual do estado observado.

### Observando uma única fonte

Uma fonte de dados do inspetor pode ser uma função getter que retorna um valor ou diretamente um [ref](./refs-api.html#ref):

```js
// assistindo um getter
const state = reactive({ count: 0 })
watch(
  () => state.count,
  (count, prevCount) => {
    /* ... */
  }
)

// assistindo diretamente um ref
const count = ref(0)
watch(count, (count, prevCount) => {
  /* ... */
})
```

### Observando a várias fontes

Um observador também pode assistir a várias fontes ao mesmo tempo usando uma matriz:

```js
watch([fooRef, barRef], ([foo, bar], [prevFoo, prevBar]) => {
  /* ... */
})
```

### Comportamento compartilhado com `watchEffect`

`watch` compartilha o comportamento [`watchEffect`](#watcheffect) em termos de [interrupção manual](../guide/reactivity-computed-watchers.html#stopping-the-watcher), [invalidação de efeito colateral](../guide/reactivity-computed-watchers.html#side-effect-invalidation) (com `onInvalidate` passado para o retorno de chamada como o terceiro argument), [tempo de libertação](../guide/reactivity-computed-watchers.html#effect-flush-timing) and [depuração](../guide/reactivity-computed-watchers.html#watcher-debugging).

**Digitando:**

```ts
// observando uma única fonte
function watch<T>(
  source: WatcherSource<T>,
  callback: (
    value: T,
    oldValue: T,
    onInvalidate: InvalidateCbRegistrator
  ) => void,
  options?: WatchOptions
): StopHandle

// Observando a várias fontes
function watch<T extends WatcherSource<unknown>[]>(
  sources: T
  callback: (
    values: MapSources<T>,
    oldValues: MapSources<T>,
    onInvalidate: InvalidateCbRegistrator
  ) => void,
  options? : WatchOptions
): StopHandle

type WatcherSource<T> = Ref<T> | (() => T)

type MapSources<T> = {
  [K in keyof T]: T[K] extends WatcherSource<infer V> ? V : never
}

// veja a digitação `watchEffect` para opções compartilhadas
interface WatchOptions extends WatchEffectOptions {
  immediate?: boolean // padrão: false
  deep?: boolean
}
```

**Veja também**: [Guia `watch`](../guide/reactivity-computed-watchers.html#watch)
