# Configuração

> Esta seção usa a sintaxe de [componente single file](single-file-component.html) para exemplos de código.

> Este guia assume que você já leu a [Introdução à API de Composição](composition-api-introduction.html) e [Fundamentos da Reatividade](reactivity-fundamentals.html). Leia-os primeiro se você é novo em API de composição (Composition API).

## Argumentos 

Ao usar a função `setup`, ela receberá dois argumentos:

1. `props`
2. `context`

Vamos nos aprofundar em como cada argumento pode ser usado.

### `props`

O primeiro argumento da função `setup` é o argumento `props`. Assim como é de se esperar em um componente padrão, `props` dentro de uma função `setup` são reativos e serão atualizados quando novos props são passados.

```js
// MyBook.vue

export default {
  props: {
    title: String
  },
  setup(props) {
    console.log(props.title)
  }
}
```
:::warning Atenção
No entanto, porque os `props` são reativos, você **não pode usar a desestruturação de objetos do ES6** porque isso vai remover sua reatividade.
:::

Se você precisa desestruturar seus props, você o pode fazer seguramente usando o [toRefs](reactivity-fundamentals.html#desestruturar-estado-reativo) dentro da função `setup`.

```js
// MyBook.vue

import { toRefs } from 'vue'

setup(props) {
	const { title } = toRefs(props)

	console.log(title.value)
}
```

### `context`

O segundo argumento passado para a função `setup` é o `context`. O `context` é um objeto Javascript normal que expõe três propriedades do componente:

```js
// MyBook.vue

export default {
  setup(props, context) {
    // Atributos (objeto não reativo)
    console.log(context.attrs)

    // Slots (objeto não reativo)
    console.log(context.slots)

    // Emissão de Eventos (método)
    console.log(context.emit)
  }
}
```

O objeto `context` é um objeto normal Javascript, ou seja, não é reativo, o que significa que você pode seguramente utilizar a desestruturação ES6 nele.

```js
// MyBook.vue
export default {
  setup(props, { attrs, slots, emit }) {
    ...
  }
}
```

`attrs` e `slots` são objetos com estados que sempre são atualizados quando o componente é atualizado. Isso significa que você deve evitar desestruturá-los e sempre referenciar suas propriedades por meio de `attrs.x` ou `slots.x`. Note também que, diferente de `props`, `attrs` e `slots` **não** são reativos. Se você busca aplicar efeitos baseados em mudanças nos `attrs` ou `slots`, você deveria fazê-lo dentro do hook de ciclo de vida `onUpdate`.

## Acessando Propriedades de Componentes

Quando o `setup` é executado, a instância do componente ainda não foi criada. Como resultado, você somente poderá acessar as seguintes propriedades:

- `props`
- `attrs`
- `slots`
- `emit`

Em outras palavras, você **não terá acesso** às seguintes opções de componentes:

- `data`
- `computed`
- `methods`

## Uso com _Templates_

Se `setup` retorna um objeto, as propriedades no objeto podem ser acessadas no _template_ do componente, assim como as propriedades do `props` passadas para o `setup`:

```vue-html
<!-- MyBook.vue -->
<template>
  <div>{{ collectionName }}: {{ readersNumber }} {{ book.title }}</div>
</template>

<script>
  import { ref, reactive } from 'vue'

  export default {
    props: {
      collectionName: String
    },
    setup(props) {
      const readersNumber = ref(0)
      const book = reactive({ title: 'Guia do Vue 3' })

      // expor ao template
      return {
        readersNumber,
        book
      }
    }
  }
</script>
```

Note que o [refs](../api/refs-api.html#ref) que é retornado do `setup` é [automaticamente desempacotado](/guide/reactivity-fundamentals.html#ref-desempacotada) quando acessado no _template_, portanto você não deveria usar `.value` nos _templates_.

## Uso com Funções de Renderização

`setup` pode, inclusive, retornar uma função de renderização que pode diretamente fazer uso do estado reativo declarado no mesmo escopo:

```js
// MyBook.vue

import { h, ref, reactive } from 'vue'

export default {
  setup() {
    const readersNumber = ref(0)
    const book = reactive({ title: 'Guia do Vue 3' })
    // Por favor, note que precisamos explicitamente expor o valor de ref aqui
    return () => h('div', [readersNumber.value, book.title])
  }
}
```

## Uso do `this`

**Dentro de `setup()`, `this` não será uma referência à instância atualmente ativa**. Já que `setup()` é chamado antes que as outras opções do componente sejam resolvidas, `this` dentro de `setup()` vai se comportar diferentemente do `this` de outras opções. Isso pode causar confusões ao se usar o `setup()` com outras API de Opções. 
