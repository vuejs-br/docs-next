---
title: Precedência de v-if vs. v-for
badges:
  - breaking
---

# {{ $frontmatter.title }} <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

- **QUEBRA**: Se usado no mesmo elemento, `v-if` terá uma maior precedência do que `v-for`

## Introdução

Duas das diretivas mais comumente usadas em Vue.js são `v-if` e `v-for`. Portanto, não é surpresa que chegue um momento em que os desenvolvedores desejem usar os dois juntos. Embora não seja uma prática recomendada, pode haver momentos em que isso seja necessário, por isso gostaríamos de fornecer orientações sobre como isso funciona.

## Sintaxe v2.x

Na v2.x, quando utilizado `v-if` e `v-for` no mesmo elemento, `v-for` teria precedência.

## Sintaxe v3.x

Na v3.x, `v-if` terá sempre uma maior precedência do que `v-for`.

## Estratégia de Migração

Recomenda-se evitar o uso de ambos no mesmo elemento devido à ambiguidade da sintaxe.

Em vez de gerenciar isso à nível de _template_, um método para realizar isso é criar um dado computado (computed property) que filtre uma lista para os elementos visíveis.
