# API's Básicas de Reatividade

> Esta seção usa sintaxe de [componente de arquivo único](../guide/single-file-component.html) para exemplos de código

## `reactive`

Retorna uma cópia reativa do objeto.

```js
const obj = reactive({ count: 0 })
```

A conversão reativa é "profunda" - ela afeta todas as propriedades aninhadas. Na implementação baseada em [ES2015 Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy), o proxy retornado **não** é igual ao objeto original. Recomenda-se trabalhar exclusivamente com o proxy reativo e evitar depender do objeto original.

**Digitando:**

```ts
function reactive<T extends object>(target: T): UnwrapNestedRefs<T>
```

## `readonly`

Captura um objeto (reativo ou simples) ou uma [ref](./refs-api.html#ref) e retorna um proxy somente leitura para o original. Um proxy somente leitura é profundo: qualquer propriedade aninhada acessada também será somente leitura

```js
const original = reactive({ count: 0 })

const copy = readonly(original)

watchEffect(() => {
  // realizando o rastreamento da reatividade
  console.log(copy.count)
})

// a mutação do "original" fará com que os observadores confiem na cópia
original.count++

// alterar a cópia irá falhar e resultará em um aviso
copy.count++ // warning!
```

## `isProxy`

Verifica se um objeto é um proxy criado por [`reactive`](#reactive) ou [`readonly`](#readonly).

## `isReactive`

Verifica se um objeto é um proxy reativo criado por [`reactive`](#reactive).

```js
import { reactive, isReactive } from 'vue'
export default {
  setup() {
    const state = reactive({
      name: 'John'
    })
    console.log(isReactive(state)) // -> true
  }
}
```

Ele também retorna `true` se o proxy é criado por [`readonly`](#readonly), mas está envolvendo outro proxy criado por [`reactive`](#reactive).

```js{7-15}
import { reactive, isReactive, readonly } from 'vue'
export default {
  setup() {
    const state = reactive({
      name: 'John'
    })
    // proxy "readonly"(somente de leitura) criado a partir de um objeto simples
    const plain = readonly({
      name: 'Mary'
    })
    console.log(isReactive(plain)) // -> false

    // proxy "readonly"(somente de leitura) criado a partir de um proxy "reactive"
    const stateCopy = readonly(state)
    console.log(isReactive(stateCopy)) // -> true
  }
}
```

## `isReadonly`

Verifica se um objeto é um proxy somente de leitura criado por [`readonly`](#readonly).

## `toRaw`

Retorna o objeto bruto original de um proxy [`reactive`](#reactive) ou [`readonly`](#readonly). Esta é uma "válvula de escape" que pode ser usada para ler temporariamente sem incorrer em acesso de proxy/sobrecarga de rastreamento ou gravação sem acionar alterações. **Não** é recomendado manter uma referência persistente ao objeto original. Use com cuidado

```js
const foo = {}
const reactiveFoo = reactive(foo)

console.log(toRaw(reactiveFoo) === foo) // true
```

## `markRaw`

Marca um objeto para que nunca seja convertido em proxy. Retorna o próprio objeto.

```js
const foo = markRaw({})
console.log(isReactive(reactive(foo))) // false

// também funciona quando aninhado dentro de outros objetos reativos
const bar = reactive({ foo })
console.log(isReactive(bar.foo)) // false
```

::: Atenção
`markRaw` e as APIs shallowXXX abaixo permitem que você desative seletivamente a conversão profunda reativa/somente de leitura padrão e incorpore objetos brutos e não proxy em seu gráfico de estado. Eles podem ser usados por vários motivos:

- Alguns valores simplesmente não devem se tornar reativos, por exemplo, uma instância complexa de classe de terceiros, ou um objeto de componente Vue.

- Ignorar a conversão de proxy pode fornecer melhorias de desempenho ao renderizar grandes listas com fontes de dados imutáveis.

Eles são considerados avançados porque a desativação bruta está apenas no nível raiz, portanto, se você definir um objeto bruto aninhado e não marcado em um objeto reativo e acessá-lo novamente, obterá a versão em proxy de volta. Isso pode levar a **riscos de identidade** - ou seja, realizar uma operação que depende da identidade do objeto, mas usando a versão bruta e a versão proxy do mesmo objeto:

```js
const foo = markRaw({
  nested: {}
})

const bar = reactive({
  // embora `foo` esteja marcado como bruto, foo.nested não está.
  nested: foo.nested
})

console.log(foo.nested === bar.nested) // false
```

Riscos de identidade são geralmente raros. No entanto, para utilizar adequadamente essas APIs e ao mesmo tempo evitar riscos de identidade, é necessário um conhecimento sólido de como funciona o sistema de reatividade.
:::

## `shallowReactive`

Cria um proxy reativo que rastreia a reatividade de suas próprias propriedades, mas não executa conversão reativa profunda de objetos aninhados (expõe valores brutos).

```js
const state = shallowReactive({
  foo: 1,
  nested: {
    bar: 2
  }
})

// as próprias propriedades do estado mutante são reativas
state.foo++
// ...mas não às converte em objetos aninhados
isReactive(state.nested) // false
state.nested.bar++ // não reativo
```

## `shallowReadonly`

Cria um proxy que torna suas próprias propriedades somente de leitura, mas não executa a conversão somente leitura de profunda de objetos aninhados (expõe valores brutos).

```js
const state = shallowReadonly({
  foo: 1,
  nested: {
    bar: 2
  }
})

// as próprias propriedades do estado mutante irão falhar
state.foo++
// ...mas funciona em objetos aninhados
isReadonly(state.nested) // false
state.nested.bar++ // funciona
```
