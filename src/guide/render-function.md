# Funções de Renderização

Vue recomenda o uso de templates para construir aplicações na grande maioria dos casos. No entanto, existem situações onde precisamos de todo o poder programático do JavaScript. É aí onde podemos utilizar a **função de renderização**.

Vamos mergulhar em um exemplo onda uma função `render()` seria prática. Digamos que queremos gerar um título ancorado:

```html
<h1>
  <a name="hello-world" href="#hello-world">
    Hello world!
  </a>
</h1>
```

Títulos ancorados são usados frequentemente, nós poderíamos criar um componente:

```vue-html
<anchored-heading :level="1">Hello world!</anchored-heading>
```

O componente deve gerar um título baseado na propriedade `level`, e, nós rapidamente chegaríamos aqui:

```js
const app = Vue.createApp({})

app.component('anchored-heading', {
  template: `
    <h1 v-if="level === 1">
      <slot></slot>
    </h1>
    <h2 v-else-if="level === 2">
      <slot></slot>
    </h2>
    <h3 v-else-if="level === 3">
      <slot></slot>
    </h3>
    <h4 v-else-if="level === 4">
      <slot></slot>
    </h4>
    <h5 v-else-if="level === 5">
      <slot></slot>
    </h5>
    <h6 v-else-if="level === 6">
      <slot></slot>
    </h6>
  `,
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

Este template não parece bom. Não apenas é verboso, como também estamos duplicando o `<slot></slot>` para cada nível de título. E quando adicionarmos o elemento de âncora, teríamos que duplicá-lo em cada ramo `v-if/v-else-if`.

Enquanto que templates funcionam muito bem para a maioria dos componentes, fica claro que este não é um deles. Então, vamos tentar reescreve-lo com uma função `render()`:

```js
const app = Vue.createApp({})

app.component('anchored-heading', {
  render() {
    const { h } = Vue

    return h(
      'h' + this.level, // tag name
      {}, // props/attributes
      this.$slots.default() // array of children
    )
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

A implementação da função `render()` é muito mais simples, mas também requer mais familiaridade com as propriedades das instâncias de componentes. Nesse caso, você deve saber que quando você passar filhos sem uma diretiva `v-slot` para um componente, como o `Hello world!` dentro do `anchored-heading`, esses filhos serão armazenados na instância do componente em `$slots.default()`. Se você já não tiver feito ainda. **é recomendado ler a [API de propriedades de instância](../api/instance-properties.html) antes de mergulhar nas funções de renderização.**

## A árvore DOM

Antes de adentrarmos nas funções de renderização, é importante conhecer um pouco sobre como os navegadores funcionam. Veja esse exemplo de HTML:

```html
<div>
  <h1>My title</h1>
  Some text content
  <!-- TODO: Add tagline -->
</div>
```

Quando um navegador lê este código, ele compila uma [árvore de "nós DOM"](https://javascript.info/dom-nodes) para ajudá-lo a acompanhar tudo.

A árvore de nós DOM para o HTML acima se parece com isso:

![DOM Tree Visualization](/images/dom-tree.png)

Cada elemento é uma nó. Cada trecho de texto é um nó. Até mesmo comentários são nós! Cada nó pode possuír filhos (i.e. cada nó pode conter outros nós).

Atualizar todos esses nós eficientemente pode ser difícil, mas felizmente, nós nunca precisamos fazê-lo manualmente. Ao invés disso, nós dizemos ao Vue qual HTML nós queremos na página, em um template:

```html
<h1>{{ blogTitle }}</h1>
```

Ou em uma função de renderização:

```js
render() {
  return Vue.h('h1', {}, this.blogTitle)
}
```

E em ambos os casos, o Vue automaticamente mantem a página atualizada, até mesmo quando o `blogTitle` muda.

## A árvore Virtual DOM

Vue mantem a página atualizada compilando um **DOM virtual** para acompanhar as mudanças que necessita para fazer o DOM real. Olhando a seguinte linha mais de perto:

```js
return Vue.h('h1', {}, this.blogTitle)
```

O que função `h()` retorna? Não é _exatamente_ um elemento DOM real. Ela retorna um objeto que contém informações que descrevem para o Vue que tipo de nó deve ser renderizado na página, incluíndo descrições de qualquer nó filho. Chamamos essa descrição do nó de "nó virtual", geralmente abreviado para **_VNode_**. "Virtual DOM" é como chamamos toda a árvore de _VNodes_, constituída de uma árvore de componentes Vue.

## Argumentos de `h()`

A função `h()`é um utilitário para criar _VNodes_. Poderia, talvez, ser nomeado com mais precisão como `createVNode()`, mas é chamada `h()` devido ao uso frequente e por brevidade. Ela aceita três argumentos:

```js
// @returns {VNode}
h(
  // {String | Object | Function } tag
  // O nome de uma tag HTML, um componente ou um componente assíncrono.
  // Usar uma função que retorna null vai renderizar um comentário.
  //
  // Obrigatório.
  'div',

  // {Object} props
  // Um objeto correspondente aos atributos, propriedades e eventos
  // que utilizaríamos em um template.
  //
  // Opcional.
  {},

  // {String | Array | Object} children
  // VNodes filhos, construídos usando `h()`,
  // ou usando strings to obter 'VNodes de texto' ou
  // um objeto com slots.
  //
  // Opcional.
  [
    'Algum texto vem primeiro.',
    h('h1', 'Um título'),
    h(MyComponent, {
      someProp: 'foobar'
    })
  ]
)
```

## Complete Example

With this knowledge, we can now finish the component we started:

```js
const app = Vue.createApp({})

/** Recursively get text from children nodes */
function getChildrenTextContent(children) {
  return children
    .map(node => {
      return typeof node.children === 'string'
        ? node.children
        : Array.isArray(node.children)
        ? getChildrenTextContent(node.children)
        : ''
    })
    .join('')
}

app.component('anchored-heading', {
  render() {
    // create kebab-case id from the text contents of the children
    const headingId = getChildrenTextContent(this.$slots.default())
      .toLowerCase()
      .replace(/\W+/g, '-') // replace non-word characters with dash
      .replace(/(^-|-$)/g, '') // remove leading and trailing dashes

    return Vue.h('h' + this.level, [
      Vue.h(
        'a',
        {
          name: headingId,
          href: '#' + headingId
        },
        this.$slots.default()
      )
    ])
  },
  props: {
    level: {
      type: Number,
      required: true
    }
  }
})
```

## Constraints

### VNodes Must Be Unique

All VNodes in the component tree must be unique. That means the following render function is invalid:

```js
render() {
  const myParagraphVNode = Vue.h('p', 'hi')
  return Vue.h('div', [
    // Yikes - duplicate VNodes!
    myParagraphVNode, myParagraphVNode
  ])
}
```

If you really want to duplicate the same element/component many times, you can do so with a factory function. For example, the following render function is a perfectly valid way of rendering 20 identical paragraphs:

```js
render() {
  return Vue.h('div',
    Array.apply(null, { length: 20 }).map(() => {
      return Vue.h('p', 'hi')
    })
  )
}
```

## Replacing Template Features with Plain JavaScript

### `v-if` and `v-for`

Wherever something can be easily accomplished in plain JavaScript, Vue render functions do not provide a proprietary alternative. For example, in a template using `v-if` and `v-for`:

```html
<ul v-if="items.length">
  <li v-for="item in items">{{ item.name }}</li>
</ul>
<p v-else>No items found.</p>
```

This could be rewritten with JavaScript's `if`/`else` and `map()` in a render function:

```js
props: ['items'],
render() {
  if (this.items.length) {
    return Vue.h('ul', this.items.map((item) => {
      return Vue.h('li', item.name)
    }))
  } else {
    return Vue.h('p', 'No items found.')
  }
}
```

### `v-model`

The `v-model` directive is expanded to `modelValue` and `onUpdate:modelValue` props during template compilation—we will have to provide these props ourselves:

```js
props: ['modelValue'],
render() {
  return Vue.h(SomeComponent, {
    modelValue: this.modelValue,
    'onUpdate:modelValue': value => this.$emit('update:modelValue', value)
  })
}
```

### `v-on`

We have to provide a proper prop name for the event handler, e.g., to handle `click` events, the prop name would be `onClick`.

```js
render() {
  return Vue.h('div', {
    onClick: $event => console.log('clicked', $event.target)
  })
}
```

#### Event Modifiers

For the `.passive`, `.capture`, and `.once` event modifiers, they can be concatenated after event name using camel case.

For example:

```javascript
render() {
  return Vue.h('input', {
    onClickCapture: this.doThisInCapturingMode,
    onKeyupOnce: this.doThisOnce,
    onMouseoverOnceCapture: this.doThisOnceInCapturingMode,
  })
}
```

For all other event and key modifiers, no special API is necessary, because we can use event methods in the handler:

| Modifier(s)                                           | Equivalent in Handler                                                                                                |
| ----------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| `.stop`                                               | `event.stopPropagation()`                                                                                            |
| `.prevent`                                            | `event.preventDefault()`                                                                                             |
| `.self`                                               | `if (event.target !== event.currentTarget) return`                                                                   |
| Keys:<br>`.enter`, `.13`                              | `if (event.keyCode !== 13) return` (change `13` to [another key code](http://keycode.info/) for other key modifiers) |
| Modifiers Keys:<br>`.ctrl`, `.alt`, `.shift`, `.meta` | `if (!event.ctrlKey) return` (change `ctrlKey` to `altKey`, `shiftKey`, or `metaKey`, respectively)                  |

Here's an example with all of these modifiers used together:

```js
render() {
  return Vue.h('input', {
    onKeyUp: event => {
      // Abort if the element emitting the event is not
      // the element the event is bound to
      if (event.target !== event.currentTarget) return
      // Abort if the key that went up is not the enter
      // key (13) and the shift key was not held down
      // at the same time
      if (!event.shiftKey || event.keyCode !== 13) return
      // Stop event propagation
      event.stopPropagation()
      // Prevent the default keyup handler for this element
      event.preventDefault()
      // ...
    }
  })
}
```

### Slots

You can access slot contents as Arrays of VNodes from [`this.$slots`](../api/instance-properties.html#slots):

```js
render() {
  // `<div><slot></slot></div>`
  return Vue.h('div', {}, this.$slots.default())
}
```

```js
props: ['message'],
render() {
  // `<div><slot :text="message"></slot></div>`
  return Vue.h('div', {}, this.$slots.default({
    text: this.message
  }))
}
```

To pass slots to a child component using render functions:

```js
render() {
  // `<div><child v-slot="props"><span>{{ props.text }}</span></child></div>`
  return Vue.h('div', [
    Vue.h('child', {}, {
      // pass `slots` as the children object
      // in the form of { name: props => VNode | Array<VNode> }
      default: (props) => Vue.h('span', props.text)
    })
  ])
}
```

## JSX

If we're writing a lot of `render` functions, it might feel painful to write something like this:

```js
Vue.h(
  Vue.resolveComponent('anchored-heading'),
  {
    level: 1
  },
  [Vue.h('span', 'Hello'), ' world!']
)
```

Especially when the template version is so concise in comparison:

```vue-html
<anchored-heading :level="1"> <span>Hello</span> world! </anchored-heading>
```

That's why there's a [Babel plugin](https://github.com/vuejs/jsx-next) to use JSX with Vue, getting us back to a syntax that's closer to templates:

```jsx
import AnchoredHeading from './AnchoredHeading.vue'

const app = createApp({
  render() {
    return (
      <AnchoredHeading level={1}>
        <span>Hello</span> world!
      </AnchoredHeading>
    )
  }
})

app.mount('#demo')
```

For more on how JSX maps to JavaScript, see the [usage docs](https://github.com/vuejs/jsx-next#installation).

## Template Compilation

You may be interested to know that Vue's templates actually compile to render functions. This is an implementation detail you usually don't need to know about, but if you'd like to see how specific template features are compiled, you may find it interesting. Below is a little demo using `Vue.compile` to live-compile a template string:

<iframe src="https://vue-next-template-explorer.netlify.app/" width="100%" height="420"></iframe>
