---
badges:
  - breaking
---

# Componentes Funcionais <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral  

Sobre o que mudou, em visão geral:

- O ganho de performance com componentes funcionais na versão 2.x, se tornou insignificante na versão 3.x, sendo recomendado o uso de componentes com estado
- Componentes Funcionais só podem ser criados com uma função simples que recebe `props` e `context` (i.e., `slots`, `attrs`, `emit`)
- **BREAKING:** O atributo `functional` na tag `<template>` em componentes single-file (SFC) foi removido
- **BREAKING:** A opção `{ functional: true }` em componentes criados por funções foi removida

Para mais informações, continue lendo!

## Introdução

No Vue 2, componentes funcionais tinham dois casos de uso primários:

- Como otimização de performance, pois inicializavam muito mais rápido que componentes com estado
- Para retornar múltiplos nós raiz (root nodes)

Porém, no Vue 3, a performance de componentes com estado melhorou, a ponto de a diferença ser insignificante. Além disso, componentes com estado agora também incluem a possibilidade de retornar multiplos nós raiz (root nodes).

Como resultado, o único caso de uso restante para componentes funcionais é componentes simples, como um componente para heading dinâmico (dynamic heading). Caso contrário, é recomendável usar componentes com estado, como você normalmente faria.

## Sintaxe 2.x

Considerando o componente `<dynamic-heading>`, que é responsável por renderizar o heading apropriado (i.e., `h1`, `h2`, `h3`, etc.), poderia ser escrito como um componente single-file na versão 2.x, da seguinte forma:

```js
// Exemplo de Componente Funcional no Vue 2
export default {
  functional: true,
  props: ['level'],
  render(h, { props, data, children }) {
    return h(`h${props.level}`, data, children)
  }
}
```

Ou, para aqueles que preferem a tag `<template>` em um componente single-file:

```js
// Exemplo de Componente Funcional com tag <template> no Vue 2
<template functional>
  <component
    :is="`h${props.level}`"
    v-bind="attrs"
    v-on="listeners"
  />
</template>

<script>
export default {
  props: ['level']
}
</script>
```

## Sintaxe 3.x

### Componentes Criados por Funções

Agora no Vue 3, todos os componentes funcionais são criados com uma função simples. Em outras palavras, não há mais a necessidade de definir a opção `{ functional: true }` no componente.

As funções receberão dois argumentos: `props` e `context`. O argumento `context` é um objeto que contem as propriedados `attrs`, `slots`, e `emit` de um componente.

Além disso, ao invés de fornecer implicitamente o argumento `h` em uma função de `renderização`, `h` agora é importado globalmente.

Usando o exemplo mencionado previamente de um componente `<dynamic-heading>`, aqui está como será na nova versão:

```js
import { h } from 'vue'

const DynamicHeading = (props, context) => {
  return h(`h${props.level}`, context.attrs, context.slots)
}

DynamicHeading.props = ['level']

export default DynamicHeading
```

### Componentes Single File (SFCs)

Na versão 3.x, a diferença de performance entre componentes funcionais e de estado foi reduzida drasticamente, e será insignificante na maioria dos casos. Como resultado, o caminho de migração para desenvolvedores usando o atributo `functional` em componentes single-page é remover o atributo e renomear as referencias de `props` para `$props` e `attrs` para `$attrs`.

Usando o exemplo `<dynamic-heading>` de antes, aqui está como será na nova versão:

```js{1}
<template>
  <component
    v-bind:is="`h${$props.level}`"
    v-bind="$attrs"
  />
</template>

<script>
export default {
  props: ['level']
}
</script>
```

As principais diferenças são:

1. Atributo `functional` removido da tag `<template>`
1. `listeners` agora são passados como parte de `$attrs` e pode ser removido

## Próximos Passos

Para mais informação sobre o uso dos novos componentes funcionais e as mudanças nas "funções de renderização" em geral, acesse:

- [Migration: Render Functions](/guide/migration/render-function-api.html)
- [Guide: Render Functions](/guide/render-function.html)
