# Mixins

## Fundamentos

Mixins distribuem funcionalidades reutilizáveis ​​para componentes Vue. Um objeto mixin pode conter qualquer opção de componente. Quando um componente usa um mixin, todas as opções do mixin serão "combinadas" com as opções do próprio componente.

Exemplo:

```js
// define um objeto mixin
const myMixin = {
  created() {
    this.hello()
  },
  methods: {
    hello() {
      console.log('olá do mixin!')
    }
  }
}

// definir um aplicativo que usa este mixin
const app = Vue.createApp({
  mixins: [myMixin]
})

app.mount('#mixins-basic') // => "olá do mixin!"
```

## Opção de mesclagem

Quando um mixin e o próprio componente contêm opções de sobreposição, eles serão "mesclados" usando estratégias apropriadas.

Por exemplo, objetos de dados passam por uma mesclagem recursiva, com os dados do componente tendo prioridade em casos de conflito.

```js
const myMixin = {
  data() {
    return {
      message: 'olá',
      foo: 'abc'
    }
  }
}

const app = Vue.createApp({
  mixins: [myMixin],
  data() {
    return {
      message: 'Tchau',
      bar: 'def'
    }
  },
  created() {
    console.log(this.$data) // => { message: "Tchau", foo: "abc", bar: "def" }
  }
})
```

Funções hoocks com o mesmo nome são mescladas em um array para que todas sejam chamadas. Os hoocks do Mixin serão chamados **antes** dos próprios hoocks do componente.

```js
const myMixin = {
  created() {
    console.log('hook do mixin chamando')
  }
}

const app = Vue.createApp({
  mixins: [myMixin],
  created() {
    console.log('hook do componente chamando')
  }
})

// => "hook do mixin chamando"
// => "hook do componente chamando"
```

Opções que esperam valores de objeto, por exemplo `methods`, `components` e `directives`, será mesclado no mesmo objeto. As opções do componente terão prioridade quando houver chaves conflitantes nestes objetos:

```js
const myMixin = {
  methods: {
    foo() {
      console.log('foo')
    },
    conflicting() {
      console.log('do mixin')
    }
  }
}

const app = Vue.createApp({
  mixins: [myMixin],
  methods: {
    bar() {
      console.log('bar')
    },
    conflicting() {
      console.log('de si mesmo')
    }
  }
})

const vm = app.mount('#mixins-basic')

vm.foo() // => "foo"
vm.bar() // => "bar"
vm.conflicting() // => "de si mesmo"
```

## Mixin Global

Você também pode aplicar um mixin globalmente para um aplicativo Vue:

```js
const app = Vue.createApp({
  myOption: 'Olá!'
})

// injetar um handler para `myOption` opção personalizada
app.mixin({
  created() {
    const myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

app.mount('#mixins-global') // => "Olá!"
```

Use com cuidado! Depois de aplicar um mixin globalmente, ele afetará **cada** instância de componente criada posteriormente no aplicativo fornecido (por exemplo, componentes filhos):

```js
const app = Vue.createApp({
  myOption: 'Olá!'
})

// injetar um handler para `myOption` opção personalizada
app.mixin({
  created() {
    const myOption = this.$options.myOption
    if (myOption) {
      console.log(myOption)
    }
  }
})

// adicione myOption também ao componente filho
app.component('test-component', {
  myOption: 'Olá do componente!'
})

app.mount('#mixins-global')

// => "Olá!"
// => "Olá do componente"
```

Na maioria dos casos, você só deve usá-lo para manipulação de opções personalizadas, conforme demonstrado no exemplo acima. Também é uma boa ideia enviá-los como [Plugins](plugins.html) para evitar a aplicação duplicada.

## Estratégias de combinação de opções personalizadas

Quando as opções personalizadas são mescladas, elas usam a estratégia padrão que substitui o valor existente. Se você deseja que uma opção personalizada seja mesclada usando uma lógica personalizada, você precisa anexar uma função `app.config.optionMergeStrategies`:

```js
const app = Vue.createApp({})

app.config.optionMergeStrategies.customOption = (toVal, fromVal) => {
  // retorna mergedVal
}
```

A estratégia de mesclagem recebe o valor dessa opção definida nas instâncias pai e filho como o primeiro e segundo argumentos, respectivamente. Vamos tentar verificar o que temos nesses parâmetros quando usamos um mixin:

```js
const app = Vue.createApp({
  custom: 'Olá!'
})

app.config.optionMergeStrategies.custom = (toVal, fromVal) => {
  console.log(fromVal, toVal)
  // => "Tchau!", undefined
  // => "Olá", "Tchau!"
  return fromVal || toVal
}

app.mixin({
  custom: 'Tchau!',
  created() {
    console.log(this.$options.custom) // => "Olá!"
  }
})
```

Como você pode ver, no console temos `toVal` e `fromVal` impresso primeiro no mixin e depois no `app`. 
Nós sempre voltamos `fromVal` se existe é por isso `this.$options.custom` está configurado para `hello!` 
No final. Vamos tentar mudar uma estratégia para sempre retornar um valor da instância filha:

```js
const app = Vue.createApp({
  custom: 'Olá!'
})

app.config.optionMergeStrategies.custom = (toVal, fromVal) => toVal || fromVal

app.mixin({
  custom: 'Tchau!',
  created() {
    console.log(this.$options.custom) // => "Tchau!"
  }
})
```

## Precauções

No Vue 2, os mixins eram a principal ferramenta para abstrair partes da lógica de componentes em blocos reutilizáveis. No entanto, eles têm alguns problemas:

- Mixins são propensos a conflitos: como as propriedades de cada recurso são mescladas no mesmo componente, você ainda precisa conhecer todos os outros recursos para evitar conflitos de nome de propriedade e para depuração.

- Reutilização é limitada: não podemos passar nenhum parâmetro ao mixin para alterar sua lógica, o que reduz sua flexibilidade em termos de abstração da lógica

Para resolver esses problemas, adicionamos uma nova maneira de organizar o código por questões lógicas: [API de composição](composition-api-introduction.html).
