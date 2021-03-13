# Misc

## name

- **Tipo:** `string`

- **Detalhes:**

  Permitir que o componente seja invocado recursivamente no *template*. Observe que quando um componente é registrado globalmente com `Vue.createApp({}).component({})`, o ID global é automaticamente definido como seu nome.

  Outro benefício de especificar a opção `name` é o *debugging*. Componentes nomeados resultam em mensagens de aviso mais úteis. Também, quando inspecionado um *app* no [vue-devtools](https://github.com/vuejs/vue-devtools), componentes sem nome vão ser exibidos como `<AnonymousComponent>`, o que não é muito informativo. Provendo a opção `name`, você vai ter uma árvore de componentes muito mais informativa.

## delimiters

- **Tipo:** `Array<string>`

- **Padrão:** `{{ "['\u007b\u007b', '\u007d\u007d']" }}` 

- **Restrições:** Essa opção só é disponível na *full build*, com compilação de *template* no navegador.

- **Detalhes:**

  Define os delimitadores usados para interpolação de texto dentro do *template*.

  Tipicamente isso é usado para evitar conflito com frameworks *server-side* que também usam sintaxe *mustache*.

- **Exemplo:**

  ```js
  Vue.createApp({
    // Delimitadores mudaram para o estilo template string ES6 
    delimiters: ['${', '}']
  })
  ```

## inheritAttrs

- **Tipo:** `boolean`

- **Padrão:** `true`

- **Detalhes:**

  Por padrão, ligações de atributo de escopo pai que não são reconhecidas como props vão "cair". Isso signifca que quando nós temos um componente *single-root*, essas ligações vão ser aplicadas para o elemento raiz do componente filho como atributos normais de HTML. Ao criar um componente que envolve um elemento alvo ou outro componente, isso pode não ser sempre o comportamento desejado. Configurando `inheritAttrs` para `false`, esse comportamento padrão pode ser desabilitado. Os atributos estão disponíveis via instância de propriedade `$attrs` e pode ser explicitamente vinculada a um elemento não não raiz usando `v-bind`.


- **Uso:**

  ```js
  app.component('base-input', {
    inheritAttrs: false,
    props: ['label', 'value'],
    emits: ['input'],
    template: `
      <label>
        {{ label }}
        <input
          v-bind="$attrs"
          v-bind:value="value"
          v-on:input="$emit('input', $event.target.value)"
        >
      </label>
    `
  })
  ```

- **Veja também:** [Desativando a Herança de Atributos](../guide/component-attrs.html#disabling-attribute-inheritance)
