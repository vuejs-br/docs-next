# Componentes Integrados

## component

- **Propriedades:**

  - `is` - `string | Component`

- **Uso:**

  Um "meta componente" para renderizar componentes dinâmicos. O componente real a ser renderizado é determinado pela propriedade `is`. Uma propriedade `is` como string pode ser um nome de tag HTML ou um nome de componente.

- **Exemplo:**

  ```html
  <!-- um componente dinâmico controlado -->
  <!-- pela propriedade `componentId` na vm -->
  <component :is="componentId"></component>

  <!-- pode também renderizar um componente registrado ou componente passado como propriedade -->
  <component :is="$options.components.child"></component>

  <!-- pode referenciar componentes por string -->
  <component :is="condition ? 'FooComponent' : 'BarComponent'"></component>

  <!-- pode ser usado para renderizar elementos HTML nativos -->
  <component :is="href ? 'a' : 'span'"></component>
  ```

- **Ver também:** [Componentes Dinâmicos](../guide/component-dynamic-async.html)

## transition

- **Propriedades:**

  - `name` - `string` Usado para gerar automaticamente nomes de classes CSS de transições. Por exemplo, `name: 'fade'` expandirá automaticamente para `.fade-enter`, `.fade-enter-active`, etc.
  - `appear` - `boolean`, Se a transição deve ser aplicada na renderização inicial deverá ser configurado para `true`. Por padrão, `false`.
  - `persisted` - `boolean`. Se estiver `true`, indica que esta transição não insere/remove realmente o elemento, mas alterna o status entre mostrar/esconder. Os gatilhos de transição são injetados, mas serão ignorados pelo renderizador. Em vez disso, uma diretiva personalizada pode controlar a transição chamando os gatilhos injetados (por exemplo `v-show`).
  - `css` - `boolean`. Aplicar ou não classes de transição CSS. Por padrão é `true`. Se configurado para `false`, apenas acionará gatilhos registados de JavaScript por meio de eventos de componentes.
  - `type` - `string`. Especifica o tipo de eventos de transição a aguardar o tempo de término da transição. Os valores disponíveis são `"transition"` and `"animation"`. Por padrão, será detectado automaticamente o tipo que tenha uma duração mais longa.
  - `mode` - `string`. Controla a sequência de temporização das transições de saída/entrada. Modos disponíveis são  `"out-in"` e `"in-out"`; o padrão é simultâneo.
  - `duration` - `number | {`enter`: number,`leave`: number }`. Especifica a duração da transição. Por padrão, o Vue aguarda o primeiro evento de `transitionend` ou `animationend` no elemento de transição raiz.
  - `enter-from-class` - `string`
  - `leave-from-class` - `string`
  - `appear-class` - `string`
  - `enter-to-class` - `string`
  - `leave-to-class` - `string`
  - `appear-to-class` - `string`
  - `enter-active-class` - `string`
  - `leave-active-class` - `string`
  - `appear-active-class` - `string`

- **Eventos:**

  - `before-enter`
  - `before-leave`
  - `enter`
  - `leave`
  - `appear`
  - `after-enter`
  - `after-leave`
  - `after-appear`
  - `enter-cancelled`
  - `leave-cancelled` (`v-show` apenas)
  - `appear-cancelled`

- **Uso:**

  `<transition>` servem como efeitos de transição para elemento/componente **único**. O `<transition>` aplica apenas o comportamento de transição para o conteúdo dentro do *wrapper*; Ele não processa um elemento DOM extra ou aparece na hierarquia dos componentes inspecionados.

  ```html
  <!-- elemento simples -->
  <transition>
    <div v-if="ok">conteúdo alternado</div>
  </transition>

  <!-- componente dinâmico -->
  <transition name="fade" mode="out-in" appear>
    <component :is="view"></component>
  </transition>

  <!-- gatilhos de evento -->
  <div id="transition-demo">
    <transition @after-enter="transitionComplete">
      <div v-show="ok">conteúdo alternado</div>
    </transition>
  </div>
  ```

  ```js
  const app = Vue.createApp({
    ...
    methods: {
      transitionComplete (el) {
        // para o 'el' passado que é um elemento do DOM como um argumento ...
      }
    }
    ...
  })

  app.mount('#transition-demo')
  ```

- **Ver também:** [Transições de entrada e saída](/guide/transitions-enterleave.html#transitioning-single-elements-components)

## transition-group

- **Propriedades:**

  - `tag` - `string`, padrão para `span`.
  - `move-class` - substituí a classe CSS aplicada durante a transição em movimento.
  - expõe as mesmas propriedades que `<transition>` exceto `mode`.

- **Eventos:**

  - expõe os mesmos eventos que `<transition>`.

- **Uso:**

  `<transition-group>` serve como efeito de transição para **múltiplos** elementos/componentes. O `<transition-group>` renderiza um elemento DOM real. Por padrão renderiza um `<span>`, e você pode configurar qual elemento deverá ser renderizado através do atributo `tag`.

  Note que cada filho em um `<transition-group>` deve ser identificado com [**chave única**](./special-attributes.html#key) para as animações funcionarem corretamente.

  `<transition-group>` suporta transições de movimento via transformações CSS. Quando a posição de um elemento filho na tela muda após uma actualização, ele aplicará uma classe de movimento CSS (gerada automaticamente a partir do atributo `name` ou configurada com o atributo `move-class` ). Se a propriedade CSS `transform` for passível de transição quando a classe de movimento é aplicada, o elemento será animado suavemente para o seu destino usando a [técnica FLIP](https://aerotwist.com/blog/flip-your-animations/).

  ```html
  <transition-group tag="ul" name="slide">
    <li v-for="item in items" :key="item.id">
      {{ item.text }}
    </li>
  </transition-group>
  ```

- **Ver também:** [Lista de Transições](/guide/transitions-list.html)

## keep-alive

- **Propriedades:**

  - `include` - `string | RegExp | Array`. Apenas os componentes correspondentes serão colocados em cache.
  - `exclude` - `string | RegExp | Array`. Qualquer componente com o mesmo nome não será colocado em cache.
  - `max` - `number | string`. O número máximo de instâncias do componente a serem colocadas em cache.

- **Uso:**

  Quando *wrapped* (enrolado) em torno de um componente dinâmico, `<keep-alive>` coloca as instâncias dos componentes inativos em cache sem os destruir. Semelhante a `<transition>`, `<keep-alive>` é um componente abstrato: ele não processa um elemento DOM em si, e não aparece na cadeia principal do componente.

  Quando um componente é alternado para dentro de `<keep-alive>`, em `activated` e `deactivated` os gatilhos de ciclo de vida serão invocados de acordo.

  Usado principalmente para preservar o estado do componente ou evitar re-renderização.

  ```html
  <!-- básico -->
  <keep-alive>
    <component :is="view"></component>
  </keep-alive>

  <!-- filhos com multiplas condições -->
  <keep-alive>
    <comp-a v-if="a > 1"></comp-a>
    <comp-b v-else></comp-b>
  </keep-alive>

  <!-- usado juntamente com `<transition>` -->
  <transition>
    <keep-alive>
      <component :is="view"></component>
    </keep-alive>
  </transition>
  ```

  Nota, `<keep-alive>` é projetado para o caso em que ele possui um componente filho direto que está sendo alternado. Isso não funciona se você tiver `v-for` dentro dele. Quando há múltiplos filhos condicionais, como acima, `<keep-alive>` exige que apenas um filho seja renderizado de cada vez.

- **`include` and `exclude`**

  As propriedades `include` e `exclude` permitem que os componentes sejam cacheados condicionalmente. Ambas as propriedades podem ser uma String separada por vígulas, uma RegExp ou um Array:

  ```html
  <!-- string separada por vírgulas -->
  <keep-alive include="a,b">
    <component :is="view"></component>
  </keep-alive>

  <!-- regex (usando `v-bind`) -->
  <keep-alive :include="/a|b/">
    <component :is="view"></component>
  </keep-alive>

  <!-- Array (usando `v-bind`) -->
  <keep-alive :include="['a', 'b']">
    <component :is="view"></component>
  </keep-alive>
  ```

  A correspondência é verificada primeiro na opção `name` do componente, depois pelo nome no registro local (a chave dentro das opções do `components` pai) se a opção `name` não estiver disponível. Os componentes anônimos não poder ser comparados.

- **`max`**

  O número máximo de instâncias do componente a serem guardadas em cache. Quando esse número for atingido, a instância do componente em cache que foi acessada menos recentemente será destruída antes de criar uma nova instância.

  ```html
  <keep-alive :max="10">
    <component :is="view"></component>
  </keep-alive>
  ```

  ::: warning
  `<keep-alive>` não funciona com componentes funcionais porque eles não têm instâncias a serem guardadas em cache.
  :::

- **Ver também:** [Componentes dinâmicos - keep-alive](../guide/component-dynamic-async.html#dynamic-components-with-keep-alive)

## slot

- **Propriedades:**

  - `name` - `string`, Usado para o *slot* nomeado.

- **Uso:**

  `<slot>` servem como pontos de distribuição de conteúdo em modelos de componentes. O próprio `<slot>` será substituído.

  For detailed usage, see the guide section no link em baixo.

- **Ver também:** [Distribuição de Conteúdo com Slots](../guide/component-basics.html#content-distribution-with-slots)

## teleport

- **Propriedades:**

  - `to` - `string`. Propriedade obrigatória, tem que ser um seletor de consulta válido, ou um elemento de HTML (se usado em um ambiente de *browser*). Especifica um elemento de destino para onde o conteúdo de `<teleport>` será movido.

  ```html
  <!-- ok -->
  <teleport to="#some-id" />
  <teleport to=".some-class" />
  <teleport to="[data-teleport]" />

  <!-- Wrong -->
  <teleport to="h1" />
  <teleport to="some-string" />
  ```

  - `disabled` - `boolean`. Esta propriedade opcional pode ser usada para desativar a funcionalidade de `<teleport>`, o que significa que o conteúdo do slot não será movido para qualquer lugar, e em vez disso será renderizado onde você especificou o `<teleport>` no componente pai envolvente.

  ```html
  <teleport to="#popup" :disabled="displayVideoInline">
    <video src="./my-movie.mp4">
  </teleport>
  ```

  Observe que isso moverá os nós DOM reais em vez de serem destruídos e recriados e também manterá todas as instâncias do componente vivas. Todos os elementos HTML com estado (por exemplo, um vídeo em reprodução) manterão seu estado.

- **Ver também:** [Componente de teletransporte](../guide/teleport.html#teleport)
