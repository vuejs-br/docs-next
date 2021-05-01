# API de Aplicação

No Vue 3, APIs que modificam globalmente o comportamento do Vue agora se tornaram instâncias da aplicação, criadas pelo novo método `createApp`. Além disso, seus efeitos agora têm como escopo a instância dessa aplicação específica:

```js
import { createApp } from 'vue'

const app = createApp({})
```

Chamar `createApp` retorna uma instância da aplicação. Esta instância fornece um contexto da aplicação. Toda a árvore de componentes montada pela instância da aplicação compartilha o mesmo contexto, fornecendo as configurações que eram "globais" anteriormente no Vue 2.x.

Além disso, uma vez que o método `createApp` retorna a própria instância da aplicação, você pode encadear outros métodos após ele, que podem ser encontrados nas seções seguintes.

## component

- **Argumentos:**

  - `{string} name`
  - `{Function | Object} definition (opcional)`

- **Retorna:**

  - A instância da aplicação se o argumento `definition` é passado
  - A definição da diretiva se o argumento `definition` não é passado

- **Uso:**

  Registrar ou recuperar um componente global. O registro também atribui automaticamente o `name` do componente com o parâmetro `name` fornecido. 

- **Exemplo:**

```js
import { createApp } from 'vue'

const app = createApp({})

// registrar um objeto de opções
app.component('my-component', {
  /* ... */
})

// recuperar um componente registrado
const MyComponent = app.component('my-component')
```

- **Ver também:** [Componentes](../guide/component-basics.html#exemplo-base)

## config

- **Uso:**

Um objeto contendo configurações da aplicação.

- **Exemplo:**

```js
import { createApp } from 'vue'
const app = createApp({})

app.config = {...}
```

- **Ver também:** [Config da Aplicação](./application-config.html)

## directive

- **Argumentos:**

  - `{string} name`
  - `{Function | Object} definition (opcional)`

- **Retorna:**

  - A instância da aplicação se o argumento `definition` é passado
  - A definição da diretiva se o argumento `definition` não é passado

- **Uso:**

  Registrar ou recuperar uma diretiva global.

- **Exemplo:**

```js
import { createApp } from 'vue'
const app = createApp({})

// registro
app.directive('my-directive', {
  // A diretiva tem um conjunto de hooks de ciclo de vida:
  // chamado antes que o componente pai do elemento vinculado (bound) seja montado
  beforeMount() {},
  // chamado quando o componente pai do elemento vinculado é montado
  mounted() {},
  // chamado antes que o VNode do componente contido seja atualizado
  beforeUpdate() {},
  // chamado depois que o VNode do componente contido e VNodes de seus filhos 
  // foram atualizados
  updated() {},
  // chamado antes que o componete pai do elemento vinculado seja desmontado
  beforeUnmount() {},
  // chamado quando o componente pai do elemento vinculado é desmontado
  unmounted() {}
})

// registro (função diretiva)
app.directive('my-directive', () => {
  // isso será chamado como `mounted` e `updated`
})

// getter, retorna a definição da diretiva se registrada
const myDirective = app.directive('my-directive')
```
Os hooks de diretiva recebem estes argumentos:

#### el

O elemento ao qual a diretiva está vinculada. Pode ser usado para manipular diretamente o DOM.

#### binding

Um objeto contendo as seguintes propriedades.

- `instance`: A instância do componente onde a diretiva é usada.
- `value`:  O valor passado para a diretiva. Por exemplo em `v-my-directive="1 + 1"`, o valor seria `2`.
- `oldValue`: O valor anterior, disponível somente em `beforeUpdate` e `updated`. Está disponível se o valor foi alterado ou não.
- `arg`: O argumento passado para a diretiva, se houver. Por exemplo em `v-my-directive:foo`, o arg seria `"foo"`.
- `modifiers`: Um objeto contendo modificadores, se houver. Por exemplo em `v-my-directive.foo.bar`, o objeto `modifiers` seria `{ foo: true, bar: true }`.
- `dir`: um objeto, passado como um parâmetro quando a diretiva é registrada. Por exemplo, na diretiva:

```js
app.directive('focus', {
  mounted(el) {
    el.focus()
  }
})
```

`dir` seria o seguinte objeto:

```js
{
  mounted(el) {
    el.focus()
  }
}
```

#### vnode

Um blueprint do elemento DOM real recebido como o argumento `el` acima.

#### prevNode

O node virtual anterior, somente disponível nos hooks `beforeUpdate` e `updated`.

:::tip Nota
Além de `el`, você deve tratar esses argumentos como somente leitura e nunca modificá-los. Se você precisar compartilhar informações entre hooks, é recomendado fazê-lo por meio do elemento [dataset](https://developer.mozilla.org/pt-BR/docs/Web/API/HTMLOrForeignElement/dataset).
:::

- **Ver também:** [Diretivas Personalizadas](../guide/custom-directive.html)

## mixin

- **Argumentos:**

  - `{Object} mixin`

- **Retorna:**

  - A instância da aplicação

- **Uso:**

  Aplicar um mixin em todo o escopo da aplicação. Uma vez registrado eles podem ser usados no modelo de qualquer componente da aplicação atual. Pode ser usado por autores de plugins para injetar comportamento personalizado nos componentes. **Não recomendado no código da aplicação**.

- **Ver também:** [Mixin Global](../guide/mixins.html#mixin-global)

## mount

- **Argumentos:**

  - `{Element | string} rootContainer`
  - `{boolean} isHydrate (opcional)`

- **Retorna:**

  - A instância do componente raiz

- **Uso:**

  Monta um componente raiz da instância da aplicação no elemento DOM fornecido.

- **Exemplo:**

```html
<body>
  <div id="my-app"></div>
</body>
```

```js
import { createApp } from 'vue'

const app = createApp({})
// faça alguns preparativos necessários
app.mount('#my-app')
```

- **Ver também:**
  - [Diagrama do Ciclo de Vida](../guide/instance.html#diagrama-do-ciclo-de-vida)

## provide

- **Argumentos:**

  - `{string | Symbol} key`
  - `value`

- **Retorna:**

  - A instância da aplicação

- **Uso:**

  Define um valor que pode ser injetado em todos componentes dentro da aplicação. Componentes devem usar `inject` para receber os valores fornecidos.
   
  De uma perspectiva `provide`/`inject`, a aplicação pode ser considerada como um ancestral do nível raiz, com o componente raiz como seu filho único.

  Este método não pode ser confundido com o  [componente de opção provide](options-composition.html#provide-inject) ou a [função provide](composition-api.html#provide-inject) da API de composição. Embora também façam parte do mesmo mecanismo  `provide`/`inject`, eles são usados para configurar valores fornecidos por um componente ao invés da aplicação.

  Fornecer valores por meio da aplicação é especialmente útil quando ao escrever plugins, pois os plugins normalmente não seriam capazes de fornecer valores usando componentes. É uma alternativa ao uso de [globalProperties](application-config.html#globalproperties).

  :::tip Nota
  Os bindings `provide` e `inject` NÃO são reativos. Isto é intencional. No entanto, se você passar um objeto observado, as propriedades desse objeto permanecerão reativas. 
  :::

- **Exemplo:**

  Injetando uma propriedade no componente raiz, com um valor fornecido pela aplicação:

```js
import { createApp } from 'vue'

const app = createApp({
  inject: ['user'],
  template: `
    <div>
      {{ user }}
    </div>
  `
})

app.provide('user', 'administrator')
```

- **Ver também:**
  - [Prover / Injetar](../guide/component-provide-inject.md)

## unmount

- **Argumentos:**

  - `{Element | string} rootContainer`

- **Uso:**

  Desmonta um componente raiz da instância da aplicação no elemento DOM fornecido.

- **Exemplo:**

```html
<body>
  <div id="my-app"></div>
</body>
```

```js
import { createApp } from 'vue'

const app = createApp({})
// faça algumas preparações necessárias
app.mount('#my-app')

// Aplicação será desmontada 5 segundos após a montagem
setTimeout(() => app.unmount('#my-app'), 5000)
```

## use

- **Argumentos:**

  - `{Object | Function} plugin`
  - `...options (opcional)`

- **Retorna:**

  - A instância da aplicação

- **Uso:**

  Instalar um Vue.js plugin. Se o plugin é um objeto, deve expor um método `install`. Se for uma função em si, será tratado como método de instalação.
  
  O método de instalação será chamado com a aplicação como seu primeiro argumento. Quaisquer `options` passadas para `use` serão passadas em argumentos subsequentes.

  Quando esse método é chamado no mesmo plugin várias vezes, o plugin será instalado apenas uma vez.

- **Ver também:** [Plugins](../guide/plugins.html)
