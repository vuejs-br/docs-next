# Advertência de Alterações Detectadas no Vue 2

> Esta página se aplica apenas para o Vue 2.x e inferiores, e assume que você já tenha lido a [Seção de Reatividade](reactivity.md). Por favor leia esta seção primeiro.

Devido a limitações no Javascript, existem alguns tipos de mudanças que o Vue **não consegue detectar**. No entanto, existem formas de contorná-las para preservar a reatividade.

### Para *Objects*

Vue não consegue detectar a inserçãoa ou remoção de propriedades. Como o Vue realiza o processo de conversão *getter*/*setter* durante a inicialização da instância, uma propriedade precisa estar presente no objeto `data` para que o Vue possa convertê-lo e torná-lo reativo. Por exemplo:

```js
var vm = new Vue({
  data: {
    a: 1
  }
})
// `vm.a` agora é reativa

vm.b = 2
// `vm.b` não é reativa
```

Vue não permite adicionar dinamicamente novas propriedades nível-raiz (*root-level*) a uma instância já criada. Contudo, é possível adicionar propriedades reativaas a um objeto aninhado usando o método `Vue.set(object, propertyName, value)`:

```js
Vue.set(vm.someObject, 'b', 2)
```

Você pode também utilizar o método da instância `vm.$set`, que é uma referência ao método global `Vue.set`:

```js
this.$set(this.someObject, 'b', 2)
```

Às vezes você pode querer atribuir mais de uma propriedade para um objeto existente, por exemplo usando `Object.assign()` ou `_.extend()`. No entanto, novas propriedades addicionadas ao objeto não irão acionar mudanças. Nestes casos, crie um objeto novo (*fresh object*) com propriedades de ambos o origial e o alterado (*mixing object*):

```js
// invés de `Object.assign(this.someObject, { a: 1, b: 2 })`
this.someObject = Object.assign({}, this.someObject, { a: 1, b: 2 })
```

### Para *Arrays*

Vue não consegue detectar as seguintes mudanças em um *array*:

1. Quando você atribuir diretamente um item através do índice, e.g. `vm.items[indexOfItem] = newValue`
2. Quando você modifica o comprimento de um *array*, e.g. `vm.items.length = newLength`

Por exemplo:

```js
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x' // NÃO é reativo
vm.items.length = 2 // NÃO é reativo
```

Para contornar a advertência 1, ambas instruções irão fazer o mesmo que `vm.items[indexOfItem] = newValue`, mas irão também acionar as atualizações no estado do sistema reativo:

```js
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
```

```js
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```

Você pode também usar o método [`vm.$set`](https://vuejs.org/v2/api/#vm-set) da instância que é uma referência ao método global `Vue.set`:

```js
vm.$set(vm.items, indexOfItem, newValue)
```

Para lidar com a advertência 2, você pode usar o método `splice`:

```js
vm.items.splice(newLength)
```

## Declarando Propriedades Reativas

Como o Vue não permite inserir propriedades de nível raíz (*root-level*) dinamicamente, você tem que inicializar a instância de um componente declarando todas as propriedades de nível raíz (*root-level*) já inicializadas, mesmo que com valores vazios:

```js
var vm = new Vue({
  data: {
    // declara message com o valor vazio
    message: ''
  },
  template: '<div>{{ message }}</div>'
})
// atribui valor a `message` posteriormente
vm.message = 'Hello!'
```

Se você não declarar a propriedade `message` no objeto data, Vue irá avisar você que a função *render* está tenntando acessar uma propriedade que não exite.

Existem razões técnicas por tráz desta restrição - elas eliminam um conjunto de casos extremos no sistema de rastreamento de dependências, além de fazer com que as istâncias dos componenntes funcionem melhor com o sistema de checagem de tipo. Porém há também uma consideração importante em termos de manutennção de código: o objeto `data` funciona como um *schema* para o estado do seu componente. Declarando todas as propriedades reativas inicialmente faz com que o código do componente seja mais fácil de entender quando revisitado posteriormente ou lido po outro desenvolvedor.

## Fila de Atualização Assíncrona (*Async Update Queue*)

No caso de você não ter notado ainda, Vue realiza atualizações na DOM de forma **assíncrona**. Sempre que uma mudança de dados é observada, é aberta uma fila e armazenado em *buffer* todas as alterações que ocorreram no mesmo ciclo (*event loop*). Se o mesmo observador é acionado muitas vezes, ele será inserido na fila apenas uma vez. Essa eliminação de duplicatas em *buffer* é importante para evitar cálculos e manipulações da DOM desnecessários. Então, no próximo ciclo *"tick"*, Vue limpla a fila e executa o trabalho atual (já desduplicado). Internamente Vue tenta utilizar nativamente `Promise.then`, `MutationObserver`, e `setImmediate` para a enfileiração e retorna para o `setTimeout(fn, 0)`.

Por exemplo, quando você atribui `vm.someData = 'new value'`, o componente não irá renderizar novamente de imadiato. Ele irá atualizar no próximo *"tick"*, quando a fila for limpa. Na maioria dos casos nós não precisamos nos preocupar com isso, mas isto pode complicar quando você quer fazer algo que dependa da pós-atualização do estado da DOM (*post-update DOM state*). Contudo Vue.js geralmente encoraja os desenvolvedores a pensar de um modo *"data-driven"* e evitar tocar na DOM diretamente, em alguns casos pode ser necessário sujar as mãos. A fim de esperar até o Vue.js ter finalizado a atualização da DOM depois de uma mudança de *data*, você pode usar `Vue.nextTick(callback)` imediatamente depois da mudança de *data*. O *callback* irá ser chamado depois que a DOM for atualizada. Por exemplo: 

```html
<div id="example">{{ message }}</div>
```

```js
var vm = new Vue({
  el: '#example',
  data: {
    message: '123'
  }
})
vm.message = 'new message' // altera data
vm.$el.textContent === 'new message' // falso
Vue.nextTick(function() {
  vm.$el.textContent === 'new message' // verdadeiro
})
```

Existe também o método da instância `vm.$nextTick()`, que é especialmente eficaz dentro de componentes, pois não precisa da global `Vue` e seus *calback's* e o contexto da variável `this`será automaticamente se limitar ao componente atual: 

```js
Vue.component('example', {
  template: '<span>{{ message }}</span>',
  data: function() {
    return {
      message: 'not updated'
    }
  },
  methods: {
    updateMessage: function() {
      this.message = 'updated'
      console.log(this.$el.textContent) // => 'não atualizad'
      this.$nextTick(function() {
        console.log(this.$el.textContent) // => 'atualizado'
      })
    }
  }
})
```

Como `$nextTick()` retorna uma *promise*, você pode ter o mesmo efeito acima usando a nova sitaxe [ES2017 async/await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function): 

```js
  methods: {
    updateMessage: async function () {
      this.message = 'updated'
      console.log(this.$el.textContent) // => 'não atualizado'
      await this.$nextTick()
      console.log(this.$el.textContent) // => 'atualizado'
    }
  }
```
