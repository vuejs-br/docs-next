# Transições de Entrada e Saída

O Vue fornece uma variedade de maneiras de aplicar efeitos de transição quando os itens são inseridos, atualizados ou removidos do DOM. Isso inclui ferramentas para:

- aplicar classes automaticamente para transições e animações CSS
- integrar bibliotecas de animação CSS de terceiros, como [Animate.css](https://animate.style/)
- usar o JavaScript para manipular diretamente o DOM durante os hooks de transição
- integrar bibliotecas de animação JavaScript de terceiros

Nesta página, cobriremos apenas as transições de entrada, saída e listagem, mas você pode ver a próxima seção para [gerenciamento de transições de estado](transitions-state.html).

## Transição de Elementos/Componentes Únicos

Vue fornece um componente _wrapper_ chamado de `transition`, permitindo que você adicione transições de entrada/saída para qualquer elemento ou componente nos seguintes contextos:

- Renderização condicional (usando `v-if`)
- Exibição condicional (usando `v-show`)
- Componentes dinâmicos
- Nós raiz do componente

Este é um exemplo em ação:

```html
<div id="demo">
  <button @click="show = !show">
    Alternar
  </button>

  <transition name="fade">
    <p v-if="show">Olá</p>
  </transition>
</div>
```

```js
const Demo = {
  data() {
    return {
      show: true
    }
  }
}

Vue.createApp(Demo).mount('#demo')
```

```css
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="Vue" data-slug-hash="3466d06fb252a53c5bc0a0edb0f1588a" data-preview="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Componente de Transição Simples">
  <span>Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/3466d06fb252a53c5bc0a0edb0f1588a">
  Componente de Transição Simples</a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Quando um elemento envolvido em um componente `transition` é inserido ou removido, isso acontece:

1. O Vue detectará automaticamente se o elemento de destino tem transições CSS ou animações aplicadas. Em caso afirmativo, as classes de transição CSS serão adicionadas/removidas nos momentos apropriados

2. Se o componente de transição fornece [gatilhos JavaScript](#gatilhos-javascript), esses gatilhos serão chamados em momentos apropriados.

3. Se nenhuma transição/animação CSS for detectada e nenhum gatilho JavaScript for fornecido, as operações DOM para inserção e/ou remoção serão executadas imediatamente no próximo quadro (Observação: este é um quadro de animação do navegador, diferente do conceito do Vue de `nextTick`).

### Classes de Transição

Existem seis classes aplicadas em transições de entrada/saída.

1. `v-enter-from`: Estado inicial para entrada. Adicionado antes do elemento ser inserido, removido um quadro após o elemento ser inserido.

2. `v-enter-active`: Estado ativo da entrada. Aplicado durante toda a fase de entrada. Adicionado antes do elemento ser inserido, removido quando a transição/animação termina. Esta classe pode ser usada para definir a duração, atraso e a curva de atenuação para a transição de entrada.

3. `v-enter-to`: Estado final da entrada. Adicionado um quadro após o elemento ser inserido (ao mesmo tempo que `v-enter-from` é removido), removido quando a transição/animação termina.

4. `v-leave-from`: Estado inicial da saída. Adicionado imediatamente quando uma transição de saída é acionada, removido após um quadro.

5. `v-leave-active`: Estado ativo da saída. Aplicado durante toda a fase de saída. Adicionado imediatamente quando a transição de saída é acionada, removido quando a transição/animação termina. Esta classe pode ser usada para definir a duração, atraso e a curva de atenuação para a transição de saída.

6. `v-leave-to`: Estado final da saída. Adicionado um quadro depois que uma transição de saída é acionada (ao mesmo tempo que `v-leave-from` é removido), removido quando a transição/animação termina.

![Diagrama de Transição](/images/transitions.svg)

Cada uma dessas classes será prefixada com o nome da transição. Aqui o prefixo `v-` é o padrão quando você usa um elemento `<transition>` sem nome. Se você usar `<transition name="my-transition">` por exemplo, então a classe `v-enter-from` seria `my-transition-enter-from`.

O `v-enter-active` e `v-leave-active` oferecem a capacidade de especificar curvas de atenuação diferentes para transições de entrada/saída, das quais você verá um exemplo na seção a seguir.

### Transições CSS

Um dos tipos de transição mais comuns usa transições CSS. Aqui está um exemplo:

```html
<div id="demo">
  <button @click="show = !show">
    Alternar renderização
  </button>

  <transition name="slide-fade">
    <p v-if="show">Olá</p>
  </transition>
</div>
```

```js
const Demo = {
  data() {
    return {
      show: true
    }
  }
}

Vue.createApp(Demo).mount('#demo')
```

```css
/* Animações de entrada/saída podem usar diferentes */
/* durações e funções de tempo */
.slide-fade-enter-active {
  transition: all 0.3s ease-out;
}

.slide-fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateX(20px);
  opacity: 0;
}
```

<p class="codepen" data-height="300" data-theme-id="39028"  data-preview="true" data-default-tab="css,result" data-user="Vue" data-slug-hash="0dfa7869450ef43d6f7bd99022bc53e2" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Diferentes Transições de Entrada e Saída">
  <span>Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/0dfa7869450ef43d6f7bd99022bc53e2">
  Diferentes Transições de Entrada e Saída</a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### Animações CSS

As animações CSS são aplicadas da mesma forma que as transições CSS, com a diferença de que `v-enter-from` não é removido imediatamente após o elemento ser inserido, mas em um evento `animationend`.

Aqui está um exemplo, omitindo regras CSS prefixadas por uma questão de brevidade:

```html
<div id="demo">
  <button @click="show = !show">Alternar exibição</button>
  <transition name="bounce">
    <p v-if="show">
      Lorem ipsum dolor sit amet, consectetur adipiscing elit. Mauris facilisis
      enim libero, at lacinia diam fermentum id. Pellentesque habitant morbi
      tristique senectus et netus.
    </p>
  </transition>
</div>
```

```js
const Demo = {
  data() {
    return {
      show: true
    }
  }
}

Vue.createApp(Demo).mount('#demo')
```

```css
.bounce-enter-active {
  animation: bounce-in 0.5s;
}
.bounce-leave-active {
  animation: bounce-in 0.5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.25);
  }
  100% {
    transform: scale(1);
  }
}
```

<p class="codepen" data-height="300" data-theme-id="39028"  data-preview="true" data-default-tab="html,result" data-user="Vue" data-slug-hash="8627c50c5514752acd73d19f5e33a781" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Exemplo de Transição de Animação CSS">
  <span>Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/8627c50c5514752acd73d19f5e33a781">
  Exemplo de Transição de Animação CSS</a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### Classes de Transição Personalizadas

Você também pode especificar classes de transição personalizadas, fornecendo os seguintes atributos:

- `enter-from-class`
- `enter-active-class`
- `enter-to-class`
- `leave-from-class`
- `leave-active-class`
- `leave-to-class`

Eles substituirão os nomes de classe convencionais. Isso é especialmente útil quando você deseja combinar o sistema de transição do Vue com uma biblioteca de animação CSS existente, como [Animate.css](https://daneden.github.io/animate.css/).

Aqui está um exemplo:

```html
<link
  href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.0/animate.min.css"
  rel="stylesheet"
  type="text/css"
/>

<div id="demo">
  <button @click="show = !show">
    Alternar renderização
  </button>

  <transition
    name="custom-classes-transition"
    enter-active-class="animate__animated animate__tada"
    leave-active-class="animate__animated animate__bounceOutRight"
  >
    <p v-if="show">Olá</p>
  </transition>
</div>
```

```js
const Demo = {
  data() {
    return {
      show: true
    }
  }
}

Vue.createApp(Demo).mount('#demo')
```

### Usando Transições e Animações Juntas

O Vue precisa anexar ouvintes de eventos para saber quando uma transição terminou. Pode ser `transitionend` ou `animationend`, 
dependendo do tipo de regras CSS aplicadas. Se você estiver usando apenas um ou outro, o Vue pode detectar automaticamente o tipo correto.

No entanto, em alguns casos, você pode querer ter ambos no mesmo elemento, por exemplo, ter uma animação CSS acionada pelo Vue, junto com um efeito de transição CSS ao passar o mouse. Nestes casos, você terá que declarar explicitamente o tipo que deseja que o Vue se preocupe em um atributo `type`, com um valor de `animation` ou `transition`.

### Durações de Transição Explícitas

<!-- TODO: validar e fornecer um exemplo -->

Na maioria dos casos, o Vue pode descobrir automaticamente quando a transição terminou. Por padrão, Vue espera pelo primeiro evento`transitionend` ou `animationend` no elemento de transição raiz. No entanto, isso pode não ser sempre o desejado - por exemplo, podemos ter uma sequência de transição coreografada onde alguns elementos internos aninhados têm uma transição atrasada ou uma duração de transição mais longa do que o elemento de transição raiz.

Nesses casos, você pode especificar uma duração de transição explícita (em milissegundos) usando a propriedade `duration` no componente `<transition>`:

```html
<transition :duration="1000">...</transition>
```

Você também pode especificar valores separados para durações de entrada e saída

```html
<transition :duration="{ enter: 500, leave: 800 }">...</transition>
```

### Gatilhos JavaScript

Você também pode definir gatilhos JavaScript em atributos:

```html
<transition
  @before-enter="beforeEnter"
  @enter="enter"
  @after-enter="afterEnter"
  @enter-cancelled="enterCancelled"
  @before-leave="beforeLeave"
  @leave="leave"
  @after-leave="afterLeave"
  @leave-cancelled="leaveCancelled"
  :css="false"
>
  <!-- ... -->
</transition>
```

```js
// ...
methods: {
  // --------
  // ENTRANDO
  // --------

  beforeEnter(el) {
    // ...
  },
  // o retorno de chamada 'done' é opcional quando
  // usado em combinação com CSS
  enter(el, done) {
    // ...
    done()
  },
  afterEnter(el) {
    // ...
  },
  enterCancelled(el) {
    // ...
  },

  // --------
  // SAINDO
  // --------

  beforeLeave(el) {
    // ...
  },
  // o retorno de chamada 'done' é opcional quando
  // usado em combinação com CSS
  leave(el, done) {
    // ...
    done()
  },
  afterLeave(el) {
    // ...
  },
  // leaveCancelled apenas disponível with v-show
  leaveCancelled(el) {
    // ...
  }
}
```

Esses gatilhos podem ser usados ​​em combinação com transições/animações CSS ou sozinhos.

Ao usar transições somente JavaScript, **os retornos de chamada `done` são necessários para os gatilhos `enter` e `leave`**. Caso contrário, os gatilhos serão chamados de forma síncrona e a transição terminará imediatamente. Adicionando `:css="false"` também informará ao Vue para pular a detecção de CSS. Além de ter um desempenho um pouco melhor, isso também evita que as regras de CSS interfiram acidentalmente na transição.

Agora vamos examinar um exemplo. Aqui está uma transição de JavaScript usando [GreenSock](https://greensock.com/):

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.3.4/gsap.min.js"></script>

<div id="demo">
  <button @click="show = !show">
    Alternar
  </button>

  <transition
    @before-enter="beforeEnter"
    @enter="enter"
    @leave="leave"
    :css="false"
  >
    <p v-if="show">
      Demo
    </p>
  </transition>
</div>
```

```js
const Demo = {
  data() {
    return {
      show: false
    }
  },
  methods: {
    beforeEnter(el) {
      gsap.set(el, {
        scaleX: 0.8,
        scaleY: 1.2
      })
    },
    enter(el, done) {
      gsap.to(el, {
        duration: 1,
        scaleX: 1.5,
        scaleY: 0.7,
        opacity: 1,
        x: 150,
        ease: 'elastic.inOut(2.5, 1)',
        onComplete: done
      })
    },
    leave(el, done) {
      gsap.to(el, {
        duration: 0.7,
        scaleX: 1,
        scaleY: 1,
        x: 300,
        ease: 'elastic.inOut(2.5, 1)'
      })
      gsap.to(el, {
        duration: 0.2,
        delay: 0.5,
        opacity: 0,
        onComplete: done
      })
    }
  }
}

Vue.createApp(Demo).mount('#demo')
```

<p class="codepen" data-height="300" data-theme-id="39028"  data-preview="true" data-default-tab="js,result" data-user="Vue" data-slug-hash="68ce1b8c41d0a6e71ff58df80fd85ae5" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Transição de Gatilhos JavaScript">
  <span> Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/68ce1b8c41d0a6e71ff58df80fd85ae5">
  Transição de Gatilhos JavaScript</a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Transições na Renderização Inicial

Se você também deseja aplicar uma transição na renderização inicial de um nó, você pode adicionar o atributo `appear`:

```html
<transition appear>
  <!-- ... -->
</transition>
```

## Transição Entre Elementos

Nós discutiremos [transição entre componentes](#transicao-entre-componentes) mais tarde, mas você também pode fazer a transição entre os elementos brutos usando `v-if`/`v-else`. Uma das transições de dois elementos mais comuns é entre um contêiner de lista e uma mensagem que descreve uma lista vazia:

```html
<transition>
  <table v-if="items.length > 0">
    <!-- ... -->
  </table>
  <p v-else>Desculpe, nenhum item encontrado.</p>
</transition>
```

Na verdade, é possível fazer a transição entre qualquer número de elementos, usando vários `v-if` ou vinculando um único elemento a uma propriedade dinâmica. Por exemplo:

<!-- TODO: reescrever o exemplo e colocar no codepen -->

```html
<transition>
  <button v-if="docState === 'saved'" key="saved">
    Editar
  </button>
  <button v-if="docState === 'edited'" key="edited">
    Salvar
  </button>
  <button v-if="docState === 'editing'" key="editing">
    Cancelar
  </button>
</transition>
```

Que também pode ser escrito como:

```html
<transition>
  <button :key="docState">
    {{ buttonMessage }}
  </button>
</transition>
```

```js
// ...
computed: {
  buttonMessage() {
    switch (this.docState) {
      case 'saved': return 'Editar'
      case 'edited': return 'Salvar'
      case 'editing': return 'Cancelar'
    }
  }
}
```

### Modos de Transição

Ainda há um problema. Experimente clicar no botão abaixo:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="Rwrqzpr" data-preview="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Problema do Botão dos Modos de Transição">
  <span>Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/Rwrqzpr">
  Problema do Botão dos Modos de Transição</a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

À medida que faz a transição entre o botão "on" e o botão "off", os dois botões são renderizados - um faz a transição para fora e o outro para dentro. Este é o comportamento padrão do `<transition>` - entrada e saída acontecem simultaneamente.

Às vezes, isso funciona muito bem, como quando os itens em transição são absolutamente posicionados um sobre o outro:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="abdQgLr" data-preview="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Problema do Botão dos Modos de Transição - Posicionamento">
  <span>Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/abdQgLr">
  Problema do Botão dos Modos de Transição - Posicionamento </a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Às vezes, isso não é uma opção, ou estamos lidando com um movimento mais complexo onde os estados de entrada e saída precisam ser coordenados, então o Vue oferece um utilitário extremamente útil chamado **modos de transição**:

- `in-out`: Primeiro o novo elemento transita entrando, quando concluído, o elemento atual transita pra fora.
- `out-in`: Primeiro o elemento atual transita saindo, quando concluído, o novo elemento transita entrando.

::: tip Dica
Você descobrirá muito rapidamente que `out-in` é o estado que você desejará na maior parte do tempo :)
:::

Agora vamos atualizar a transição para nossos botões on/off com `out-in`:

```html
<transition name="fade" mode="out-in">
  <!-- ... os botões ... -->
</transition>
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="ZEQmdvq" data-preview="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Problema do Botão dos Modos de Transição - resolvido">
  <span>Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/ZEQmdvq">
  Problema do Botão dos Modos de Transição - resolvido</a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Com a adição de um atributo, corrigimos a transição original sem precisar adicionar nenhum estilo especial.

Podemos usar isso para coordenar movimentos mais expressivos, como um cartão dobrável, conforme demonstrado a seguir. Na verdade, são dois elementos em transição entre si, mas como os estados inicial e final estão na mesma escala: horizontalmente para 0, parece um movimento fluido. Este tipo de prestidigitação pode ser muito útil para microinterações de IU realistas:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="76e344bf057bd58b5936bba260b787a8" data-preview="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Modos de Transição Flip Cards">
  <span>Veja um exemplo de  <a href="https://codepen.io/team/Vue/pen/76e344bf057bd58b5936bba260b787a8">
  Modos de Transição Flip Cards</a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Transição Entre Componentes

A transição entre componentes é ainda mais simples - nem precisamos do atributo `key`. Em vez disso, envolvemos um [componente dinâmico](component-basics.html#componentes-dinamicos):

```html
<div id="demo">
  <input v-model="view" type="radio" value="v-a" id="a"><label for="a">A</label>
  <input v-model="view" type="radio" value="v-b" id="b"><label for="b">B</label>
  <transition name="component-fade" mode="out-in">
    <component :is="view"></component>
  </transition>
</div>
```

```js
const Demo = {
  data() {
    return {
      view: 'v-a'
    }
  },
  components: {
    'v-a': {
      template: '<div>Componente A</div>'
    },
    'v-b': {
      template: '<div>Componente B</div>'
    }
  }
}

Vue.createApp(Demo).mount('#demo')
```

```css
.component-fade-enter-active,
.component-fade-leave-active {
  transition: opacity 0.3s ease;
}

.component-fade-enter-from,
.component-fade-leave-to {
  opacity: 0;
}
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="Vue" data-slug-hash="WNwVxZw" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Transição Entre Componentes">
  <span>Veja o exemplo <a href="https://codepen.io/team/Vue/pen/WNwVxZw">
  Transição Entre Componentes</a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>
