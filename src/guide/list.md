# Renderização de Listas

## Mapeando um Array de Elementos com `v-for`

Podemos utilizar a diretiva `v-for` para renderizar uma lista de elementos com base nos dados de um Array. A diretiva `v-for` requer uma sintaxe especial na forma de `item in items`, onde `items` é a fonte de dados e `item` é um **apelido** para o elemento que estiver sendo iterado:

```html
<ul id="array-rendering">
  <li v-for="item in items">
    {{ item.message }}
  </li>
</ul>
```

```js
Vue.createApp({
  data() {
    return {
      items: [{ message: 'Vue' }, { message: 'JavaScript' }]
    }
  }
}).mount('#array-rendering')
```

Resultado:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="vuejs-br" data-slug-hash="WNwPbmx" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Exemplo de v-for com Array">
  <span>Veja o <a href="https://codepen.io/vuejs-br/pen/WNwPbmx">
  Exemplo de v-for com Array</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Dentro de blocos `v-for` temos acesso completo às propriedades do escopo pai. `v-for` também suporta um segundo argumento opcional para o índice do item atual.

```html
<ul id="array-with-index">
  <li v-for="(item, index) in items">
    {{ parentMessage }} - {{ index }} - {{ item.message }}
  </li>
</ul>
```

```js
Vue.createApp({
  data() {
    return {
      parentMessage: 'Pai',
      items: [{ message: 'Vue' }, { message: 'JavaScript' }]
    }
  }
}).mount('#array-with-index')
```

Resultado:
<p class="codepen" data-height="265" data-theme-id="light" data-default-tab="css,result" data-user="vuejs-br" data-slug-hash="wvGNBOK" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Exemplo de v-for com Array e índice">
   <span>Veja o <a href="https://codepen.io/vuejs-br/pen/wvGNBOK">
  Exemplo de v-for com Array e índice</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Também é possível utilizar `of` como delimitador em vez de `in`, de forma que fique mais próximo da sintaxe de iteradores do JavaScript:

```html
<div v-for="item of items"></div>
```

## `v-for` com Objetos

Você também pode utilizar `v-for` para iterar pelas propriedades de um objeto.

```html
<ul id="v-for-object" class="demo">
  <li v-for="value in myObject">
    {{ value }}
  </li>
</ul>
```

```js
Vue.createApp({
  data() {
    return {
      myObject: {
        titulo: 'Como fazer listas no Vue',
        autor: 'Jane Doe',
        publicadoEm: '2020-03-22'
      }
    }
  }
}).mount('#v-for-object')
```

Resultado:

<p class="codepen" data-height="265" data-theme-id="light" data-default-tab="js,result" data-user="vuejs-br" data-slug-hash="wvGNBNa" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Exemplo de v-for com objeto">
   <span>Veja o <a href="https://codepen.io/vuejs-br/pen/wvGNBNa">
  Exemplo de v-for com objeto</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Você também pode fornecer um segundo argumento para o nome da propriedade (também conhecido como chave):

```html
<li v-for="(value, name) in myObject">
  {{ name }}: {{ value }}
</li>
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="vuejs-br" data-slug-hash="RwavNvm" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Exemplo de v-for com objeto e chave">
  <span>Veja o <a href="https://codepen.io/vuejs-br/pen/RwavNvm">
  Exemplo de v-for com objeto e chave</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

E ainda um terceiro para o índice:

```html
<li v-for="(value, name, index) in myObject">
  {{ index }}. {{ name }}: {{ value }}
</li>
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="vuejs-br" data-slug-hash="qBZgEgM" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Exemplo de v-for com objeto, chave e índice">
  <span>Veja o <a href="https://codepen.io/vuejs-br/pen/qBZgEgM">
  Exemplo de v-for com objeto, chave e índice</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

:::tip
Quando estiver iterando sobre um objeto, a ordem é baseada na ordem de enumeração do `Object.keys()`, que não tem garantia de consistência entre implementações distintas de motores JavaScript.
:::

## Manutenção de Estado

Quando Vue está atualizando uma lista de elementos renderizados com `v-for`, por padrão se utiliza de uma estratégia de "remendo local" (_in-place patch_). Se a ordem dos itens de dados tiver mudado, em vez de mover os elementos do DOM para corresponder à nova ordem, Vue simplesmente remendará o conteúdo de cada elemento em seu local atual, garantindo que o resultado reflita o que precisa ser renderizado em cada índice em particular.

Este modo padrão é eficiente, mas **adequado apenas quando seu resultado de renderização não se apoiar em estado de componentes filhos ou estado de DOM temporário (como valores de campos de formulário)**.

Para dar ao Vue uma dica de modo que ele possa rastrear a identidade de cada nó e, assim, reutilizar e reordenar os elementos existentes, você precisa fornecer um atributo `key` exclusivo para cada item:

```html
<div v-for="item in items" :key="item.id">
  <!-- conteúdo -->
</div>
```

É recomendado oferecer um atributo `key` para `v-for` sempre que possível, a menos que esteja iterando sobre um conteúdo DOM simples, ou esteja intencionalmente se apoiando no comportamento padrão para ganho de desempenho.

Por ser um mecanismo genérico do Vue para identificar nós, `key` também tem outras utilidades não especificamente associadas ao `v-for`, como veremos futuramente neste guia.

:::tip Note
Não use valores não primitivos como objetos e arrays como chaves para `v-for`. Em vez disso, use String ou valores numéricos
:::

Para uso detalhado do atributo `key`, por favor veja a [documentação da API `key`](../api/special-attributes.html#key).

## Detecção de Mudanças em Arrays

### Métodos de Mutação

Vue envolve automaticamente os métodos de mutação de um Array observado, de forma que dispare alterações na interface. Os métodos englobados são:

- `push()`
- `pop()`
- `shift()`
- `unshift()`
- `splice()`
- `sort()`
- `reverse()`

Você pode abrir o _console_ e brincar com os `items` dos exemplos anteriores, chamando seus métodos de mutação. Por exemplo: `example1.items.push({ message: 'Baz' })`.

### Substituindo um Array

Métodos de mutação, como o nome sugere, mudam o Array original no qual são chamados. Em comparação, também há métodos sem mutação, como `filter()`, `concat()` e `slice()`, que não modificam o Array original, mas sim **retornam um novo Array**. Ao trabalhar com métodos sem mutação, você pode substituir o Array antigo pelo novo:

```js
example1.items = example1.items.filter(item => item.message.match(/Foo/))
```

Você pode pensar que isto fará o Vue jogar fora todo o DOM existente e "re-renderizar" a lista inteira - por sorte, não é o caso. Vue implementa algumas heurísticas inteligentes para maximizar a reutilização dos elementos DOM, assim, sobrescrever um Array com outro, contendo elementos subjacentes, é uma operação muito eficiente.

## Exibindo Resultados Filtrados/Ordenados

Às vezes, queremos exibir uma versão filtrada ou ordenada de um Array sem efetivamente mudar ou reiniciar seus dados originais. Neste caso, você pode criar um dado computado que retorna um Array filtrado ou ordenado.

Por exemplo:

```html
<li v-for="n in evenNumbers">{{ n }}</li>
```

```js
data() {
  return {
    numbers: [ 1, 2, 3, 4, 5 ]
  }
},
computed: {
  evenNumbers() {
    return this.numbers.filter(number => number % 2 === 0)
  }
}
```

Em situações onde dados computados não são factíveis (por exemplo, em repetições `v-for` aninhadas), você pode usar um método:

```html
<ul v-for="numbers in sets">
  <li v-for="n in even(numbers)">{{ n }}</li>
</ul>
```

```js
data() {
  return {
    sets: [[ 1, 2, 3, 4, 5 ], [6, 7, 8, 9, 10]]
  }
},
methods: {
  even(numbers) {
    return numbers.filter(number => number % 2 === 0)
  }
}
```

## Intervalo Numérico no `v-for`

O `v-for` pode aceitar um número inteiro. Nesse caso, o _template_ se repetirá este número de vezes.

```html
<div id="range" class="demo">
  <span v-for="n in 10">{{ n }} </span>
</div>
```

Resultado:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="vuejs-br" data-slug-hash="ExKrarE" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Exemplo de v-for com intervalo numérico">
  <span>Veja o <a href="https://codepen.io/vuejs-br/pen/ExKrarE">
  Exemplo de v-for com intervalo numérico</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>


## Utilizando `<template>` com `v-for`

Similar ao uso de _template_ com `v-if`, você também pode usar a tag `<template>` com `v-for` para renderizar um bloco de múltiplos elementos. Por exemplo:

```html
<ul>
  <template v-for="item in items">
    <li>{{ item.msg }}</li>
    <li class="divider" role="presentation"></li>
  </template>
</ul>
```

## Utilizando `v-if` com `v-for`

:::tip
**Não** é recomendado usar `v-if` e `v-for` juntos. Consulte o [guia de estilos](../style-guide/#avoid-v-if-with-v-for-essential) para detalhes.
:::

Quando presentes em um mesmo nó, `v-if` tem maior prioridade que `v-for`. Isto significa que o `v-if` não terá acesso às variáveis criadas no escopo do `v-for`:

```html
<!-- Um erro será lançado pois o dado "todo" não está definido nesta instância. -->

<li v-for="todo in todos" v-if="!todo.isComplete">
  {{ todo }}
</li>
```

Isto pode ser resolvido envolvendo-se o `v-for` em uma _tag_ `<template>`:

```html
<template v-for="todo in todos">
  <li v-if="!todo.isComplete">
    {{ todo }}
  </li>
</template>
```

## Componentes com `v-for`

> Esta seção pressupõe que você tem conhecimendo sobre [Componentes](component-basics.md). Sinta-se à vontade para pular e voltar depois.

Você pode usar `v-for` diretamente em um componente personalizado, assim como faz num elemento normal:

```html
<my-component v-for="item in items" :key="item.id"></my-component>
```

No entanto, isso não passará automaticamente nenhum dado ao componente, pois componentes possuem seus próprios escopos isolados. Para passar dados iterados para dentro do componente, devemos utilizar propriedades.

```html
<my-component
  v-for="(item, index) in items"
  :item="item"
  :index="index"
  :key="item.id"
></my-component>
```

A razão para não injetarmos automaticamente `item` dentro do componente é para evitar que ele fique fortemente acoplado ao funcionamento do `v-for`. Explicitar a origem de seus dados torna o componente reutilizável em outras situações.

Aqui temos um exemplo de uma lista de tarefas simples usando componentes:

```html
<div id="todo-list-example">
  <form v-on:submit.prevent="addNewTodo">
    <label for="new-todo">Adicionar tarefa:</label>
    <input
      v-model="newTodoText"
      id="new-todo"
      placeholder="Ex.: Alimentar o gato"
    />
    <button>Adicionar</button>
  </form>
  <ul>
    <todo-item
      v-for="(todo, index) in todos"
      :key="todo.id"
      :title="todo.title"
      @remove="todos.splice(index, 1)"
    ></todo-item>
  </ul>
</div>
```

```js
const app = Vue.createApp({
  data() {
    return {
      newTodoText: '',
      todos: [
        {
          id: 1,
          title: 'Lavar a louça'
        },
        {
          id: 2,
          title: 'Tirar o lixo'
        },
        {
          id: 3,
          title: 'Cortar a grama'
        }
      ],
      nextTodoId: 4
    }
  },
  methods: {
    addNewTodo() {
      this.todos.push({
        id: this.nextTodoId++,
        title: this.newTodoText
      })
      this.newTodoText = ''
    }
  }
})

app.component('todo-item', {
  template: `
    <li>
      {{ title }}
      <button @click="$emit('remove')">Remover</button>
    </li>
  `,
  props: ['title']
})

app.mount('#todo-list-example')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="vuejs-br" data-slug-hash="KKzJwYa" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Exemplo de v-for com componentes">
  <span>Veja o <a href="https://codepen.io/vuejs-br/pen/KKzJwYa">
  Exemplo de v-for com componentes</a> por Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>
