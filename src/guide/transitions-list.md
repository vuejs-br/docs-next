# Transições de Listas

Até agora, gerenciamos transições para:

- Nós individuais
- Multiplos nós onde apenas 1 é renderizado por vez

E quando tivermos toda uma lista de itens que queremos renderizar simultâneamente, com `v-for` por exemplo? Neste caso, usaremos o componente `<transition-group>`. Antes de entramos em um exemplo, existem algumas coisas que são importantes saber sobre esse componente:

- Ao contrário do `<transition>`, ele renderiza um elemento real: um `<span>` por padrão. Você pode alterar o elemento que é renderizado com o atributo `tag`.
- Os [modos de transição](/guide/transitions-enterleave#transition-modes) não estão disponíveis, porque não estamos mais alternando entre elementos mutuamente exclusivos.
- Os elementos internos **sempre precisam** ter um atributo `key` único.
- As classes de transições CSS serão aplicadas aos elementos internos e não ao grupo/contêiner em si.

### Transições de Entrada/Saída em Listas

Agora iremos exemplificar, fazendo transições de entrada e saída usando as mesmas classes CSS que usamos anteriormente:

```html
<div id="list-demo">
  <button @click="add">Adicionar</button>
  <button @click="remove">Remover</button>
  <transition-group name="list" tag="p">
    <span v-for="item in items" :key="item" class="list-item">
      {{ item }}
    </span>
  </transition-group>
</div>
```

```js
const Demo = {
  data() {
    return {
      items: [1, 2, 3, 4, 5, 6, 7, 8, 9],
      nextNum: 10
    }
  },
  methods: {
    randomIndex() {
      return Math.floor(Math.random() * this.items.length)
    },
    add() {
      this.items.splice(this.randomIndex(), 0, this.nextNum++)
    },
    remove() {
      this.items.splice(this.randomIndex(), 1)
    }
  }
}

Vue.createApp(Demo).mount('#list-demo')
```

```css
.list-item {
  display: inline-block;
  margin-right: 10px;
}
.list-enter-active,
.list-leave-active {
  transition: all 1s ease;
}
.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="Vue" data-slug-hash="e1cea580e91d6952eb0ae17bfb7c379d" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Transition List">
  <span>Veja o Pen <a href="https://codepen.io/team/Vue/pen/e1cea580e91d6952eb0ae17bfb7c379d">
  Transição de Listas</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Existe um problema com esse exemplo. Quando você adiciona ou remove um item, os que estão ao redor se encaixam instantaneamente em seu novo lugar, em vez de realizarem uma transição suave. Corrigiremos isso depois.

### Transições de Movimento em Listas

O componente `<transition-group>` tem outro truque na manga. Ele não apenas anima entrada e saída, como também anima mudanças nas posições. O único conceito novo que você precisa saber para usar esse recurso é a adição da **classe `v-move`**, que é adicionada quando os items estão mudando de posição. Assim como as outras classes, esse prefixo irá corresponder ao valor do atributo `name` fornecido e você pode especificar manualmente uma classe com o atributo `move-class`.

Essa classe é útil principalmente para especificar o tempo de transição e a curva de atenuação, como você pode ver abaixo:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.15/lodash.min.js"></script>

<div id="flip-list-demo">
  <button @click="shuffle">Misturar</button>
  <transition-group name="flip-list" tag="ul">
    <li v-for="item in items" :key="item">
      {{ item }}
    </li>
  </transition-group>
</div>
```

```js
const Demo = {
  data() {
    return {
      items: [1, 2, 3, 4, 5, 6, 7, 8, 9]
    }
  },
  methods: {
    shuffle() {
      this.items = _.shuffle(this.items)
    }
  }
}

Vue.createApp(Demo).mount('#flip-list-demo')
```

```css
.flip-list-move {
  transition: transform 0.8s ease;
}
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="Vue" data-slug-hash="049211673d3c185fde6b6eceb8baebec" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Transition-group example">
  <span>Veja o Pen <a href="https://codepen.io/team/Vue/pen/049211673d3c185fde6b6eceb8baebec">
  Exemplo de transition-group</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Isso pode parecer mágica, mas por baixo dos panos, o Vue está usando uma técnica de animação chamada [FLIP](https://aerotwist.com/blog/flip-your-animations/) para transicionar suavemente os elementos de sua antiga posição para a nova posição usando transformações.

Podemos combinar essa técnica com nossa implementação anterior para animar qualquer mudança possível em nossa lista!

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.14.1/lodash.min.js"></script>

<div id="list-complete-demo" class="demo">
  <button @click="shuffle">Misturar</button>
  <button @click="add">Adicionar</button>
  <button @click="remove">Remover</button>
  <transition-group name="list-complete" tag="p">
    <span v-for="item in items" :key="item" class="list-complete-item">
      {{ item }}
    </span>
  </transition-group>
</div>
```

```js
const Demo = {
  data() {
    return {
      items: [1, 2, 3, 4, 5, 6, 7, 8, 9],
      nextNum: 10
    }
  },
  methods: {
    randomIndex() {
      return Math.floor(Math.random() * this.items.length)
    },
    add() {
      this.items.splice(this.randomIndex(), 0, this.nextNum++)
    },
    remove() {
      this.items.splice(this.randomIndex(), 1)
    },
    shuffle() {
      this.items = _.shuffle(this.items)
    }
  }
}

Vue.createApp(Demo).mount('#list-complete-demo')
```

```css
.list-complete-item {
  transition: all 0.8s ease;
  display: inline-block;
  margin-right: 10px;
}

.list-complete-enter-from,
.list-complete-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

.list-complete-leave-active {
  position: absolute;
}
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="Vue" data-slug-hash="373b4429eb5769ae2e6d097fd954fd08" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Transition-group example">
  <span>Veja o Pen <a href="https://codepen.io/team/Vue/pen/373b4429eb5769ae2e6d097fd954fd08">
  Exemplo transition-group</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

::: tip Nota
É importante notar que essas transições FLIP não funcionam com elementos configurados com `display: inline`. Alternativamente, você pode usar `display: inline-block` ou posicionar os elementos em um contexto _flex_.
:::

Essas animações FLIP não são limitadas à um único eixo. Itens em um _grid_ multidimensional podem ser [transicionados também](https://codesandbox.io/s/github/vuejs/vuejs.org/tree/master/src/v2/examples/vue-20-list-move-transitions):

TODO: exemplo

### Escalonando Transições de Listas

Comunicando-se com transições JavaScript por meio de atributos de dados, também é possível escalonar as transições em uma lista:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.3.4/gsap.min.js"></script>

<div id="demo">
  <input v-model="query" />
  <transition-group
    name="staggered-fade"
    tag="ul"
    :css="false"
    @before-enter="beforeEnter"
    @enter="enter"
    @leave="leave"
  >
    <li
      v-for="(item, index) in computedList"
      :key="item.msg"
      :data-index="index"
    >
      {{ item.msg }}
    </li>
  </transition-group>
</div>
```

```js
const Demo = {
  data() {
    return {
      query: '',
      list: [
        { msg: 'Bruce Lee' },
        { msg: 'Jackie Chan' },
        { msg: 'Chuck Norris' },
        { msg: 'Jet Li' },
        { msg: 'Kung Fury' }
      ]
    }
  },
  computed: {
    computedList() {
      var vm = this
      return this.list.filter(item => {
        return item.msg.toLowerCase().indexOf(vm.query.toLowerCase()) !== -1
      })
    }
  },
  methods: {
    beforeEnter(el) {
      el.style.opacity = 0
      el.style.height = 0
    },
    enter(el, done) {
      gsap.to(el, {
        opacity: 1,
        height: '1.6em',
        delay: el.dataset.index * 0.15,
        onComplete: done
      })
    },
    leave(el, done) {
      gsap.to(el, {
        opacity: 0,
        height: 0,
        delay: el.dataset.index * 0.15,
        onComplete: done
      })
    }
  }
}

Vue.createApp(Demo).mount('#demo')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="Vue" data-slug-hash="c2fc5107bd3025ceadea049b3ee44ec0" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Staggered Lists">
  <span>Veja o Pen <a href="https://codepen.io/team/Vue/pen/c2fc5107bd3025ceadea049b3ee44ec0">
  Listas Escalonadas</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Transições Reutilizáveis

Transições podem ser reutilizadas através do sistema de componentes do Vue. Para criar uma transição reutilizável tudo o que você precisa fazer é colocar um componente `<transition>` ou `<transition-group>` na raiz, e então passar qualquer filho para o componente de transição.

TODO: refatorar para o Vue 3

Veja um exemplo usando um componente com _template_:

```js
Vue.component('my-special-transition', {
  template: '\
    <transition\
      name="very-special-transition"\
      mode="out-in"\
      @before-enter="beforeEnter"\
      @after-enter="afterEnter"\
    >\
      <slot></slot>\
    </transition>\
  ',
  methods: {
    beforeEnter(el) {
      // ...
    },
    afterEnter(el) {
      // ...
    }
  }
})
```

[Componentes funcionais](render-function.html#Functional-Components) são especialmente adequados para esta tarefa:

```js
Vue.component('my-special-transition', {
  functional: true,
  render: function(createElement, context) {
    var data = {
      props: {
        name: 'very-special-transition',
        mode: 'out-in'
      },
      on: {
        beforeEnter(el) {
          // ...
        },
        afterEnter(el) {
          // ...
        }
      }
    }
    return createElement('transition', data, context.children)
  }
})
```

## Transições Dinâmicas

Sim, até mesmo as transições no Vue são baseadas em dados! O exemplo mais trivial de uma transição dinâmica vincula o atributo `name` à uma propriedade dinâmica.

```html
<transition :name="transitionName">
  <!-- ... -->
</transition>
```

Isso pode ser útil quando você definiu transições/animações CSS usando as convenções de classes de transição do Vue e quer alternar entre elas.

Na verdade, qualquer atributo de transição pode ser vinculado dinamicamente. E não somente atributos. Como gatilhos de eventos são métodos, eles possuem acesso à qualquer dado no contexto. Isso significa que, dependendo do estado do seu componente, as transições do JavaScript podem se comportar de maneira diferente.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/velocity/1.2.3/velocity.min.js"></script>

<div id="dynamic-fade-demo" class="demo">
  Fade In:
  <input type="range" v-model="fadeInDuration" min="0" :max="maxFadeDuration" />
  Fade Out:
  <input
    type="range"
    v-model="fadeOutDuration"
    min="0"
    :max="maxFadeDuration"
  />
  <transition
    :css="false"
    @before-enter="beforeEnter"
    @enter="enter"
    @leave="leave"
  >
    <p v-if="show">olá</p>
  </transition>
  <button v-if="stop" @click="stop = false; show = false">
    Iniciar animação
  </button>
  <button v-else @click="stop = true">Parar!</button>
</div>
```

```js
const app = Vue.createApp({
  data() {
    return {
      show: true,
      fadeInDuration: 1000,
      fadeOutDuration: 1000,
      maxFadeDuration: 1500,
      stop: true
    }
  },
  mounted() {
    this.show = false
  },
  methods: {
    beforeEnter(el) {
      el.style.opacity = 0
    },
    enter(el, done) {
      var vm = this
      Velocity(
        el,
        { opacity: 1 },
        {
          duration: this.fadeInDuration,
          complete: function() {
            done()
            if (!vm.stop) vm.show = false
          }
        }
      )
    },
    leave(el, done) {
      var vm = this
      Velocity(
        el,
        { opacity: 0 },
        {
          duration: this.fadeOutDuration,
          complete: function() {
            done()
            vm.show = true
          }
        }
      )
    }
  }
})

app.mount('#dynamic-fade-demo')
```

TODO: exemplo

Finalmente, a melhor maneira de criar transições dinâmicas é por meio de componentes que aceitam propriedades para mudar a natureza das transições à serem usadas. Pode soar clichê, mas o único limite realmente é sua imaginação.
