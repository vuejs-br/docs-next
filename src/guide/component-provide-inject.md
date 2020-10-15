# Prover e Injetar Dados

> Esta página assume que você já leu o [Básico sobre Componentes](component-basics.md). Leia lá primeiro se você for novo em componentes.

Normalmente, quando precisamos passar dados do componente pai para o filho, usamos [propriedades](component-props.md) (também conhecidas como _props_). Imagine a estrutura em que você tem alguns componentes profundamente aninhados e precisa de algo do componente pai apenas num componente filho que está profundamente aninhado. Nesse caso, você precisaria passar a propriedade por toda a cadeia de componentes, o que poderia ser irritante.

Nessas situações, podemos usar o par `provide` e `inject`. Os componentes pai podem servir como provedores de dependência para todos os seus filhos, independentemente da profundidade da hierarquia do componente. Este recurso funciona em duas partes: o componente pai tem uma opção `provide` para fornecer dados e o componente filho tem uma opção `inject` para usar esses dados.

![Provide/inject scheme](/images/components_provide.png)

Por exemplo, se tivéssemos uma hierarquia como esta:

```
Raiz
└─ TodoList
   ├─ TodoItem
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

Se quiséssemos passar o tamanho da lista de tarefas diretamente para `TodoListStatistics`, passaríamos a propriedade por toda hierarquia: `TodoList` -> `TodoListFooter` -> `TodoListStatistics`. Com a abordagem de prover/injetar, podemos fazer isso diretamente:

```js
const app = Vue.createApp({})

app.component('todo-list', {
  data() {
    return {
      todos: ['Alimentar o gato', 'Comprar ingressos']
    }
  },
  provide: {
    user: 'John Doe'
  },
  template: `
    <div>
      {{ todos.length }}
      <!-- restante do _template_ -->
    </div>
  `
})

app.component('todo-list-statistics', {
  inject: ['user'],
  created() {
    console.log(`Propriedade injetada: ${this.user}`) // > Propriedade injetada: John Doe
  }
})
```

No entanto, isso não funcionará se tentarmos prover alguma propriedade da instância do componente:

```js
app.component('todo-list', {
  data() {
    return {
      todos: ['Alimentar o gato', 'Comprar ingressos']
    }
  },
  provide: {
    todoLength: this.todos.length // isso resultará no erro `Cannot read property 'length' of undefined`
  },
  template: `
    ...
  `
})
```

Para acessar as propriedades da instância do componente, precisamos converter `provide` em uma função que retorna um objeto

```js
app.component('todo-list', {
  data() {
    return {
      todos: ['Alimentar o gato', 'Comprar ingressos']
    }
  },
  provide() {
    return {
      todoLength: this.todos.length
    }
  },
  template: `
    ...
  `
})
```

Isso nos permite continuar desenvolvendo esse componente com mais segurança, sem medo de que possamos alterar/remover algo em que um componente filho dependa. A interface entre esses componentes permanece claramente definida, similar à `props`.

Na verdade, você pode pensar na injeção de dependência como uma espécie de "`props` de longo alcance", exceto:

- os componentes pai não precisam saber quais descendentes usam as propriedades que ele fornece
- os componentes filhos não precisam saber de onde vêm as propriedades injetadas

## Trabalhando com Reatividade

No exemplo acima, se alterarmos a lista de tarefas, essa alteração não será refletida na propriedade injetada `todoLength`. Isso ocorre porque as interligações `provide/inject` **não** são reativas por padrão. Podemos mudar esse comportamento passando uma propriedade `ref` ou objeto reativo (`reactive`) para `provide`. Em nosso exemplo, se quiséssemos reagir às mudanças no componente ancestral, precisaríamos atribuir um dado computado (usando o `computed` da API de Composição) para nosso `todoLength` fornecido:

```js
app.component('todo-list', {
  // ...
  provide() {
    return {
      todoLength: Vue.computed(() => this.todos.length)
    }
  }
})
```

Assim, qualquer alteração em `todos.length` será refletida corretamente nos componentes, onde `todoLength` é injetado. Leia mais sobre `provide/inject` de forma reativa na [seção da API de Composição](composition-api-provide-inject.html#injection-reactivity)
