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

## Exemplo completo

Com este conhecimento, podemos agora finalizar o componente que começamos:

```js
const app = Vue.createApp({})

/** Recupera o texto dos nós filhos recursivamente */
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
    // cria um id em kebab-case a partir do texto dos filhos
    const headingId = getChildrenTextContent(this.$slots.default())
      .toLowerCase()
      .replace(/\W+/g, '-') // substitui caracteres não-texto por traços
      .replace(/(^-|-$)/g, '') // remove os traços iniciais e finais

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

## Restrições

### VNodes devem ser únicos

Todos os _VNodes_ na árvore de componentes devem ser únicos. Isso significa que a função de renderização a seguir é inválida:

```js
render() {
  const myParagraphVNode = Vue.h('p', 'hi')
  return Vue.h('div', [
    // Éca - VNodes duplicados!
    myParagraphVNode, myParagraphVNode
  ])
}
```

Se você realmente quiser duplicar o mesmo elemento/componente várias vezes, você pode fazê-lo com uma função fábrica (_factory function_). Por exemplo, a função de renderização a seguir é uma forma perfeitamente válida de renderizar 20 parágrafos idênticos:

```js
render() {
  return Vue.h('div',
    Array.apply(null, { length: 20 }).map(() => {
      return Vue.h('p', 'hi')
    })
  )
}
```

## Substituíndo Templates de recursos por JavaScript Simples
### `v-if` e `v-for`

Sempre que algo for facilmente realizado usando JavaScript simples, as funções de renderização do Vue não são uma alternativa apropriada. Por exemplo, em um template usando `v-if` e `v-for`:

```html
<ul v-if="items.length">
  <li v-for="item in items">{{ item.name }}</li>
</ul>
<p v-else>Não foram encontrados itens.</p>
```

Pode ser rescrito usando usando `if`/`else` e `map()` com JavaScript em uma função de renderização:

```js
props: ['items'],
render() {
  if (this.items.length) {
    return Vue.h('ul', this.items.map((item) => {
      return Vue.h('li', item.name)
    }))
  } else {
    return Vue.h('p', 'Não foram encontrados itens.')
  }
}
```

### `v-model`

A diretiva `v-model` é expandida para as propriedades `modelValue`e `onUpdate:modelValue` durante a compilação do template - nós mesmos teremos que prover essas propriedades:

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

Temos que prover o propriedade com nome apropriado para o manipulador do evento, e.g., para manipular um evento de `click`, o nome da propriedade deve ser `onClick`.

```js
render() {
  return Vue.h('div', {
    onClick: $event => console.log('clicked', $event.target)
  })
}
```

#### Modificadores de Eventos

Os modificadores de evento `.passive`, `.capture` e `.once`, podem ser concatenados após o nome do evento usando grafia camelo (_camel case_).

Por exemplo:

```javascript
render() {
  return Vue.h('input', {
    onClickCapture: this.doThisInCapturingMode,
    onKeyupOnce: this.doThisOnce,
    onMouseoverOnceCapture: this.doThisOnceInCapturingMode,
  })
}
```

Para todos os outros modificadores de evento, não é necessária nenhuma API especial, pois podemos usar métodos de evento no manipulador:

| Modificador(es)                                                | Equivalente no manipulador                                                                                                        |
| -------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `.stop`                                                        | `event.stopPropagation()`                                                                                                         |
| `.prevent`                                                     | `event.preventDefault()`                                                                                                          |
| `.self`                                                        | `if (event.target !== event.currentTarget) return`                                                                                |
| Teclas:<br>`.enter`, `.13`                                     | `if (event.keyCode !== 13) return` (mude `13` para [outro código de tecla](http://keycode.info/) para outro modificador de tecla) |
| Modificadores de teclas:<br>`.ctrl`, `.alt`, `.shift`, `.meta` | `if (!event.ctrlKey) return` (mude `ctrlKey` para `altKey`, `shiftKey`, ou `metaKey`, respectivamente)                            |

Aqui temos um exemplo de todos esses modificadores sendo usados juntos:

```js
render() {
  return Vue.h('input', {
    onKeyUp: event => {
      // Aborta se o elemento emitindo o evento não é
      // o elemento em qual o evento é ligado
      if (event.target !== event.currentTarget) return
      // Aborta se a tecla que foi pressionada não é a tecla enter
      // (13) e a tecla shift não está sendo segurada
      // ao mesmo tempo
      if (!event.shiftKey || event.keyCode !== 13) return
      // Para a propagação de eventos
      event.stopPropagation()
      // Previne o manipulador padrão de teclas para este elemento
      event.preventDefault()
      // ...
    }
  })
}
```

### Slots

Você pode acessar os conteúdos de slots como Arrays de _VNodes_ através de [`this.$slots`](../api/instance-properties.html#slots):

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

Passar slots para um componente filho usando funções de renderização:

```js
render() {
  // `<div><child v-slot="props"><span>{{ props.text }}</span></child></div>`
  return Vue.h('div', [
    Vue.h('child', {}, {
      // passa `slots` como objetos filhos
      // na forma de { name: props => VNode | Array<VNode> }
      default: (props) => Vue.h('span', props.text)
    })
  ])
}
```

## JSX

Se estivermos escrevendo muitas funções `render`, pode ficar doloroso escrever algo assim:

```js
Vue.h(
  Vue.resolveComponent('anchored-heading'),
  {
    level: 1
  },
  [Vue.h('span', 'Hello'), ' world!']
)
```

Especialmente quando a versão usando template é mais concisa em comparação:

```vue-html
<anchored-heading :level="1"> <span>Hello</span> world! </anchored-heading>
```

É por isso que existe um [_plugin_ Babel](https://github.com/vuejs/jsx-next) para usar JSX com Vue, nos colocando em uma sintaxe que é mais próxima dos templates:

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

Para saber mais sobre como JSX mapeia para o JavaScript, veja a [documentação de uso](https://github.com/vuejs/jsx-next#installation).

## Template Compilation

You may be interested to know that Vue's templates actually compile to render functions. This is an implementation detail you usually don't need to know about, but if you'd like to see how specific template features are compiled, you may find it interesting. Below is a little demo using `Vue.compile` to live-compile a template string:

<iframe src="https://vue-next-template-explorer.netlify.app/" width="100%" height="420"></iframe>
