# Configuração

> Esta seção usa a sintaxe de [componente de um arquivo](single-file-component.html) para exemplos de código.

> Este guia assume que você já leu a [Introdução à API de Composição](composition-api-introduction.html) e [Fundamentos de Reatividade](reactivity-fundamentals.html). Leia-os primeiros se você é novo à API de composição (Composition API).

## Argumentos 

Ao usar a função `setup`, ela receberá dois argumentos:

1. `props`
2. `context`
   
Vamos nos aprofundar em como cada argumento pode ser usado.

### Props

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
:::warning
No entanto, porque os `props` são reativos, você **não pode usar desestruturação de objetos com ES6** porque isso vai remover sua reatividade.
:::

Se você precisa desestruturar seus props, você o pode fazer seguramente usando o [toRefs](reactivity-fundamentals.html#destructuring-reactive-state) dentro da função `setup`.

```js
// MyBook.vue

import { toRefs } from 'vue'

setup(props) {
	const { title } = toRefs(props)

	console.log(title.value)
}
```
### Contexto 

O segundo argumento passado para a função `setup` é o `contexto`. O `contexto` é um objeto Javascript normal que expõe três propriedades do componente:


```js
// MyBook.vue

export default {
  setup(props, context) {
    // Attributes (Non-reactive object)
    console.log(context.attrs)

    // Slots (Non-reactive object)
    console.log(context.slots)

    // Emit Events (Method)
    console.log(context.emit)
  }
}
```

O objeto `contexto` é um objeto normal Javascript (ou seja, não é reativo, o que significa que você pode seguramente utilizar a desestruturação ES6 nele)

```js
// MyBook.vue
export default {
  setup(props, { attrs, slots, emit }) {
    ...
  }
}
```
`attrs` e `slots` são objetos com estados que sempre são atualizados quando o componente é atualizado. Isso significa que você deve evitar desestruturá-los e sempre referenciar suas propriedades por meio de `attrs.x` ou `slots.x`. Note também que, diferente de `props`, `attrs` e `slots` não são reativos. Se você busca aplicar efeitos baseados em mudanças nos `attrs` ou `slots`, você deveria fazê-lo dentro do hook de ciclo de vida `onUpdate`.

## Accessando propriedades de componentes

Quando o `setup` é executado, a instância do componente ainda não foi criada. Como resultado, você somente poderá acessar as seguintes propriedades:

- `props`
- `attrs`
- `slots`
- `emit`

Em outras palavras, você **não terá acesso** às seguintes opções de componentes:

- `data`
- `computed`
- `methods`

## Usos com os Templates

Se `setup` retorna um objeto, as propriedades no objeto podem ser acessadas no template do componente, assim como as propriedades do `props` passadas para o `setup`:

If `setup` returns an object, the properties on the object can be accessed in the component's template, as well as the properties of the `props` passed into `setup`:

```vue-html
<!-- MyBook.vue -->
<template>
  <div>{{ nomeDaColecao }}: {{ numeroDeLeitores }} {{ livro.titulo }}</div>
</template>

<script>
  import { ref, reactive } from 'vue'

  export default {
    props: {
      nomeDaColecao: String
    },
    setup(props) {
      const numeroDeLeitores = ref(0)
      const livro = reactive({ titulo: 'Vue 3 Guide' })

      // expose to template
      return {
        numeroDeLeitores,
        livro
      }
    }
  }
</script>
```
Note que [refs](../api/refs-api.html#ref) que é retornado do `setup` é [automaticamente desestruturado](/guide/reactivity-fundamentals.html#ref-unwrapping) quando acessado no template, portanto você não deveria usar `.value` nos templates.

## Uso com funções de renderização

`setup` pode, inclusive, retornar uma função de renderização que pode diretamente fazer uso do estado reativo declarado no mesmo escopo:

```js
// MyBook.vue

import { h, ref, reactive } from 'vue'

export default {
  setup() {
    const numeroDeLeitores = ref(0)
    const livro = reactive({ titulo: 'Vue 3 Guide' })
    // Por favor, note que precisamos explicitamente expor o valor de ref aqui
    return () => h('div', [readersNumber.value, book.titulo])
  }
}
```
# Uso de `this`

**Dentro de `setup()`, `this` não será uma referência à instância atualmente ativa**. Já que `setup()` é chamado antes que as outras opções do componente são resolvidas, `this` dentro de `setup()` vai se comportar diferentemente do `this` noutras opções. Isso pode causar confusões ao se usar o `setup()` com outras API de Opções. 
