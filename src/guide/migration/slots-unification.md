---
badges:
  - breaking
---

# Unificação de Slots <MigrationBadges :badges="$frontmatter.badges" />

## Visão geral

Esta modificação unifica tanto _slots_ normais quanto _slots_ `scoped` em 3.x.

Aqui um pequeno sumário sobre o que mudou:

- `this.$slots` agora exibe os _slots_ como funções
- **NOVIDADE**: `this.$scopedSlots` foi removido

Para mais informações, continue lendo!

## Sintaxe 2.x

Ao utilizar uma função de renderização, como, `h`, 2.x definia a propriedade `slot` no conteúdo do nó.

```js
// Sintaxe 2.x
h(LayoutComponent, [
  h('div', { slot: 'header' }, this.header),
  h('div', { slot: 'content' }, this.content)
])
```

Além disso, ao fazer referência a _slots_ com escopo definido, eles podem ser referenciados usando a seguinte sintaxe:

```js
// Sintaxe 2.x
this.$scopedSlots.header
```

## Sintaxe 3.x

No 3.x, os _slots_ são definidos como um objeto, filhos do nó atual:

```js
// Sintaxe 3.x
h(LayoutComponent, {}, {
  header: () => h('div', this.header),
  content: () => h('div', this.content)
})
```

E quando você precisar referenciar _slots_ com escopo programaticamente, estes agora estão unificados na opção `$slots`.

```js
// Sintaxe 2.x
this.$scopedSlots.header

// Sintaxe 3.x
this.$slots.header()
```

## Estratégia de Migração

A maioria das alterações já estão presentes na versão 2.6. Desta forma, a migração pode acontecer em uma etapa:

1. Susbstituir todas as ocorrências de `this.$scopedSlots` por `this.$slots` em 3.x.
2. Susbstituir todas as ocorrências de `this.$slots.mySlot` por `this.$slots.mySlot()`.
