---
badges:
  - breaking
---

# Unificação de Slots <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

Esta modificação unifica _slots_ normais e _slots_ com escopo na v3.x.

Aqui um pequeno sumário sobre o que mudou:

- `this.$slots` agora exibe os _slots_ como funções
- **QUEBRA**: `this.$scopedSlots` foi removido

Para mais informações, continue lendo!

## Sintaxe v2.x

Ao utilizar uma função de renderização, como `h`, a v2.x definia a propriedade `slot` nos nós do conteúdo.

```js
// Sintaxe v2.x
h(LayoutComponent, [
  h('div', { slot: 'header' }, this.header),
  h('div', { slot: 'content' }, this.content)
])
```

Além disso, ao referenciar _slots_ com escopo definido, eles poderiam ser referenciados usando a seguinte sintaxe:

```js
// Sintaxe v2.x
this.$scopedSlots.header
```

## Sintaxe v3.x

Na v3.x, os _slots_ são definidos como um objeto, filhos do nó atual:

```js
// Sintaxe v3.x
h(LayoutComponent, {}, {
  header: () => h('div', this.header),
  content: () => h('div', this.content)
})
```

E quando você precisar referenciar _slots_ com escopo programaticamente, estes agora estão unificados na opção `$slots`.

```js
// Sintaxe v2.x
this.$scopedSlots.header

// Sintaxe v3.x
this.$slots.header()
```

## Estratégia de Migração

A maioria das alterações já estão presentes na versão 2.6. Desta forma, a migração pode acontecer em uma etapa:

1. Susbstitua todas as ocorrências de `this.$scopedSlots` por `this.$slots` na v3.x.
2. Susbstitua todas as ocorrências de `this.$slots.mySlot` por `this.$slots.mySlot()`.
