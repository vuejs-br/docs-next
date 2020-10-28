# Introdução

::: info 
Novo em Vue.js? Veja nosso [Guia Essencial](/guide/introduction.html) para começar. 
:::

Este guia é sobretudo para usuários com experiência prévia em Vue 2 que desejam aprender sobre os novos recursos e mudanças do Vue 3. **Este não é um artigo que você precise ler detalhadamente antes de experimentar o Vue 3.** Embora pareça que muita coisa mudou, muito do que você conhece e ama no Vue ainda é o mesmo; mas queríamos ser o mais completos possível fornecendo explicações detalhadas e exemplos para cada alteração documentada. 

- [Início rápido](#quickstart)
- [Principais recursos novos](#notable-new-features)
- [Incompatibilidades](#breaking-changes)
- [Bibliotecas suportadas](#supporting-libraries)

## Visão geral

<br>
<iframe src="https://player.vimeo.com/video/440868720" width="640" height="360" frameborder="0" allow="autoplay; fullscreen" allowfullscreen></iframe>

Começe a aprender Vue 3 com [Vue Mastery](https://www.vuemastery.com/courses-path/vue3).

## Início rápido

- Via CDN: `<script src="https://unpkg.com/vue@next"></script>`
- Pelo navegador, via _playground_ em [Codepen](https://codepen.io/yyx990803/pen/OJNoaZL)
- Pelo navegador, via Sandbox em [CodeSandbox](https://v3.vue.new)
- Via plataforma [Vite](https://github.com/vitejs/vite):

  ```bash
  npm init vite-app hello-vue3 # OU yarn create vite-app hello-vue3
  ```

- Via plataforma [vue-cli](https://cli.vuejs.org/):

  ```bash
  npm install -g @vue/cli # OU yarn global add @vue/cli
  vue create hello-vue3
  # selecione vue3 como pré-definição
  ```

## Principais recursos novos

Alguns dos recursos novos para ficar de olho no Vue 3 incluem:

- [_Composition_ API](/guide/composition-api-introduction.html)
- [Teleporte](/guide/teleport.html)
- [Fragmentos](/guide/migration/fragments.html)
- [Opções para _emit_ entre componentes](/guide/component-custom-events.html)
- [API `createRenderer` de `@vue/runtime-core`](https://github.com/vuejs/vue-next/tree/master/packages/runtime-core) para criar renderizações custumizadas
- [Síntaxe _Sugar_ para CSF que utilizem _Composition_ API (`<script setup>`)](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-script-setup.md) <Badge text="experimental" type="warning" />
- [Variáveis CSS baseadas em estado (`<style vars>`)](https://github.com/vuejs/rfcs/blob/sfc-improvements/active-rfcs/0000-sfc-style-variables.md) <Badge text="experimental" type="warning" />
- [`<style scoped>` em CSF agora podem incluir regras globais ou regras que visam apenas conteúdo de _slots_](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0023-scoped-styles-changes.md)

## Incompatibilidades

::: info INFO
Ainda estamos trabalhando na migração dedicada do Vue 3 com comportamento compatível com o Vue 2 e avisos de tempo de execução de uso incompatível. Se você está planejando migrar um aplicativo Vue 2 mais complexo, é altamente recomendável aguardar a Distribuição de Migração para uma experiência mais tranquila.
:::

A seguir, uma lista de mudanças incompatíveis com 2.x:

### Global API

- [API Global de Vue agora utiliza uma instância da aplicação](/guide/migration/global-api.html)
- [API Global e internas foram reestruturadas para _tree-shakable_](/guide/migration/global-api-treeshaking.html)

### Diretivas do template

- [O uso de `v-model` em componentes foi remodelado](/guide/migration/v-model.html)
- [O atributo `key` em `<template v-for>` e nós não `v-for` foi alterado](/guide/migration/key-attribute.html)
- [A precedência de `v-if` e `v-for` quando usados no mesmo elemento foi modificada](/guide/migration/v-if-v-for.html)
- [`v-bind="object"` é agora sensível à ordem de disposição](/guide/migration/v-bind.html)
- [`ref` em `v-for` não resulta mais em um array de refs](/guide/migration/array-refs.html)

### Componentes

- [Componentes funcionais só podem ser criados usando uma função simples](/guide/migration/functional-components.html)
- [Os atributos `functional` em um Componentes Single File (CSF), `<template>` e a opção componente `functional` estão obsoletas](/guide/migration/functional-components.html)
- [Componentes assíncronos necessitam de um método `defineAsyncComponent` para serem criados](/guide/migration/async-components.html)

### Funções de Renderização

- [A função de renderização da API foi modificada](/guide/migration/render-function-api.html)
- [A propriedade `$scopedSlots` foi removida e todos os seus _slots_ passam a ser obtidos como funções através de `$slots`](/guide/migration/slots-unification.html)

### Elementos personalizados
- [A lista de elementos custumizados agora são executados durante a compilação do _template_](/guide/migration/custom-elements-interop.html)
- [A propriedade especial `is` está restrita apenas à _tag_ `<component>`](/guide/migration/custom-elements-interop.html#customized-built-in-elements)

### Outras mudanças menores

- A opção de ciclo de vida `destroyed` foi renomeada para `unmounted`
- A opção de ciclo de vida `beforeDestroy` foi renomeada para `beforeUnmount`
- [A propriedade `default` da função construtora não acessa mais o contexto `this`](/guide/migration/props-default-this.html)
- [A API de diretivas costumizadas foi modificada para se alinhar ao ciclo de vida do componente](/guide/migration/custom-directives.html)
- [A opção `data` deverá sempre ser declarada como uma função](/guide/migration/data-option.html)
- [A opção `data` de mixins is now merged shallowly](/guide/migration/data-option.html#mixin-merge-behavior-change)
- [Estratégia de coerção de atributos foi alterada](/guide/migration/attribute-coercion.html)
- [Algumas classes de transição foram renomeadas](/guide/migration/transition.html)
- [O `callback` de um `array` que está sendo observado só será acionado quando este for substituído. Caso você precise chama-lo dentro de uma `mutattion`, a opção `deep` deve ser especificada.](/guide/migration/watch.html)
- As tags `<template>` sem diretivas especiais (`v-if/else-if/else`, `v-for`, ou `v-slot`) agora são tratadas como elementos simples resultando em um elemento `<template>` nativo ao invez de renderizar seu conteúdo interno.
- No Vue 2.x, o _container_ raiz da aplicação `outerHTML` é substituído pelo _template_ do componente raiz (ou eventualmente compilado em um _template_, se o componente raiz não dispuser da opção _template_/renderização). Vue 3.x agora usa o _conteiner_ da aplicação `innerHTML` ao invés - isto é, o próprio container não é mais considerado parte do _template_.

### APIs removidas

- [Suporte `keyCode` como modificadores `v-on`](/guide/migration/keycode-modifiers.html)
- [Instância dos métodos $on, $off e \$once](/guide/migration/events-api.html)
- [Filtros](/guide/migration/filters.html)
- [Atributos `inline` nos _templates_](/guide/migration/inline-template-attribute.html)
- Instância do método `$destroy`. Os usuários não devem mais acessar manualmente o ciclo de vida de componentes indiviuais Vue.

## Bibliotecas suportadas

Todas as nossas bibliotecas e ferramentas oficiais agora oferecem suporte ao Vue 3, mas, a maioria delas ainda está em _status_ beta e distribuídas sob a _tag_ `next` no NPM. **Estamos planejando estabilizar e mudar todos os projetos para usar a tag `latest` até o final de 2020.**

### Vue CLI

<a href="https://www.npmjs.com/package/@vue/cli" target="_blank" noopener noreferrer><img src="https://img.shields.io/npm/v/@vue/cli"></a>

A partir da v4.5.0, `vue-cli` agora oferece opção integrada para escolher a pre-definição Vue 3 ao criar um novo projeto. Você pode atualizar o `vue-cli` e executar o `vue create` para criar um projeto Vue 3 hoje.

- [Documentação](https://cli.vuejs.org/)
- [GitHub](https://github.com/vuejs/vue-cli)

### Vue Router

<a href="https://www.npmjs.com/package/vue-router/v/next" target="_blank" noopener noreferrer><img src="https://img.shields.io/npm/v/vue-router/next.svg"></a>

Vue Router 4.0 provê suporte ao Vue 3, possuindo uma série de modificações relevantes próprias. Leia o [README](https://github.com/vuejs/vue-router-next#vue-router-next-) para verificar todos os detalhes.

- [GitHub](https://github.com/vuejs/vue-router-next)
- [RFCs](https://github.com/vuejs/rfcs/pulls?q=is%3Apr+is%3Amerged+label%3Arouter)

### Vuex

<a href="https://www.npmjs.com/package/vuex/v/next" target="_blank" noopener noreferrer><img src="https://img.shields.io/npm/v/vuex/next.svg"></a>

Vuex 4.0 provê suporte ao Vue 3 com basicamente a mesma API que o 3.x. A única modificação importante é em [como o plugin é instalado](https://github.com/vuejs/vuex/tree/4.0#breaking-changes).

- [GitHub](https://github.com/vuejs/vuex/tree/4.0)

### Extensão para desenvolvedores

Estamos trabalhando em uma nova versão do _Devtools_ com uma nova IU e refatoração interna para oferecer suporte a várias versões do Vue. A nova versão está atualmente em beta e suporta apenas Vue 3 (por enquanto). A integração do Vuex e do Router também está em andamento.

- Para navegador Chrome: [Instale da Chrome web store](https://chrome.google.com/webstore/detail/vuejs-devtools/ljjemllljcmogpfapbkkighbhhppjdbg?hl=en)

  - Nota: o canal beta pode entrar em conflito com a versão estável do _devtools_, então pode ser necessário desabilitar temporariamente a versão estável para que a versão beta funcione corretamente.

- Para navegador Firefox: [Baixe a extensão assinada](https://github.com/vuejs/vue-devtools/releases/tag/v6.0.0-beta.2) (Arquivo `.xpi` em Assets)

### Suporte IDE

É recomendado o uso do [VSCode](https://code.visualstudio.com/) com a extensão oficial [Vetur](https://marketplace.visualstudio.com/items?itemName=octref.vetur), a qual provê suporte à IDE para Vue 3.

### Outros projetos

| Projeto               | NPM                           | Repositório          |
| --------------------- | ----------------------------- | -------------------- |
| @vue/babel-plugin-jsx | [![rc][jsx-badge]][jsx-npm]   | [[GitHub][jsx-code]] |
| eslint-plugin-vue     | [![ga][epv-badge]][epv-npm]   | [[GitHub][epv-code]] |
| @vue/test-utils       | [![beta][vtu-badge]][vtu-npm] | [[GitHub][vtu-code]] |
| vue-class-component   | [![beta][vcc-badge]][vcc-npm] | [[GitHub][vcc-code]] |
| vue-loader            | [![beta][vl-badge]][vl-npm]   | [[GitHub][vl-code]]  |
| rollup-plugin-vue     | [![beta][rpv-badge]][rpv-npm] | [[GitHub][rpv-code]] |

[jsx-badge]: https://img.shields.io/npm/v/@vue/babel-plugin-jsx.svg
[jsx-npm]: https://www.npmjs.com/package/@vue/babel-plugin-jsx
[jsx-code]: https://github.com/vuejs/jsx-next
[vd-badge]: https://img.shields.io/npm/v/@vue/devtools/beta.svg
[vd-npm]: https://www.npmjs.com/package/@vue/devtools/v/beta
[vd-code]: https://github.com/vuejs/vue-devtools/tree/next
[epv-badge]: https://img.shields.io/npm/v/eslint-plugin-vue.svg
[epv-npm]: https://www.npmjs.com/package/eslint-plugin-vue
[epv-code]: https://github.com/vuejs/eslint-plugin-vue
[vtu-badge]: https://img.shields.io/npm/v/@vue/test-utils/next.svg
[vtu-npm]: https://www.npmjs.com/package/@vue/test-utils/v/next
[vtu-code]: https://github.com/vuejs/vue-test-utils-next
[jsx-badge]: https://img.shields.io/npm/v/@ant-design-vue/babel-plugin-jsx.svg
[jsx-npm]: https://www.npmjs.com/package/@ant-design-vue/babel-plugin-jsx
[jsx-code]: https://github.com/vueComponent/jsx
[vcc-badge]: https://img.shields.io/npm/v/vue-class-component/next.svg
[vcc-npm]: https://www.npmjs.com/package/vue-class-component/v/next
[vcc-code]: https://github.com/vuejs/vue-class-component/tree/next
[vl-badge]: https://img.shields.io/npm/v/vue-loader/next.svg
[vl-npm]: https://www.npmjs.com/package/vue-loader/v/next
[vl-code]: https://github.com/vuejs/vue-loader/tree/next
[rpv-badge]: https://img.shields.io/npm/v/rollup-plugin-vue/next.svg
[rpv-npm]: https://www.npmjs.com/package/rollup-plugin-vue/v/next
[rpv-code]: https://github.com/vuejs/rollup-plugin-vue/tree/next
