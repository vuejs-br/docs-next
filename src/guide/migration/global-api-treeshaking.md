---
badges:
  - breaking
---

# TreeShaking da API Global <MigrationBadges :badges="$frontmatter.badges" />

## Sintaxe v2.x

Se você já teve que manipular manualmente o DOM no Vue, pode ter encontrado este padrão:

```js
import Vue from 'vue'

Vue.nextTick(() => {
  // algo relacionado ao DOM
})
```

Ou, se você fez testes unitários em seu aplicativo envolvendo [componentes assíncronos](/guide/component-dynamic-async.html), provavelmente você escreveu algo assim:

```js
import { shallowMount } from '@vue/test-utils'
import { MyComponent } from './MyComponent.vue'

test('um recurso assíncrono', async () => {
  const wrapper = shallowMount(MyComponent)

  // execute alguma tarefa relacionada ao DOM

  await wrapper.vm.$nextTick()

  // execute suas asserções
})
```

`Vue.nextTick()` é uma API global exposta diretamente em um único objeto Vue - na verdade, o método de instância `$nextTick()` é apenas um *wrapper* em torno de `Vue.nextTick()` com o contexto `this` do retorno de chamada automaticamente vinculado à instância atual por conveniência.

Mas e se você nunca teve que lidar com manipulação manual do DOM, nem está usando ou testando componentes assíncronos em nosso aplicativo? Ou, e se, por qualquer motivo, você preferir usar o bom e velho `window.setTimeout()` em vez disso? Nesse caso, o código para `nextTick()` se tornará um código morto - ou seja, o código que foi escrito, mas nunca usado. E código morto dificilmente é uma coisa boa, especialmente em nosso contexto do lado do cliente, onde cada kilobyte é importante.

Os empacotadores de módulo, como o [webpack](https://webpack.js.org/), oferecem suporte à [tree-shaking](https://webpack.js.org/guides/tree-shaking/), que é um termo sofisticado para “eliminação de código morto”. Infelizmente, devido à forma como o código é escrito nas versões anteriores do Vue, APIs globais como `Vue.nextTick()` não podem ser eliminadas com *tree-shaking* e serão incluídas no pacote final, independentemente de onde sejam realmente usadas ou não.

## Sintaxe v3.x

No Vue 3, as APIs globais e internas foram reestruturadas tendo em mente o suporte à *tree-shaking*. Como resultado, as APIs globais agora podem ser acessadas apenas como exportações nomeadas para a construção de Módulos ES. Por exemplo, nossos blocos de códigos anteriores agora devem ser semelhantes a este:

```js
import { nextTick } from 'vue'

nextTick(() => {
  // algo relacionado ao DOM
})
```

e

```js
import { shallowMount } from '@vue/test-utils'
import { MyComponent } from './MyComponent.vue'
import { nextTick } from 'vue'

test('um recurso assíncrono', async () => {
  const wrapper = shallowMount(MyComponent)

  // execute alguma tarefa relacionada ao DOM

  await nextTick()

  // execute suas asserções
})
```

Chamar `Vue.nextTick()` diretamente agora resultará no abominável erro `undefined is not a function`.

Com essa mudança, dado que o empacotador de módulos suporte *tree-shaking*, APIs globais que não são usadas em seu aplicativo Vue serão eliminadas do pacote final, resultando em um ótimo tamanho de arquivo.

## APIs Afetadas

Essas APIs globais no Vue 2.x são afetadas por esta mudança:

- `Vue.nextTick`
- `Vue.observable` (substituído por `Vue.reactive`)
- `Vue.version`
- `Vue.compile` (apenas em compilações completas)
- `Vue.set` (apenas em compilações de compatibilidade)
- `Vue.delete` (apenas em compilações de compatibilidade)

## Ajudantes Internos

Além das APIs públicas, muitos dos componentes/ajudantes internos agora também são exportados como exportações nomeadas. Isso permite que o compilador produza um código que importa apenas recursos quando eles são usados. Por exemplo, o seguinte template:

```html
<transition>
  <div v-show="ok">olá</div>
</transition>
```

é compilado em algo semelhante ao seguinte:

```js
import { h, Transition, withDirectives, vShow } from 'vue'

export function render() {
  return h(Transition, [withDirectives(h('div', 'olá'), [[vShow, this.ok]])])
}
```

Isso significa essencialmente que o componente `Transition` só é importado quando o aplicativo realmente faz uso dele. Em outras palavras, se o aplicativo não tiver nenhum componente `<transition>`, o código que suporta esse recurso não estará presente no pacote final.

Com o *tree-shaking* global, o usuário “paga” apenas pelos recursos que realmente usa. Melhor ainda, sabendo que os recursos opcionais não aumentarão o tamanho do pacote para aplicativos que não os utilizam, o tamanho do framework se tornou muito menos uma preocupação para recursos centrais adicionais no futuro, se é que o fará.

::: warning Importante
O que foi dito acima se aplica apenas as [Construções de Módulos ES](/guide/installation.html#explanation-of-different-builds) para uso com empacotadores capazes de aplicar *tree-shaking* - o construtor UMD ainda inclui todos os recursos e expõe tudo na variável global Vue (e o compilador produzirá a saída apropriada para usar APIs fora do global em vez de importar).
:::

## Uso em Plugins

Se o seu plug-in depende de uma API global do Vue 2.x afetada, por exemplo:

```js
const plugin = {
  install: Vue => {
    Vue.nextTick(() => {
      // ...
    })
  }
}
```

No Vue 3, você terá que importá-lo explicitamente:

```js
import { nextTick } from 'vue'

const plugin = {
  install: app => {
    nextTick(() => {
      // ...
    })
  }
}
```

Se você usar um empacotador de módulo como webpack, isso pode fazer com que o código-fonte do Vue seja agrupado no plug-in e, na maioria das vezes, não é o que você esperava. Uma prática comum para evitar que isso aconteça é configurar o empacotador de módulo para excluir Vue do pacote final. No caso do webpack, você pode usar a opção de configuração [`externals`](https://webpack.js.org/configuration/externals/):

```js
// webpack.config.js
module.exports = {
  /*...*/
  externals: {
    vue: 'Vue'
  }
}
```

Isso dirá ao webpack para tratar o módulo Vue como uma biblioteca externa e não empacotá-lo.

Se o empacotador de módulo de sua escolha for [Rollup](https://rollupjs.org/), você basicamente obterá o mesmo efeito de graça, pois por padrão o Rollup tratará IDs de módulo absolutos (`'vue'` em nosso caso) como dependências externas e não incluí-las no pacote final. No entanto, durante o empacotamento, ele pode emitir um aviso [“Tratando vue como dependência externa”](https://rollupjs.org/guide/en/#warning-treating-module-as-external-dependency), que pode ser suprimido com a opção `external`:

```js
// rollup.config.js
export default {
  /*...*/
  external: ['vue']
}
```
