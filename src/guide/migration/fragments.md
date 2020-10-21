---
badges:
  - new
---

# Fragmentos <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

No Vue 3, os componentes agora tem suporte oficial para componentes com nós multi-raiz, ou seja, fragmentos!

## Sintaxe v2.x

Na v2.x, componentes multi-raiz não eram suportados e iriam emitir um aviso no caso de um usuário acidentalmente criar um. Como resultado, vários componentes são envolvidos em uma única `<div>` de forma a corrigir esse erro.

```html
<!-- Layout.vue -->
<template>
  <div>
    <header>...</header>
    <main>...</main>
    <footer>...</footer>
  </div>
</template>
```

## Sintaxe v3.x

Na v3.x, componentes agora podem ter vários nós raiz! No entanto, isso requer aos desenvolvedores definir explicitamente onde os atributos deveriam ser distribuídos.

```html
<!-- Layout.vue -->
<template>
  <header>...</header>
  <main v-bind="$attrs">...</main>
  <footer>...</footer>
</template>
```

Para mais informações sobre como a herança de atributos funciona, veja [Atributos Não-Propriedades](/guide/component-attrs.html).
