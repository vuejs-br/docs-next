---
badges:
  - breaking
---

# API da Função de Renderização <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

Esta mudança não vai afetar usuários de `<template>`.

Aqui está um rápido resumo do que mudou:

- `h` agora é globalmente importado em vez de passado como argumento para funções de renderização
- Argumentos da função de renderização mudaram para serem mais consistentes entre componentes funcionais e com estado
- VNodes agora têm uma estrutura plana de props

Para mais informações, continue lendo!

## Argumento da Função de Renderização

### Sintaxe v2.x

Na v2.x, a função `render` receberia automaticamente a função `h` (que é um _alias_ para `createElement`) como argumento: 

```js
// Exemplo da Função de Renderização no Vue 2
export default {
  render(h) {
    return h('div')
  }
}
```

### Sintaxe v3.x

Na v3.x, o `h` agora é importado globalmente em vez de automaticamente passado como um argumento.

```js
// Exemplo da Função de Renderização no Vue 3
import { h } from 'vue'

export default {
  render() {
    return h('div')
  }
}
```

## Mudança da Assinatura da Função de Renderização

### Sintaxe v2.x

Na v2.x, a função `render` recebeu automaticamente argumentos como o `h`.

```js
// Exemplo da Função Render no Vue 2
export default {
  render(h) {
    return h('div')
  }
}
```

### Sintaxe v3.x

Na v3.x, desde que a função `render` já não recebe argumentos, ela será primeiramente usada dentro da função `setup()`. Isso traz o benefício adicional em ganhar acesso ao estado reativo e às funções declaradas no escopo, assim como argumentos passados para o `setup()`.

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

    // retorna a função de renderização
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

### Sintaxe v2.x

Na v2.x, o `domProps` continha uma lista aninhada dentro dos props VNode:

```js
// v2.x
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

### Sintaxe v3.x

Na v3.x, toda a estrutura de props do VNode é achatada. Usando o exemplo acima, agora ele ficaria algo assim.

```js
// Sintaxe v3.x
{
  class: ['button', { 'is-outlined': isOutlined }],
  style: [{ color: '#34495E' }, { backgroundColor: buttonColor }],
  id: 'submit',
  innerHTML: '',
  onClick: submitForm,
  key: 'submit-button'
}
```

## Componente Registrado

### Sintaxe v2.x

Na v2.x, quando um componente é registrado, a função de renderização funcionaria bem quando passado o nome do componente como uma string para o primeiro argumento:

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
      Clicado {{ count }} vezes.
    </button>
  `
})

export default {
  render(h) {
    return h('button-counter')
  }
}
```

### Sintaxe v3.x

Na v3.x, com os VNodes sendo livres de contexto, não podemos usar um ID como string para implicitamente buscar por componentes registrados. Em vez disso, precisamos usar o método importado `resolveComponent`:

```js
// v3.x
import { h, resolveComponent } from 'vue'

export default {
  setup() {
    const ButtonCounter = resolveComponent('button-counter')
    return () => h(ButtonCounter)
  }
}
```

Para mais informações, veja [O RFC* das mudanças da API da Função de Renderização](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0008-render-function-api-change.md#context-free-vnodes).

* **Nota da tradução**: RFC significa _Request For Comments_ (Solicitação de Comentários), e é um processo que visa prover um caminho consistente e controlado para as novas funcionalidades de um framework.

## Estratégia de Migração

### Autores de Biblioteca

A importação global do `h` significa que qualquer biblioteca que contenha componentes Vue vão incluir `import { h } from 'vue'` em algum lugar. Como resultado, isso cria um pouco de sobrecarga, pois requer que os autores de biblioteca configurem corretamente a externalização do Vue em suas configurações de compilação:

- o Vue não deve estar empacotado na biblioteca
- Para construções de módulo, a importação deve ser deixada sozinha e tratada pelo empacotador do usuário final
- Para UMD / compilações de navegador, deve-se tentar o Vue.h global primeiro e alternar para exigir chamadas se necessário

## Próximos Passos

Veja [O Guia da Função de Renderização](/guide/render-function) para documentação mais detalhada!
