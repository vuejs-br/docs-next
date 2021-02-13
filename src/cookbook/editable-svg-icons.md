# Sistemas de Ícones SVG Editáveis

## Exemplo Base

Existem muitas formas de criar um sistema de ícone SVG, mas o método que tira vantagem das capacidades do Vue é criar ícones *inline* editáveis como componente. Algumas das vantagens desta forma de trabalhar são:

- São fáceis de se editar em tempo real
- São animáveis
- Pode se usar props, definindo padrões ou alterando-as caso necessário
- São incorporados, então não exigem requisições HTTP adicionais
- Eles podem ser acessados dinamicamente

Primeiro, vamos criar uma pasta para todos os ícones, e nomeá-los de forma padronizada para fácil recuperação:

- `components/icons/IconBox.vue`
- `components/icons/IconCalendar.vue`
- `components/icons/IconEnvelope.vue`

Eis um exemplo de repositório, onde pode ver toda a configuração: [https://github.com/sdras/vue-sample-svg-icons/](https://github.com/sdras/vue-sample-svg-icons/)

![Documentation site](https://s3-us-west-2.amazonaws.com/s.cdpn.io/28963/screendocs.jpg 'Docs demo')

Vamos criar um componente de ícone base (`IconBase.vue`) que usa um `slot`.

```html
<template>
  <svg
    xmlns="http://www.w3.org/2000/svg"
    :width="width"
    :height="height"
    viewBox="0 0 18 18"
    :aria-labelledby="iconName"
    role="presentation"
  >
    <title :id="iconName" lang="en">{{ iconName }} icon</title>
    <g :fill="iconColor">
      <slot />
    </g>
  </svg>
</template>
```

Você pode usar essa base de ícones como está – a única coisa que você pode precisar atualizar é a e `viewBox` dependendo da `viewBox` dos ícones que for utilizar. Na base, vamos usar propriedades para `width`, `height`, `iconColor`, e `iconColor`, para que possam ser atualizados dinamicamente. O nome será usado tanto para o conteúdo do `<title>` como para o `id` para acessilibidade.

Nosso script ficará assim, teremos alguns valores padrão para que nosso ícone seja renderizado de forma consistente, a menos que declaremos o contrário: 

```js
export default {
  props: {
    iconName: {
      type: String,
      default: 'box'
    },
    width: {
      type: [Number, String],
      default: 18
    },
    height: {
      type: [Number, String],
      default: 18
    },
    iconColor: {
      type: String,
      default: 'currentColor'
    }
  }
}
```

A propriedade `currentColor` , usada como padrão para o preenchimento, irá permitir ao ícone herdar a cor do texto que o rodeia. Também poderíamos passar uma cor diferente como `prop` se quisermos.

Podemos usá-lo assim, com o único conteúdo de `IconWrite.vue` contendo os `path` dentro do ícone:

```html
<icon-base icon-name="write"><icon-write /></icon-base>
```

Agora, se quisermos fazer vários tamanhos para o ícone, podemos fazer isso facilmente:

```html
<p>
  <!-- pode-se passar um `width` e `height` mais pequenos como props -->
  <icon-base width="12" height="12" icon-name="write"><icon-write /></icon-base>
  <!-- ou você pode usar o padrão, que é 18 -->
  <icon-base icon-name="write"><icon-write /></icon-base>
  <!-- ou torná-lo um pouco maior também :) -->
  <icon-base width="30" height="30" icon-name="write"><icon-write /></icon-base>
</p>
```

<img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/28963/Screen%20Shot%202018-01-01%20at%204.51.40%20PM.png" width="450" />

## Ícones Animáveis

Manter os ícones como componentes é muito útil quando você deseja animá-los, especialmente em uma interação. Os SVGs embutidos (*inline*) têm o maior suporte para interação de qualquer método. Eis um exemplo básico de um ícone animado no evento `@click`:

```html
<template>
  <svg
    @click="startScissors"
    xmlns="http://www.w3.org/2000/svg"
    viewBox="0 0 100 100"
    width="100"
    height="100"
    aria-labelledby="scissors"
    role="presentation"
  >
    <title id="scissors" lang="en">Scissors Animated Icon</title>
    <path id="bk" fill="#fff" d="M0 0h100v100H0z" />
    <g ref="leftscissor">
      <path d="M..." />
      ...
    </g>
    <g ref="rightscissor">
      <path d="M..." />
      ...
    </g>
  </svg>
</template>
```

```js
import { TweenMax, Sine } from 'gsap'

export default {
  methods: {
    startScissors() {
      this.scissorAnim(this.$refs.rightscissor, 30)
      this.scissorAnim(this.$refs.leftscissor, -30)
    },
    scissorAnim(el, rot) {
      TweenMax.to(el, 0.25, {
        rotation: rot,
        repeat: 3,
        yoyo: true,
        svgOrigin: '50 45',
        ease: Sine.easeInOut
      })
    }
  }
}
```

Estamos aplicando `refs` aos grupos de caminhos que precisamos mover e, como ambos os lados da tesoura devem mover-se em conjunto, criamos uma função que permite a reutilização em ambas as `refs`. O uso da biblioteca GreenSock ajuda a resolver questões de suporte às animações e problemas de `transform-origin` entre os navegadores.

<p data-height="300" data-theme-id="0" data-slug-hash="dJRpgY" data-default-tab="result" data-user="Vue" data-embed-version="2" data-pen-title="Editable SVG Icon System: Animated icon" class="codepen">See the Pen <a href="https://codepen.io/team/Vue/pen/dJRpgY/">Editable SVG Icon System: Animated icon</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>) on <a href="https://codepen.io">CodePen</a>.</p><script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

<p style="margin-top:-30px">Um resultado atingido facilmente! E fácil de se atualizar sempre que necessário.</p>

Pode ver mais exemplos de animações [neste repositório](https://github.com/sdras/vue-sample-svg-icons/)

## Notas Adicionais

Os *designers* podem mudar de ideias. Ou podem ocorrer mudanças nos requisitos do produto. Manter todo o seu sistema de ícones baseados em componentes garante a possibilidade de atualizá-los todos, e os ter automaticamente aplicados em todo o sistema. Mesmo com o uso de uma ferramenta do tipo icon loader, algumas situações irão requerer que você recrie ou edite todos os SVG, para fazer alterações globais. O presente método pode ainda poupar-lhe tempo e dor.

## Quando Evitar Este Padrão

Este tipo de sistema de ícones SVG é realmente útil quando você tem vários ícones que são usados de maneiras diferentes em todo o *site*. Entretanto, se houver grande repetição do mesmo ícone em uma página (ex.: uma grande tabela com um ícone *delete* em cada linha), poderá fazer mais sentido ter todos os ícones compilados em um arquivo de sprites, com a *tag* `<use>` para carregá-los.

## Padrões Alternativos

Outras ferramentas para ajudar no gerenciamento de ícones SVG:

- [svg-sprite-loader](https://github.com/kisenka/svg-sprite-loader)
- [svgo-loader](https://github.com/rpominov/svgo-loader)

Essas ferramentas agrupam SVGs em tempo de compilação, mas os tornam um pouco mais difíceis de editar em *runtime* (durante o tempo de execução), porque as tags `<use>` pode introduzir problemas de compatibilidade entre navegadores para quaisquer ações mais complexas. Eles também deixam você com duas propriedades `viewBox` aninhadas e, desta forma, dois sistemas de coordenadas. Isto torna a implementação um pouco mais complexa.
