# Propriedades

> Esta página assume que você já leu o [Básico sobre Componentes](component-basics.md). Leia primeiro caso seja novo com componentes.

## Tipos de Propriedades

Até agora, nós apenas vimos propriedades listadas como um vetor de Strings:

```js
props: ['title', 'likes', 'isPublished', 'commentIds', 'author']
```

Mas normalmente, você vai querer que cada propriedade seja de um tipo específico de valor. Nestes casos, você pode listar as propriedades como um objeto cujas chaves e valores contém o nome e o tipo da propriedade, respectivamente:

```js
props: {
  title: String,
  like: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object,
  callback: Function,
  contactsPromise: Promise // ou qualquer outro construtor
}
```

Fazer isso não apenas documenta seu componente, mas também mostra um aviso aos usuários no console JavaScript do navegador se eles passarem o tipo errado. Você irá aprender muito mais sobre [checagem de tipos e outras validações de propriedades](#prop-validation) mais abaixo nesta página.

## Passando Propriedades Estáticas ou Dinâmicas

Até agora, você viu propriedades passando um valor estático, como por exemplo:

```html
<blog-post title="My journey with Vue"></blog-post>
```

Você também viu propriedades associadas dinamicamente com `v-bind` ou sua abreviação, o caractere `:`, como em:

```html
<!-- Dinamicamente associa ao valor de uma variável -->
<blog-post :title="post.title"></blog-post>

<!-- Dinamicamente associa ao valor de uma expressão complexa -->
<blog-post :title="post.title + ' by ' + post.author.name"><blog-post>
```

Em ambos os exemplos acima nós passamos valores de String, mas _qualquer_ tipo de valor pode ser passado para uma propriedade.

### Passando um Número

```html
<!-- Apesar de `42` ser estático, precisamos do v-bind para dizer -->
<!-- ao Vue que esta é uma expressão JavaScript e não uma String. -->
<blog-post :likes="42"></blog-post>

<!-- Dinamicamente atribui ao valor de uma variável. -->
<blog-post :likes="post.likes"></blog-post>
```

### Passando um Boolean

```html
<!-- Incluir a propriedade, sem valor nenhum, implica em `true`. -->
<blog-post is-published></blog-post>

<!-- Apesar de `false` ser estático, precisamos do v-bind para dizer -->
<!-- ao Vue que esta é uma expressão JavaScript e não uma String.    -->
<blog-post :is-published="false"></blog-post>

<!-- Dinamicamente atribui ao valor de uma variável. -->
<blog-post :is-published="post.isPublished"></blog-post>
```

### Passando um Array

```html
<!-- Apesar de o Array ser estático, precisamos do v-bind para dizer -->
<!-- ao Vue que esta é uma expressão JavaScript e não uma String.    -->
<blog-post :comment-ids="[234, 266, 273]"></blog-post>

<!-- Dinamicamente atribui ao valor de uma variável. -->
<blog-post :comment-ids="post.commentIds"></blog-post>
```

### Passando um Objeto

```html
<!-- Apesar de o objeto ser estático, precisamos do v-bind para dizer -->
<!-- ao Vue que esta é uma expressão JavaScript e não uma String.     -->
<blog-post
  :author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
></blog-post>

<!-- Dinamicamente atribui ao valor de uma variável. -->
<blog-post :author="post.author"></blog-post>
```

### Passando as Propriedades de um Objeto

Se você deseja passar todas as propriedades de um objeto como propriedades, você pode utilizar o `v-bind` sem argumentos (`v-bind` no lugar de `:nome-da-propriedade`). Por exemplo, dado um objeto `post`:

```js
post: {
  id: 1,
  title: 'My journey with Vue'
}
```

A seguinte _template_:

```html
<blog-post v-bind="post"></blog-post>
```

Será equivalente a:

```html
<blog-post v-bind:id="post.id" v-bind:title="post.title"></blog-post>
```

### Fluxo de Dados Unidirecional

Todas as propriedades formam um **vínculo unidirecional para baixo** entre a propriedade do filho e a do pai: quando a propriedade do pai é atualizada, ela vai fluir para baixo até o filho, mas não do filho para o pai. Isso evita que componentes filhos acidentalmente alterem o estado do pai, o que pode tornar o fluxo de dados da sua aplicação mais difícil de entender.

Além disso, toda vez que o componente pai é atualizado, todas as propriedades no componente filho serão atualizadas com o valor mais recente. Isso significa que você **não** deve tentar alterar uma propriedade dentro do componente filho. Se você tentar, o Vue vai te avisar no console.

Existem dois casos mais comuns onde é tentador alterar uma propriedade:

1. **A propriedade é usada para passar um valor inicial; após isso, o componente filho quer usá-la como uma propriedade de dado local.** Neste caso, é melhor definir uma propriedade de dados local que usa a propriedade recebida como seu valor inicial:

```js
props: ['initialCounter'],
data() {
  return {
    counter: this.initialCounter
  }
}
```

2. **A propriedade é passada como um valor puro que precisa ser transformado.** Neste caso, é melhor definir uma propriedade computada usando o valor da propriedade recebida:

```js
props: ['size'],
computed: {
  normalizedSize: function () {
    return this.size.trim().toLowerCase()
  }
}
```

::: tip Observação
Observe que objetos e Arrays em JavaScript são passados por referência, então se a propriedade for um Array ou objeto, alterar o objeto ou Array em si dentro do componente filho **irá** afetar o estado do pai.
:::

## Validação de Propriedades

Componentes podem especificar requerimentos para suas propriedades, tais como os tipos que você já viu. Se um requerimento não for satisfeito, o Vue irá avisá-lo no console JavaScript do navegador. Isso é especialmente útil ao desenvolver um componente que será utilizado por outras pessoas.

Para especificar as validações de propriedade, você pode fornecer um objeto com os requerimentos de validação ao valor de `props`, ao invés de um Array de Strings. Por exemplo:

```js
app.component('my-component', {
  props: {
    // Checagem básica de tipo (valores `null` e `undefined` passam qualquer validação de tipo)
    propA: Number,
    // Múltiplos tipo possíveis
    propB: [String, Number],
    // String obrigatória
    propC: {
      type: String,
      required: true
    },
    // Number com valor padrão
    propD: {
      type: Number,
      default: 100
    },
    // Object com valor padrão
    propE: {
      type: Object,
      // Object ou Array padrões devem ser retornados
      // de uma função construtora
      default: function() {
        return { message: 'hello' }
      }
    },
    // Função validadora customizada
    propF: {
      validator: function(value) {
        // O valor passado deve bater com alguma destas Strings
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    },
    // Função com valor padrão
    propG: {
      type: Function,
      // Diferente de um Object ou Array padrão, isto não é uma função construtora - isto é uma função para servir de valor padrão
      default: function() {
        return 'Função padrão'
      }
    }
  }
})
```

Quando a validação de uma propriedade falha, o Vue irá produzir um aviso no console (se estiver usando a versão de desenvolvimento).

::: tip Observação
Observe que as propriedades são validadas **antes** de a instância do componente ser criada, então as propriedades de instância (como `data`, `computed`, etc) não estarão disponíveis dentro das funções `default` ou `validator`.
:::

### Checagem de Tipos

O `type` (tipo) pode ser um dos seguintes construtores nativos:

- String
- Number
- Boolean
- Array
- Object
- Date
- Function
- Symbol

Além disso, `type` também pode ser uma função construtora customizada e a asserção será feita com uma checagem através de `instanceof`. Por exemplo, dada a seguinte função construtora já existente:

```js
function Person(firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}
```

Você poderia usar:

```js
app.component('blog-post', {
  props: {
    author: Person
  }
})
```

para validar que o valor da propriedade `author` foi criada utilizando `new Person`.

## _Case_ de Propriedades (_camelCase_ vs _kebab-case_)

Nomes de atributos HTML são _case-insensitive_, logo navegadores irão interpretar quaisquer caracteres maiúsculos como minúsculas. Isso significa que quando você está usando _templates_ dentro do DOM, nomes de propriedades em _camelCase_ devem utilizar seus equivalentes _kebab-case_ (delimitados por hífen):

```js
const app = Vue.createApp({})

app.component('blog-post', {
  // camelCase em JavaScript
  props: ['postTitle'],
  template: '<h3{{ postTitle }}</h3>'
})
```

```html
<!-- kebab-case no HTML -->
<blog-post post-title="hello!"></blog-post>
```

Novamente, caso você esteja usando _templates_ String, esta limitação não se aplica.