# Componentes Dinâmicos e Assíncronos

> Essa página pressupõe que você já leu [Componentes Básicos](component-basics.md). Leia isso primeiro se você for novo em componentes.

## Componentes dinâmicos com `keep-alive`

Anteriormente, usamos o atributo `is` para alternar entre os componentes em uma _interface_ com guias:

```vue-html
<component :is="currentTabComponent"></component>
```

Todavia, ao alternar entre esses componentes, às vezes você desejará manter seu estado ou evitar uma nova renderização por motivos de desempenho. Por exemplo, vamos expandir um pouco nossa _interface_ com guias:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="Vue" data-slug-hash="jOPjZOe" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Dynamic components: without keep-alive">
  <span>Veja o Pen <a href="https://codepen.io/team/Vue/pen/jOPjZOe">
  Componentes Dinâmicos: sem keep-alive</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Você notará que, se selecionar uma postagem, alternar para a guia _Arquivo_ e, em seguida, voltar para _Postagens_, ela não estará mais mostrando a postagem que você selecionou. Isso porque, a cada vez que você muda para uma nova guia, o Vue cria uma instância do `currentTabComponent`.

Recriar componentes dinâmicos normalmente é um comportamento útil, mas neste caso, realmente gostaríamos que essas instâncias do componente de guia fossem armazenadas em _cache_ assim que fosserm criadas pela primeira vez. Para resolver esse problema, podemos envolver nosso componente dinâmico com um elemento `<keep-alive>`:

```vue-html
<!-- Os componentes inativos serão armazenados em cache! -->
<keep-alive>
  <component :is="currentTabComponent"></component>
</keep-alive>
```

Confira o resultado abaixo:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="html,result" data-user="Vue" data-slug-hash="VwLJQvP" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Dynamic components: with keep-alive">
  <span>Veja o Pen <a href="https://codepen.io/team/Vue/pen/VwLJQvP">
  Componentes Dinâmicos: com keep-alive</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  no <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Agora a guia _Postagens_ mantém seu estado (a postagem selecionada) mesmo quando não é renderizada.

Confira mais detalhes sobre `<keep-alive>` na [Referência da API](../api/built-in-components.html#keep-alive).

## Componentes Assíncronos

Em grandes aplicações, podemos precisar dividir o aplicativo em pedaços menores e carregar um componente do servidor apenas quando for necessário. Para tornar isso possível, o Vue tem o método `defineAsyncComponent`:

```js
const app = Vue.createApp({})

const AsyncComp = Vue.defineAsyncComponent(
  () =>
    new Promise((resolve, reject) => {
      resolve({
        template: '<div>Eu sou assíncrono!</div>',
      })
    })
)

app.component('async-example', AsyncComp)
```

Como você pode ver, esse método aceita uma função de fábrica(_factory function_) que retorna uma `Promise`. O _callback_ `resolve` da Promise deve ser chamado quando você recupera a definição do componente do servidor. Você também pode chamar o `reject(motivo)` para indicar que o carregamento falhou.

Você também pode retornar uma `Promise` na _factory function_, com o Webpack 2 ou posterior e a sintaxe ES2015, você pode fazer:

```js
import { defineAsyncComponent } from 'vue'

const AsyncComp = defineAsyncComponent(() =>
  import('./components/AsyncComponent.vue')
)

app.component('async-component', AsyncComp)
```

Você também pode usar o `defineAsyncComponent` ao [registrar um componente localmente](component-registration.html#local-registration):

```js
import { createApp, defineAsyncComponent } from 'vue'

createApp({
  // ...
  components: {
    AsyncComponent: defineAsyncComponent(() =>
      import('./components/AsyncComponent.vue')
    ),
  },
})
```

### Usando com Suspense

Os componentes assíncronos são _suspensos_ por padrão. Isso significa que, se houver um [`<Suspense>`](TODO) na cadeia (pai), ele será tratado como uma dependência assíncrona desse `<Suspense>`. Nesse caso, o estado de carregamento vai se controlado pelo `<Suspense>`, e as opções de carregamento, erro, atraso e tempo limite do próprio componente serão ignoradas.

O componente assíncrono pode cancelar o controle do `Suspense` e deixar que o componente sempre controle seu próprio estado de carregamento, especificando `suspensible: false` em suas opções.

Você pode conferir a lista de opções disponíveis na [Referência da API](../api/global-api.html#arguments-4)
