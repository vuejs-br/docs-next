---
badges:
  - breaking
---

# API da Função de Renderização <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

Esta mudança não vai afetar usuários de `<template>`.

Aqui está um rápido resumo do que mudou:

- `h` agora é globalmente importado em vez de passado como argumento para funções de renderização
- Argumentos da função render mudaram para ser mais consistentes entre componentes funcionais e com estado
- VNodes agora têm uma estrutura plana de props


Para mais informações, continue lendo!

## Argumento da Função Render

### Sintaxe 2.x

Na versão 2.x, a função `render` receberia automaticamente a função `h` (que é um _alias_ para `createElement`) como argumento: 

```js
// Exemplo da Função Render no Vue 2
export default {
  render(h) {
    return h('div')
  }
}
```

### Sintaxe 3.x

Na versão 3.x, o `h` agora é importado globalmente em vez de automaticamente passado como um argumento.


```js
// Exemplo da Função Render no Vue 3
import { h } from 'vue'

export default {
  render() {
    return h('div')
  }
}
```
## Mudança da assinatura da função Render

### Sintaxe 2.x

Na versão 2.x, a função `render` recebeu automaticamente argumentos como o `h`.

```js
// Exemplo da Função Render no Vue 2
export default {
  render(h) {
    return h('div')
  }
}
```
### Sintaxe 3.x

Na versão 3.x, desde que a função `render` já não recebe argumentos, ela será primeiramente usada dentro da função `setup()`. Isso traz o benefício adicional em ganhar acesso para o estado reativo e às funções declaradas no escopo, assim como argumentos passados para o `setup()`.


```js
import { h, reactive } from 'vue'

export default {
  setup(props, { slots, attrs, emit }) {
    const state = reactive({
      count: 0
    })

    function increment() {
      state.count++
    }

    // retorna a função render
    return () =>
      h(
        'div',
        {
          onClick: increment
        },
        state.count
      )
  }
}
```
Para mais informações em como o `setup()` funciona, veja nosso [Guia da API de Composição](/guide/composition-api-introduction.html).

## Formato de Props VNode

### Sintaxe 2.x

Na versão 2.x, o `domProps` continha uma lista aninhada dentro dos props VNode:

```js
// 2.x
{
  staticClass: 'button',
  class: {'is-outlined': isOutlined },
  staticStyle: { color: '#34495E' },
  style: { backgroundColor: buttonColor },
  attrs: { id: 'submit' },
  domProps: { innerHTML: '' },
  on: { click: submitForm },
  key: 'submit-button'
}
```

### 3.x Syntax

In 3.x, the entire VNode props structure is flattened. Using the example from above, here is what it would look like now.

```js
// 3.x Syntax
{
  class: ['button', { 'is-outlined': isOutlined }],
  style: [{ color: '#34495E' }, { backgroundColor: buttonColor }],
  id: 'submit',
  innerHTML: '',
  onClick: submitForm,
  key: 'submit-button'
}
```

## Registered Component

### 2.x Syntax

In 2.x, when a component has been registered, the render function would work well when passing the component's name as a string to the first argument:

```js
// 2.x
Vue.component('button-counter', {
  data() {
    return {
      count: 0
    }
  }
  template: `
    <button @click="count++">
      Clicked {{ count }} times.
    </button>
  `
})

export default {
  render(h) {
    return h('button-counter')
  }
}
```

### 3.x Syntax

In 3.x, with VNodes being context-free, we can no longer use a string ID to implicitly lookup registered components. Instead, we need to use an imported `resolveComponent` method:

```js
// 3.x
import { h, resolveComponent } from 'vue'

export default {
  setup() {
    const ButtonCounter = resolveComponent('button-counter')
    return () => h(ButtonCounter)
  }
}
```

For more information, see [The Render Function Api Change RFC](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0008-render-function-api-change.md#context-free-vnodes).

## Migration Strategy

### Library Authors

`h` being globally imported means that any library that contains Vue components will include `import { h } from 'vue'` somewhere. As a result, this creates a bit of overhead since it requires library authors to properly configure the externalization of Vue in their build setup:

- Vue should not be bundled into the library
- For module builds, the import should be left alone and be handled by the end user bundler
- For UMD / browser builds, it should try the global Vue.h first and fallback to require calls

## Next Steps

See [Render Function Guide](/guide/render-function) for more detailed documentation!
