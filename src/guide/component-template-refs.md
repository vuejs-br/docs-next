# *refs* &nbsp;de Template

> Esta página assume que você já leu o [Básico de Componentes](component-basics.md). Leia lá primeiro se você for novo em componentes.

Apesar da existência de propriedades e eventos, às vezes você ainda pode precisar acessar diretamente um componente filho em JavaScript. Para isso, você pode atribuir um ID de referência ao componente filho ou elemento HTML usando o atributo `ref`. Por exemplo:

```html
<input ref="input" />
```

Isso pode ser útil quando você deseja, por exemplo, de forma programática, aplicar o _focus_ nesse _input_ na montagem do componente:

```js
const app = Vue.createApp({})

app.component('base-input', {
  template: `
    <input ref="input" />
  `,
  methods: {
    focusInput() {
      this.$refs.input.focus()
    }
  },
  mounted() {
    this.focusInput()
  }
})
```

Além disso, você pode adicionar outro `ref` ao próprio componente e usá-lo para acionar o evento `focusInput` a partir do componente pai:

```html
<base-input ref="usernameInput"></base-input>
```

```js
this.$refs.usernameInput.focusInput()
```

::: warning Atenção
`$refs` é preenchido apenas após o componente ter sido renderizado. Isso serve apenas como uma saída de emergência para a manipulação direta de elementos filhos - você deve evitar acessar `$refs` de dentro de _templates_ ou dados computados.
:::

**Veja também**: [Usando _refs_ de _templates_ na Composition API](/guide/composition-api-template-refs.html#template-refs)
