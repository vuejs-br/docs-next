# Visão Geral

Vue oferece algumas abstrações que podem lhe ajudar a trabalhar com transições e animações, particularmente em resposta à mudança de algo. Algumas dessas abstrações incluem:

- Gatilhos para componentes entrando e saindo do DOM, em CSS e JS, usando o componente interno `<transition>`.
- Modos de Transição para que você possa orquestrar a ordenação durante a transição. 
- Gatilhos para quando múltiplos elementos estão atualizando em posição, com técnicas FLIP aplicadas em seu interior para aumentar a performance, usando o componente `<transition-group>`.
- Transição de diferentes estados em uma aplicação, com `watchers`.

Vamos cobrir todos esses e mais nas próximas seções deste guia. Contudo, além dessas ofertas úteis de API, é importante mencionar que as declarações de classe e estilo mencionadas antes podem também ser usadas para aplicar animações e transições, para casos de uso simplificados.

Na próxima seção, vamos passar por conceitos básicos de transições e animações web, assim como oferecer links para alguns destes recursos para exploração posterior. Se você já estiver familiarizado com animação web e como estes princípios podem funcionar com algumas das directivas do Vue, sinta-se livre para pular a próxima seção. Para quem estiver buscando aprender mais sobre os básicos de animação web, continue a leitura.

## Animações & Transições Baseadas em Classes

Ainda que o componente `<transition>` pode ser maravilhoso para componentes que entram e saem, você também pode ativar uma animação sem montar um componente, adicionando uma classe condicional.

```html
<div id="demo">
  Aperte este botão para fazer algo que você não deveria estar fazendo:<br />

  <div :class="{ shake: noActivated }">
    <button @click="noActivated = true">Clique-me</button>
    <span v-if="noActivated">Ah não!</span>
  </div>
</div>
```

```css
.shake {
  animation: shake 0.82s cubic-bezier(0.36, 0.07, 0.19, 0.97) both;
  transform: translate3d(0, 0, 0);
  backface-visibility: hidden;
  perspective: 1000px;
}

@keyframes shake {
  10%,
  90% {
    transform: translate3d(-1px, 0, 0);
  }

  20%,
  80% {
    transform: translate3d(2px, 0, 0);
  }

  30%,
  50%,
  70% {
    transform: translate3d(-4px, 0, 0);
  }

  40%,
  60% {
    transform: translate3d(4px, 0, 0);
  }
}
```

```js
const Demo = {
  data() {
    return {
      noActivated: false
    }
  }
}

Vue.createApp(Demo).mount('#demo')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="css,result" data-user="Vue" data-slug-hash="ff45b91caf7a98c8c9077ad8ab539260" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Criar a animação por meio de uma classe">
  <span>Veja o exemplo <a href="https://codepen.io/team/Vue/pen/ff45b91caf7a98c8c9077ad8ab539260">
  Criar a animação por meio de uma classe</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

# Transições com Ligações de Estilo

Alguns efeitos de transições podem ser aplicados pela interpolação de valores, por exemplo, ao ligar um estilo com um elemento enquanto ocorre uma interação. Veja o seguinte exemplo:

```html
<div id="demo">
  <div
    @mousemove="xCoordinate"
    :style="{ backgroundColor: `hsl(${x}, 80%, 50%)` }"
    class="movearea"
  >
    <h3>Mexa o seu mouse pela tela...</h3>
    <p>x: {{x}}</p>
  </div>
</div>
```

```css
.movearea {
  transition: 0.2s background-color ease;
}
```

```js
const Demo = {
  data() {
    return {
      x: 0
    }
  },
  methods: {
    xCoordinate(e) {
      this.x = e.clientX
    }
  }
}

Vue.createApp(Demo).mount('#demo')
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="JjGezQY" data-preview="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Interpolação com ligações por estilo">
  <span>Veja o Pen <a href="https://codepen.io/team/Vue/pen/JjGezQY">
  Interpolação com ligações por estilo</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Neste exemplo, estamos criando uma animação por meio do uso de interpolação, adjunto ao movimento do mouse. A transição CSS é aplicada ao elemento também, permitindo que o elemento saiba que tipo de atenuação é usada ao se atualizar.

## Performance

Você pode perceber que as animações mostradas acima estão usando coisas como `transforms`, e aplicando propriedades estranhas como `perspective` - por que foram construídos assim em vez de simplesmente usar `margin` e `top`, etc?

Podemos criar animações extremamente suaves para a web estando cientes da performance. Queremos que o hardware acelere elementos quando pudermos, e que use as propriedades que não disparem a repintura dos mesmos. Vamos ver como podemos fazer isso.

### Transformações e Opacidade

Podemos verificar recursos como [Gatilhos CSS](https://csstriggers.com) para vermos quais propriedades vão desencadear repinturas se nós a animarmos. Aqui, caso você olhe a parte de `transform`, você verá:

> As mudanças do `transform` não desencadeiam mudanças geométricas ou de pintura, o que é muito bom. Isso significa que a operação provavelmente será realizada pela _thread_ de composição com o auxílio da GPU.

A opacidade se comporta de maneira similar. Assim, são candidatos ideais para movimentos na web.

### Aceleração de Hardware

Propriedades como `perspective`, `backface-visibility`, e `transform: translateZ(x)` vão permitir ao browser saber quando se necessita de aceleração de hardware.

Se você gostaria de acelerar o hardware de um elemento, você pode aplicar qualquer uma dessas propriedades (não necessariamente só uma):

```css
perspective: 1000px;
backface-visibility: hidden;
transform: translateZ(0);
```

Muitas bibliotecas JS como GreenSock vão assumir que você quer aceleração de hardware e as vão aplicar por padrão, assim você não precisa configurá-las manualmente.

## Tempo de Animação

Para transições de UI simples, de um estado para outro sem estados intermediários, é comum usar tempos entre 0.1s e 0.4s, e muitas pessoas acham que _0.25s_ tende a ser o ponto certo. Você pode usar este tempo para tudo? Não necessariamente. Se você tem algo que precisa se mover uma distância maior ou que tenha mais passos ou mudanças de estado, 0.25s não vai funcionar tão bem e você terá que ser muito mais intencional, e o tempo terá a necessidade de ser mais único. O que não significa que você não possa ter ótimos padrões que você repete dentro da sua aplicação.

Você também pode se dar conta de que entradas têm uma aparência melhor com um pouco mais de tempo que uma saída. O usuário tipicamente está sendo guiado durante uma entrada e é um pouco menos paciente na saída, por que querem seguir seu caminho.

## Atenuação

A atenuação é um modo importante de transmitir profundidade em uma animação. Um dos erros mais comuns que novatos em animação é usar `ease-in` para entradas, e `ease-out` para saídas. Quando, na verdade, você vai precisar do oposto.

Se tivéssemos que aplicar estes estados a uma transição, ela ficaria algo assim:

```css
.button {
  background: #1b8f5a;
  /* aplicada ao estado inicial, assim esta transição será aplicada ao estado de retorno */
  transition: background 0.25s ease-in;
}

.button:hover {
  background: #3eaf7c;
  /* aplicada ao estado de hover, assim esta transição será aplicada quando um hover é acionado. */
  transition: background 0.35s ease-out;
}
```

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="css,result" data-user="Vue" data-slug-hash="996a9665131e7902327d350ca8a655ac" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Exemplo de transição atenuada">
  <span>Veja o Pen <a href="https://codepen.io/team/Vue/pen/996a9665131e7902327d350ca8a655ac">
  Exemplo de transição atenuada</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

A atenuação pode inclusive transmitir a qualidade do material sendo animado. Olhe este pen, por exemplo, qual bola você acha que é rígida e qual é macia?

<p class="codepen" data-height="500" data-theme-id="39028" data-default-tab="result" data-user="sdras" data-slug-hash="zxJWBJ" data-preview="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Demo de bola quicando">
  <span>Veja o Pen <a href="https://codepen.io/sdras/pen/zxJWBJ">
 Demo de Bola Quicando</a> por Sarah Drasner (<a href="https://codepen.io/sdras">@sdras</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Você pode obter muitos efeitos únicos e fazer sua animação bem estilosa ajustando sua atenuação. O CSS lhe permite modificar isso ajustando uma propriedade bezier cúbica, [este playground](https://cubic-bezier.com/#.17,.67,.83,.67) por Lea Verou é muito útil para explorar isso.

Apesar de que você possa alcançar grandes efeitos para animações simples com as duas alças que a acentuação por bezier-cúbico oferece, Javascript permite múltiplas alças, e portanto, permite muito mais variação.

![Comparação de acentuação](/images/css-vs-js-ease.svg)

Tomemos um efeito de salto, por exemplo. Em CSS temos que declarar cada keyframe, para cima e para baixo. Em JavaScript, podemos expressar todo o movimento dentro da acentuação declarando `bounce` na [API GreenSock (GSAP)](https://greensock.com/) (outras bibliotecas JS têm outros tipos de padrões de acentuação).

Aqui está o código usado para o salto em CSS (exemplo de animate.css):

```css
@keyframes bounceInDown {
  from,
  60%,
  75%,
  90%,
  to {
    animation-timing-function: cubic-bezier(0.215, 0.61, 0.355, 1);
  }

  0% {
    opacity: 0;
    transform: translate3d(0, -3000px, 0) scaleY(3);
  }

  60% {
    opacity: 1;
    transform: translate3d(0, 25px, 0) scaleY(0.9);
  }

  75% {
    transform: translate3d(0, -10px, 0) scaleY(0.95);
  }

  90% {
    transform: translate3d(0, 5px, 0) scaleY(0.985);
  }

  to {
    transform: translate3d(0, 0, 0);
  }
}

.bounceInDown {
  animation-name: bounceInDown;
}
```

E aqui está o mesmo salto em JS usando GreenSock:

```js
gsap.from(element, { duration: 1, ease: 'bounce.out', y: -500 })
```

Vamos usar o GreenSock para alguns dos exemplos nas seções seguintes. Eles têm um ótimo [visualizador de atenuação](https://greensock.com/ease-visualizer) que vai lhe permitir construir atenuações bem trabalhadas.

## Leituras Adicionais

- [Designing Interface Animation: Improving the User Experience Through Animation by Val Head](https://www.amazon.com.br/dp/B01J4NKSZA)
- [Animation at Work by Rachel Nabors](https://abookapart.com/products/animation-at-work)
