---
title: Comportamento da Combinação do v-bind
badges:
  - breaking
---

# {{ $frontmatter.title }} <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

- **BREAKING**: A ordem das vinculações para v-bind irá afetar o resultado da renderização.

## Visão Geral

Quando se está dinamicamente vinculando atributos em um elemento, um cenário comum envolve utilizar tanto a sintaxe `v-bind="object"` quanto as propriedades indiviuais no mesmo elemento. Contudo, isso gera questões tal como a prioridade na combinação.

## Sintaxe 2.x

No 2.x, se um elemento tem tanto o `v-bind="object"` quanto uma propriedade individual idêntica definida, a propriedade individual sempre vai sobrescrever as vinculações do `objeto`. 

```html
<!-- template -->
<div id="red" v-bind="{ id: 'blue' }"></div>
<!-- resultado -->
<div id="red"></div>
```

## Sintaxe 3.x

No 3x, se um elemento tem tanto o `v-bind="object"` quanto uma propriedade individual idêntica definida, a ordem de como as vinculações são declaradas determina como elas serão combinadas. Em outras palavras, em vez de assumir que os desenvolvedores querem que a propriedade individual sempre sobrescreva o que está definido no `objeto`, agora os desenvolvedores terão mais controle sobre qual o comportamento desejado na combinação.

```html
<!-- template -->
<div id="red" v-bind="{ id: 'blue' }"></div>
<!-- resultado -->
<div id="blue"></div>

<!-- template -->
<div v-bind="{ id: 'blue' }" id="red"></div>
<!-- resultado -->
<div id="red"></div>
```

## Estratégia de Migração

Se você está If you are confiando nessa funcionalidade de sobrescrita do `v-bind`, nós atualmente recomendamos você a garantir que seu atributo `v-bind` attribute seja definido sempre antes das propriedades individuais.
