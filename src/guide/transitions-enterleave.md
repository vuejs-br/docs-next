# Entrar e sair das transições

O Vue fornece uma variedade de maneiras de aplicar efeitos de transição quando os itens são inseridos, atualizados ou removidos do DOM. Isso inclui ferramentas para:

- aplicar classes automaticamente para transições e animações CSS
- integrar bibliotecas de animação CSS de terceiros, como [Animate.css](https://animate.style/)
- usar o JavaScript para manipular diretamente o DOM durante os hooks de transição

Nesta página, cobriremos apenas as transições de entrada, saída e lista, mas você pode ver a próxima seção para [gerenciamento de transições de estado](transitions-state.html).

## Transição de elementos / componentes únicos

Vue fornece um componente wrapper chamado de `transition`, permitindo que você adicione transições de entrada/saída para qualquer elemento ou componente nos seguintes contextos:

- Renderização condicional (usando `v-if`)
- Exibição condicional (usando `v-show`)
- Componentes dinâmicos
- Nós raiz do componente

Este é um exemplo em ação:

```html
<div id="demo">
  <button @click="show = !show">
    Toggle
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

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="Vue" data-slug-hash="3466d06fb252a53c5bc0a0edb0f1588a" data-preview="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Simple Transition Component">
  <span>Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/3466d06fb252a53c5bc0a0edb0f1588a">
  Componente de transição simples </a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Quando um elemento está envolvido em uma `transition` o componente é inserido ou removido, isto é o que acontece:

1. O Vue detectará automaticamente se o elemento de destino tem transições CSS ou animações aplicadas. Em caso afirmativo, as classes de transição CSS serão adicionadas / removidas nos momentos apropriados

2. Se o componente de transição fornecido [JavaScript hooks](#javascript-hooks), esses hooks serão chamados em momentos apropriados.

3. Se nenhuma transição/animação CSS for detectada e nenhum hooks JavaScript for fornecido, as operações DOM para inserção ou remoção serão executadas imediatamente no próximo quadro (Observação: este é um quadro de animação do navegador, diferente do conceito de Vue de `nextTick`).

### classes de transição

Tem seis classes aplicado para entrar/sair de transições.

1. `v-enter-from`: Estado inicial para entrar. Adicionado antes do elemento ser inserido, removido um quadro após o elemento ser inserido.

2. `v-enter-active`: Estado ativo para entrar. Aplicado durante toda a fase de entrada. Adicionado antes de o elemento ser inserido, removido quando a transição/animação termina. Esta classe pode ser usada para definir a duração, o atraso e a curva de atenuação para a transição de entrada.

3. `v-enter-to`: **Disponível apenas em versões 2.1.8+.** Estado final para entrar. Adicionado um quadro após o elemento ser inserido (ao mesmo tempo `v-enter` é removido), removido quando a transição/animação termina.

4. `v-leave-from`: Estado inicial para licença. Adicionado imediatamente quando uma transição de saída é acionada, removido após um quadro.

5. `v-leave-active`: Estado ativo para licença. Aplicado durante toda a fase de saída. Adicionado imediatamente quando a transição de licença é acionada, removido quando a transição/animação termina. Esta classe pode ser usada para definir a duração, o atraso e a curva de atenuação para a transição de saída.

6. `v-leave-to`: **Disponível apenas em versões 2.1.8+.** Estado final para licença. Adicionado um quadro depois que uma transição de saída é acionada (ao mesmo tempo `v-leave` é removido), removido quando a transição/animação termina.

![Diagrama de Transição](/images/transitions.svg)

Cada uma dessas classes será prefixada com o nome da transição. Aqui o `v-` prefixo é o padrão, quando você usa um `<transition>` elemento sem nome. Se você usar `<transition name="my-transition">` por exemplo, então o `v-enter-from` classe seria em vez disso `my-transition-enter-from`.

O `v-enter-active` e `v-leave-active` oferecem a capacidade de especificar curvas de atenuação diferentes para transições de entrada/saída, das quais você verá um exemplo na seção a seguir.

### Transições CSS

Um dos tipos de transição mais comuns usa transições CSS. Aqui está um exemplo:

```html
<div id="demo">
  <button @click="show = !show">
    Toggle render
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
/* Enter and leave animations can use different */
/* durations and timing functions.              */
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

<p class="codepen" data-height="300" data-theme-id="39028"  data-preview="true" data-default-tab="css,result" data-user="Vue" data-slug-hash="0dfa7869450ef43d6f7bd99022bc53e2" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Different Enter and Leave Transitions">
  <span>Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/0dfa7869450ef43d6f7bd99022bc53e2">
  Diferentes transições de entrada e saída </a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### CSS Animations

As animações CSS são aplicadas da mesma forma que as transições CSS, com a diferença de que `v-enter-from` não é removido imediatamente após o elemento ser inserido, mas em um `animationend` evento.

Aqui está um exemplo, omitindo regras CSS prefixadas por uma questão de brevidade:

```html
<div id="example-2">
  <button @click="show = !show">Toggle show</button>
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
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
```

<p class="codepen" data-height="300" data-theme-id="39028"  data-preview="true" data-default-tab="html,result" data-user="Vue" data-slug-hash="8627c50c5514752acd73d19f5e33a781" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="CSS Animation Transition Example">
  <span>Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/8627c50c5514752acd73d19f5e33a781">
  Exemplo de transição de animação CSS</a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

### Classes de transição personalizadas

Você também pode especificar classes de transição personalizadas, fornecendo os seguintes atributos:

- `enter-from-class`
- `enter-active-class`
- `enter-to-class` (2.1.8+)
- `leave-from-class`
- `leave-active-class`
- `leave-to-class` (2.1.8+)

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
    Toggle render
  </button>

  <transition
    name="custom-classes-transition"
    enter-active-class="animate__animated animate__tada"
    leave-active-class="animate__animated animate__bounceOutRight"
  >
    <p v-if="show">hello</p>
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

### Usando transições e animações juntas

O Vue precisa anexar ouvintes de eventos para saber quando uma transição terminou. Pode ser `transitionend` ou `animationend`, 
dependendo do tipo de regras CSS aplicadas. Se você estiver usando apenas um ou outro, o Vue pode detectar automaticamente o tipo correto.

No entanto, em alguns casos, você pode querer ter ambos no mesmo elemento, por exemplo, ter uma animação CSS acionada por Vue, junto com um efeito de transição CSS ao passar o mouse. Nestes casos, você terá que declarar explicitamente o tipo que deseja que o Vue se preocupe em um `type` atributo, com um valor de qualquer `animation` ou `transition`.

### Durações de transição explícitas

TODO: validar e fornecer um exemplo

> Apartir da 2.2.0+

Na maioria dos casos, o Vue pode descobrir automaticamente quando a transição terminou. Por padrão, Vue espera pelo primeiro`transitionend` ou `animationend` evento no elemento de transição raiz. No entanto, isso pode não ser sempre desejado - por exemplo, podemos ter uma sequência de transição coreografada onde alguns elementos internos aninhados têm uma transição atrasada ou uma duração de transição mais longa do que o elemento de transição raiz.

Nesses casos, você pode especificar uma duração de transição explícita (em milissegundos) usando o `duration` suporte no componente `<transition>`:

```html
<transition :duration="1000">...</transition>
```

Você também pode especificar valores separados para durações de entrada e saída

```html
<transition :duration="{ enter: 500, leave: 800 }">...</transition>
```

### JavaScript Hooks

Você também pode definir hooks de JavaScript em atributos:

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
  // o retorno de chamada feito é opcional quando
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
  // PARTINDO
  // --------

  beforeLeave(el) {
    // ...
  },
  // o retorno de chamada feito é opcional quando
  // usado em combinação com CSS
  leave(el, done) {
    // ...
    done()
  },
  afterLeave(el) {
    // ...
  },
  // leaveCancelled Apenas disponível with v-show
  leaveCancelled(el) {
    // ...
  }
}
```

Esses hooks podem ser usados ​​em combinação com transições / animações CSS ou sozinhos.

Ao usar transições somente JavaScript, **o `done` retornos de chamada são necessários para o `enter` e `leave` hooks**. assim, os hooks serão chamados de forma síncrona e a transição terminará imediatamente. Adicionando `:css="false"` também informará ao Vue para pular a detecção de CSS. Além de ter um desempenho um pouco melhor, isso também evita que as regras de CSS interfiram acidentalmente na transição.

Agora vamos examinar um exemplo. Aqui está uma transição de JavaScript usando [GreenSock](https://greensock.com/):

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/gsap/3.3.4/gsap.min.js"></script>

<div id="demo">
  <button @click="show = !show">
    Toggle
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

<p class="codepen" data-height="300" data-theme-id="39028"  data-preview="true" data-default-tab="js,result" data-user="Vue" data-slug-hash="68ce1b8c41d0a6e71ff58df80fd85ae5" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="JavaScript Hooks Transition">
  <span> Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/68ce1b8c41d0a6e71ff58df80fd85ae5">
  Transição de hooks de JavaScript</a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Transições na renderização inicial

Se você também deseja aplicar uma transição na renderização inicial de um nó, você pode adicionar o atributo `appear`:

```html
<transition appear>
  <!-- ... -->
</transition>
```

## Transição entre os elementos

Nós discutimos [transição entre componentes](#transitioning-between-components) mais tarde, mas você também pode fazer a transição entre os elementos brutos usando `v-if`/`v-else`. Uma das transições de dois elementos mais comuns é entre um contêiner de lista e uma mensagem que descreve uma lista vazia:

```html
<transition>
  <table v-if="items.length > 0">
    <!-- ... -->
  </table>
  <p v-else>Desculpe, nenhum item encontrado.</p>
</transition>
```

Na verdade, é possível fazer a transição entre qualquer número de elementos, usando vários `v-if` ou vinculando um único elemento a uma propriedade dinâmica. Por exemplo:

TODO: reescrever o exemplo e colocar no exemplo de código

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
      case 'saved': return 'Edit'
      case 'edited': return 'Save'
      case 'editing': return 'Cancel'
    }
  }
}
```

### Modos de transição

Ainda há um problema. Experimente clicar no botão abaixo:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="Rwrqzpr" data-preview="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Transition Modes Button Problem">
  <span>Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/Rwrqzpr">
  Problema do botão dos modos de transição Problema </a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

À medida que faz a transição entre o botão "ligar" e o botão "desligar", os dois botões são renderizados - um faz a transição para fora e o outro para dentro. Este é o comportamento padrão do `<transition>` - entrar e sair acontecem simultaneamente.

Às vezes, isso funciona muito bem, como quando os itens de transição são absolutamente posicionados um sobre o outro:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="abdQgLr" data-preview="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Transition Modes Button Problem- positioning">
  <span>Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/abdQgLr">
  Problema do botão dos modos de transição - posicionamento </a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Às vezes, isso não é uma opção, ou estamos lidando com um movimento mais complexo onde os estados de entrada e saída precisam ser coordenados, então o Vue oferece um utilitário extremamente útil chamado **modos de transição**:

- `in-out`: As novas transições de elemento entram primeiro e, quando concluído, o elemento atual sai.
- `out-in`: O elemento atual sai primeiro e, quando concluído, o novo elemento entra.

::: dica
Você descobrirá muito rapidamente que `out-in` é o estado que você desejará na maior parte do tempo:)
:::

Agora vamos atualizar a transição para nossos botões liga/desliga com `out-in`:

```html
<transition name="fade" mode="out-in">
  <!-- ... the buttons ... -->
</transition>
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="ZEQmdvq" data-preview="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Transition Modes Button Problem- solved">
  <span>Veja um exemplo de <a href="https://codepen.io/team/Vue/pen/ZEQmdvq">
  Problema do botão dos modos de transição - resolvido</a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Com a adição de um atributo, corrigimos a transição original sem precisar adicionar nenhum estilo especial.

Podemos usar isso para coordenar movimentos mais expressivos, como um cartão dobrável, conforme demonstrado a seguir. Na verdade, são dois elementos em transição entre si, mas como os estados inicial e final estão na mesma escala: horizontalmente para 0, parece um movimento fluido. Este tipo de toque leve pode ser muito útil para microinterações de IU realistas:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="76e344bf057bd58b5936bba260b787a8" data-preview="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Transition Modes Flip Cards">
  <span>SVeja um exemplo de  <a href="https://codepen.io/team/Vue/pen/76e344bf057bd58b5936bba260b787a8">
  Modos de transição Flip Cards</a> do Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Transição entre componentes

A transição entre componentes é ainda mais simples - nem precisamos do atributo `key`. Em vez disso, envolvemos um [componente dinâmico](component-basics.html#dynamic-components):

TODO: atualização para Vue 3

```html
<transition name="component-fade" mode="out-in">
  <component :is="view"></component>
</transition>
```

```js
new Vue({
  el: '#transition-components-demo',
  data: {
    view: 'v-a'
  },
  components: {
    'v-a': {
      template: '<div>Component A</div>'
    },
    'v-b': {
      template: '<div>Component B</div>'
    }
  }
})
```

```css
.component-fade-enter-active,
.component-fade-leave-active {
  transition: opacity 0.3s ease;
}
.component-fade-enter, .component-fade-leave-to
/* .componente-fade-leave-active abaixo da versão 2.1.8 */ {
  opacity: 0;
}
```
