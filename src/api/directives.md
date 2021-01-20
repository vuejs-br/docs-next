# Diretivas

## v-text

- **Espera:** `string`

- **Detalhes:**

  Atualiza o [textContent](https://developer.mozilla.org/en-US/docs/Web/API/Node/textContent) do elemento. Se você precisa atualizar a parte de `textContent`, você deve usar [interpolações mustache](/guide/template-syntax.html#text).

- **Exemplo:**

  ```html
  <span v-text="msg"></span>
  <!-- o mesmo que -->
  <span>{{msg}}</span>
  ```

- **Ver também:** [Sintaxe de Templates - Interpolações](../guide/template-syntax.html#text)

## v-html

- **Espera:** `string`

- **Detalhes:**

  Atualiza o [innerHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML) do elemento. **Perceba que os conteúdos são inseridos como HTML puro - eles não serão compilados como templates Vue**. Se você se encontra tentando compor templates usando `v-html`, tente repensar a solução usando componentes em vez disso.

  ::: warning
  Renderizar dinamicamente HTML arbitrário em seu website pode ser muito perigoso porque isso pode facilmente levar a [ataques XSS](https://en.wikipedia.org/wiki/Cross-site_scripting). Apenas use `v-html` em conteúdo confiável e **nunca** em conteúdo fornecido por usuário.
  :::

  Em [Componentes Single File](../guide/single-file-component.html), estilos `scoped` não serão aplicados a conteúdos `v-html`, pois tal HTML não é processado pelo compilador de templates do Vue. Se você quer atingir conteúdo `v-html` com CSS com escopo, é possível utilizar [CSS modules](https://vue-loader.vuejs.org/en/features/css-modules.html) ou um elemento global adicional `<style>` , com uma estratégia manual de escopo como BEM.

- **Exemplo:**

  ```html
  <div v-html="html"></div>
  ```

- **Ver também:** [Sintaxe de Templates - Interpolações](../guide/template-syntax.html#raw-html)

## v-show

- **Espera:** `any`

- **Uso:**

  Alterna a propriedade CSS `display` do elemento baseado na condição de verdade do valor da expressão.

  Esta diretiva dispara transições quando sua condição muda.

- **Ver também:** [Renderização Condicional - v-show](../guide/conditional.html#v-show)

## v-if

- **Espera:** `any`

- **Uso:**

  Renderiza condicionalmente o elemento baseado na condição de verdade do valor da expressão. O elemento e suas diretivas/componentes contidos são destruídos e reconstruídos durante alternâncias (*toggles*). Se o elemento é um `<template>`, o seu conteúdo será extraído como o bloco condicional.

  Esta diretiva dispara transições quando sua condição muda.

  Quando usada em conjunto com `v-if`, `v-for` tem uma prioridade maior. Não recomendamos o uso dessas duas diretivas juntas em um elemento - consulte a [guia de renderização de listas](../guide/list.html#v-for-with-v-if) para obter detalhes.

- **Ver também:** [Renderização Condicional - v-if](../guide/conditional.html#v-if)

## v-else

- **Não espera expressão**

- **Restriction:** o elemento-irmão anterior deve ter `v-if` ou `v-else-if`.

- **Uso:**

  Significa o bloco "senão" de uma cadeia `v-if` ou `v-if`/`v-else-if`.

  ```html
  <div v-if="Math.random() > 0.5">
    Agora você me vê
  </div>
  <div v-else>
    Agora você não me vê
  </div>
  ```

- **Ver também:** [Renderização Condicional - v-else](../guide/conditional.html#v-else)

## v-else-if

- **Espera:** `any`

- **Restriction:** o elemento-irmão anterior deve ter `v-if` ou `v-else-if`.

- **Uso:**

  Significa o bloco "senão caso" para `v-if`. Pode ser encadeado.

  ```html
  <div v-if="type === 'A'">
    A
  </div>
  <div v-else-if="type === 'B'">
    B
  </div>
  <div v-else-if="type === 'C'">
    C
  </div>
  <div v-else>
    Não A/B/C
  </div>
  ```

- **Ver também:** [Renderização Condicional - v-else-if](../guide/conditional.html#v-else-if)

## v-for

- **Espera:** `Array | Object | number | string | Iterable`

- **Uso:**

  Renderiza o elemento ou bloco de template múltiplas vezes baseado nos dados de origem (*source data*). O valor da diretiva deve usar a sintaxe especial `alias in expression` para fornecer um alias para o elemento atual ser iterado em:

  ```html
  <div v-for="item in items">
    {{ item.text }}
  </div>
  ```

  Alternativamente, você também pode especificar um alias para o índice (ou a chave, se usada em um Objeto):

  ```html
  <div v-for="(item, index) in items"></div>
  <div v-for="(value, key) in object"></div>
  <div v-for="(value, name, index) in object"></div>
  ```

  O comportamento padrão de `v-for` tentará corrigir os elementos no local sem os mover. Para forçá-lo a reordenar elementos, você precisa fornecer uma sugestão de ordenação com o atributo especial `key`:

  ```html
  <div v-for="item in items" :key="item.id">
    {{ item.text }}
  </div>
  ```

  `v-for` também pode trabalhar em valores que implementam o [Protocolo Iterável](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#The_iterable_protocol), incluindo `Map` e `Set` nativos.

  O uso detalhado de `v-for` é explicado na seção de Guia com link abaixo.

- **Ver também:**
  - [Renderização de Listas](../guide/list.html)

## v-on

- **Forma abreviada:** `@`

- **Espera:** `Function | Inline Statement | Object`

- **Argumento:** `event`

- **Modificadores:**

  - `.stop` - chama `event.stopPropagation()`.
  - `.prevent` - chama `event.preventDefault()`.
  - `.capture` - adiciona escuta de eventos em modo de captura.
  - `.self` - aciona o manipulador somente se o evento foi disparado a partir deste elemento.
  - `.{keyAlias}` - aciona o manipulador apenas em certas chaves.
  - `.once` - aciona o manipulador somente uma vez.
  - `.left` - aciona o manipulador somente para eventos do botão esquerdo do mouse.
  - `.right` - aciona o manipulador somente para eventos do botão direito do mouse.
  - `.middle` - aciona o manipulador somente para eventos do botão do meio do mouse.
  - `.passive` - atribui um evento ao DOM com `{ passive: true }`.

- **Uso:**

  Atribui uma escuta de evento ao elemento. O tipo de evento é indicado pelo argumento. A expressão pode ser um nome de método, uma declaração *inline*, ou omitida quando há modificadores presentes.

  Quando usada em um elemento normal, escuta somente [**eventos nativos de DOM**](https://developer.mozilla.org/en-US/docs/Web/Events). Quando usada em um componente de elemento personalizado, escuta **eventos personalizados** emitidos naquele componente-filho.

  Quando escutando a eventos nativos de DOM, o método recebe o evento nativo como argumento único. Quando usada declaração *inline*, ela tem acesso à propriedade especial `$event`: `v-on:click="handle('ok', $event)"`.

  `v-on` also supports binding to an object of event/listener pairs without an argument. Note when using the object syntax, it does not support any modifiers.

- **Exemplo:**

  ```html
  <!-- manipulador de método -->
  <button v-on:click="doThis"></button>

  <!-- evento dinâmico -->
  <button v-on:[event]="doThis"></button>

  <!-- declaração inline -->
  <button v-on:click="doThat('hello', $event)"></button>

  <!-- forma abreviada -->
  <button @click="doThis"></button>

  <!-- forma abreviada para o evento dinâmico -->
  <button @[event]="doThis"></button>

  <!-- para a propagação  -->
  <button @click.stop="doThis"></button>

  <!-- previne padrão -->
  <button @click.prevent="doThis"></button>

  <!-- previne padrão sem expressão -->
  <form @submit.prevent></form>

  <!-- modificador em cadeia -->
  <button @click.stop.prevent="doThis"></button>

  <!-- modificador de chave usando keyAlias -->
  <input @keyup.enter="onEnter" />

  <!-- o evento de clique será acionado somente uma vez -->
  <button v-on:click.once="doThis"></button>

  <!-- sintaxe de objeto -->
  <button v-on="{ mousedown: doThis, mouseup: doThat }"></button>
  ```

  Escutando eventos personalizados em um componente-filho (o manipulador é chamado quando “my-event” é emitido no filho):

  ```html
  <my-component @my-event="handleThis"></my-component>

  <!-- declaração inline -->
  <my-component @my-event="handleThis(123, $event)"></my-component>
  ```

- **Ver também:**
  - [Manipulação de Eventos](../guide/events.html)
  - [Componentes - Eventos Personalizados](../guide/component-basics.html#listening-to-child-components-events)

## v-bind

- **Forma abreviada:** `:`

- **Espera:** `any (with argument) | Object (without argument)`

- **Argumento:** `attrOrProp (optional)`

- **Modificadores:**

  - `.camel` -  transforma o nome do atributo de kebab-case para camelCase.

- **Uso:**

  Dynamically bind one or more attributes, or a component prop to an expression.

  When used to bind the `class` or `style` attribute, it supports additional value types such as Array or Objects. See linked guide section below for more details.

  When used for prop binding, the prop must be properly declared in the child component.

  When used without an argument, can be used to bind an object containing attribute name-value pairs. Note in this mode `class` and `style` does not support Array or Objects.

- **Exemplo:**

  ```html
  <!-- bind an attribute -->
  <img v-bind:src="imageSrc" />

  <!-- dynamic attribute name -->
  <button v-bind:[key]="value"></button>

  <!-- shorthand -->
  <img :src="imageSrc" />

  <!-- shorthand dynamic attribute name -->
  <button :[key]="value"></button>

  <!-- with inline string concatenation -->
  <img :src="'/path/to/images/' + fileName" />

  <!-- class binding -->
  <div :class="{ red: isRed }"></div>
  <div :class="[classA, classB]"></div>
  <div :class="[classA, { classB: isB, classC: isC }]">
    <!-- style binding -->
    <div :style="{ fontSize: size + 'px' }"></div>
    <div :style="[styleObjectA, styleObjectB]"></div>

    <!-- binding an object of attributes -->
    <div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

    <!-- prop binding. "prop" must be declared in my-component. -->
    <my-component :prop="someThing"></my-component>

    <!-- pass down parent props in common with a child component -->
    <child-component v-bind="$props"></child-component>

    <!-- XLink -->
    <svg><a :xlink:special="foo"></a></svg>
  </div>
  ```

  The `.camel` modifier allows camelizing a `v-bind` attribute name when using in-DOM templates, e.g. the SVG `viewBox` attribute:

  ```html
  <svg :view-box.camel="viewBox"></svg>
  ```

  `.camel` is not needed if you are using string templates, or compiling with `vue-loader`/`vueify`.

- **Ver também:**
  - [Class and Style Bindings](../guide/class-and-style.html)
  - [Components - Props](../guide/component-basics.html#passing-data-to-child-components-with-props)

## v-model

- **Espera:** varies based on value of form inputs element or output of components

- **Limitado a:**

  - `<input>`
  - `<select>`
  - `<textarea>`
  - components

- **Modificadores:**

  - [`.lazy`](../guide/forms.html#lazy) - listen to `change` events instead of `input`
  - [`.number`](../guide/forms.html#number) - cast valid input string to numbers
  - [`.trim`](../guide/forms.html#trim) - trim input

- **Uso:**

  Create a two-way binding on a form input element or a component. For detailed usage and other notes, see the Guide section linked below.

- **Ver também:**
  - [Form Input Bindings](../guide/forms.html)
  - [Components - Form Input Components using Custom Events](../guide/component-custom-events.html#v-model-arguments)

## v-slot

- **Forma abreviada:** `#`

- **Espera:** JavaScript expression that is valid in a function argument position (supports destructuring in [supported environments](../guide/component-slots.html#destructuring-slot-props)). Optional - only needed if expecting props to be passed to the slot.

- **Argumento:** slot name (optional, defaults to `default`)

- **Limitado a:**

  - `<template>`
  - [components](../guide/component-slots.html#abbreviated-syntax-for-lone-default-slots) (for a lone default slot with props)

- **Uso:**

  Denote named slots or slots that expect to receive props.

- **Exemplo:**

  ```html
  <!-- Named slots -->
  <base-layout>
    <template v-slot:header>
      Header content
    </template>

    <template v-slot:default>
      Default slot content
    </template>

    <template v-slot:footer>
      Footer content
    </template>
  </base-layout>

  <!-- Named slot that receives props -->
  <infinite-scroll>
    <template v-slot:item="slotProps">
      <div class="item">
        {{ slotProps.item.text }}
      </div>
    </template>
  </infinite-scroll>

  <!-- Default slot that receive props, with destructuring -->
  <mouse-position v-slot="{ x, y }">
    Mouse position: {{ x }}, {{ y }}
  </mouse-position>
  ```

  For more details, see the links below.

- **Ver também:**
  - [Components - Slots](../guide/component-slots.html)

## v-pre

- **Não espera expressão**

- **Uso:**

  Skip compilation for this element and all its children. You can use this for displaying raw mustache tags. Skipping large numbers of nodes with no directives on them can also speed up compilation.

- **Exemplo:**

  ```html
  <span v-pre>{{ this will not be compiled }}</span>
  ```

## v-cloak

- **Não espera expressão**

- **Uso:**

  This directive will remain on the element until the associated component instance finishes compilation. Combined with CSS rules such as `[v-cloak] { display: none }`, this directive can be used to hide un-compiled mustache bindings until the component instance is ready.

- **Exemplo:**

  ```css
  [v-cloak] {
    display: none;
  }
  ```

  ```html
  <div v-cloak>
    {{ message }}
  </div>
  ```

  The `<div>` will not be visible until the compilation is done.

## v-once

- **Não espera expressão**

- **Detalhes:**

  Render the element and component **once** only. On subsequent re-renders, the element/component and all its children will be treated as static content and skipped. This can be used to optimize update performance.

  ```html
  <!-- single element -->
  <span v-once>This will never change: {{msg}}</span>
  <!-- the element have children -->
  <div v-once>
    <h1>comment</h1>
    <p>{{msg}}</p>
  </div>
  <!-- component -->
  <my-component v-once :comment="msg"></my-component>
  <!-- `v-for` directive -->
  <ul>
    <li v-for="i in list" v-once>{{i}}</li>
  </ul>
  ```

- **Ver também:**
  - [Data Binding Syntax - interpolations](../guide/template-syntax.html#text)

## v-is

> Note: this section only affects cases where Vue templates are directly written in the page's HTML.

- **Espera:** string literal

- **Limitado a:** native HTML elements

- **Uso:** When using in-DOM templates, the template is subject to native HTML parsing rules. Some HTML elements, such as `<ul>`, `<ol>`, `<table>` and `<select>` have restrictions on what elements can appear inside them, and some elements such as `<li>`, `<tr>`, and `<option>` can only appear inside certain other elements. As a workaround, we can use `v-is` directive on these elements:

```html
<table>
  <tr v-is="'blog-post-row'"></tr>
</table>
```

:::warning
`v-is` functions like a dynamic 2.x `:is` binding - so to render a component by its registered name, its value should be a JavaScript string literal:

```html
<!-- Incorrect, nothing will be rendered -->
<tr v-is="blog-post-row"></tr>

<!-- Correct -->
<tr v-is="'blog-post-row'"></tr>
```

:::
