# Propriedades de Instância

## $data

- **Tipo:** `Object`

- **Detalhes:**

  O objeto de dados que a instância do componente está observando. As proxies da instância do componente acessam as propriedades deste objeto de dados.

- **Ver também:** [Opções - Dados](./options-data.html#data-2)

## $props

- **Tipo:** `Object`

- **Detalhes:**

  Um objeto representando a propriedade atual que o componente recebeu. As proxies de instância do componente acessam as propriedades em seu objeto de propriedades.

## $el

- **Tipo:** `any`

- **Somente leitura**

- **Detalhes:**

  O elemento raiz do DOM que a instância do componente está gerenciando.

## $options

- **Tipo:** `Object`

- **Somente leitura**

- **Detalhes:**

  As opções utilizadas pela atual instância do componente. Isto é útil quando você queira incluir propriedades customizadas nas opções:

  ```js
  const app = Vue.createApp({
    customOption: 'foo',
    created() {
      console.log(this.$options.customOption) // => 'foo'
    }
  })
  ```

## $parent

- **Tipo:** `Component instance`

- **Somente leitura**

- **Detalhes:**

  A instância mãe, caso a instância atual possua uma.

## $root

- **Tipo:** `Component instance`

- **Somente leitura**

- **Detalhes:**

  A instância do componente raiz da árvore de componentes. Caso a instância atual não tenha pais, este valor será ele mesmo.

## $slots

- **Tipo:** `{ [name: string]: (...args: any[]) => Array<VNode> | undefined }`

- **Somente Leitura**

- **Detalhes:**

  Usado para o acesso de conteúdo [distribuído com slots](../guide/component-basics.html#content-distribution-with-slots) de forma programática. Cada [slot nomeado](../guide/component-slots.html#slots-nomeados) tem sua propriedade correspondente (e.g. o conteúdo de `v-slot:foo` será encontrado em `this.$slots.foo()`). A propriedade `default` contém os demais nós não incluídos nos slots nomeados ou conteúdos do `v-slot:default`.

  Acessar `this.$slots` é mais útil quando construir um componente com uma [função de renderização](../guide/render-function.html).

- **Exemplo:**

  ```html
  <blog-post>
    <template v-slot:header>
      <h1>Sobre</h1>
    </template>

    <template v-slot:default>
      <p>
        Aqui está o conteúdo da página, que será incluído em $slots.default.
      </p>
    </template>

    <template v-slot:footer>
      <p>Copyright 2020 Evan You</p>
    </template>
  </blog-post>
  ```

  ```js
  const app = Vue.createApp({})

  app.component('blog-post', {
    render() {
      return Vue.h('div', [
        Vue.h('header', this.$slots.header()),
        Vue.h('main', this.$slots.default()),
        Vue.h('footer', this.$slots.footer())
      ])
    }
  })
  ```

- **Ver também:**
  - [Componente `<slot>`](built-in-components.html#slot)
  - [Distribuição de Conteúdo com Slots](../guide/component-basics.html#distribuição-de-conteúdo-com-slots)
  - [Funções de Renderização - Slots](../guide/render-function.html#slots)

## $refs

- **Tipo:** `Object`

- **Somente leitura**

- **Detalhes:**

  Um objeto dos elementos do DOM e as instâncias de componente, registrados com [atributos `ref`](../guide/component-template-refs.html)

- **See also:**
  - [_refs_ de Template ](../guide/component-template-refs.html)
  - [Atributos especiais - ref](./special-attributes.md#ref)

## $attrs

- **Tipo:** `Object`

- **Somente leitura**

- **Detalhes:**

Contêm o atributo de escopo-pai e eventos que não são reconhecidos (e extraídos) como [propriedades](./options-data.html#props) do componente ou [eventos customizados](./options-data.html#emits). Quando um componente não possui nenhuma propriedade declarada ou eventos customizados, esse essencialmente contêm todos os bindings do escopo-pai, e podem ser passados abaixo para um componente interno via `v-bind="$attrs'"` - útil na criação de componentes de prioridade alta.

- **Veja também:**
  - [Atributos Não-Propriedades](../guide/component-attrs.html)
