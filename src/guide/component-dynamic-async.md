# Dinâmicos & Assíncronos

> Essa página assume que você já leu o [Básico sobre Componentes](component-basics.md). Leia lá primeiro se você for novo em componentes.

## Componentes Dinâmicos com `keep-alive`

Anteriormente, usamos o atributo `is` para alternar entre os componentes em uma interface com guias:

```vue-html
<component :is="currentTabComponent"></component>
```

Ao alternar entre esses componentes, às vezes, você desejará manter seu estado ou evitar uma nova renderização, por motivos de desempenho. Por exemplo, ao expandir nossa interface com guias um pouco:

<common-codepen-snippet title="Componentes Dinâmicos: sem keep-alive" slug="jOPjZOe" />

Você notará que, se selecionar uma postagem, alternar para a guia _Arquivo_ e, em seguida, voltar para _Postagens_, ela não estará mais mostrando a postagem selecionada. Isso acontece pois, cada vez que você muda para uma nova guia, o Vue cria uma nova instância do componente `currentTabComponent`.

Recriar componentes dinâmicos normalmente é um comportamento útil, mas, nesse caso, gostaríamos que essas instâncias de componentes de guias fossem armazenadas em _cache_ assim que fossem criadas pela primeira vez. Para resolver este problema, podemos envolver nosso componente dinâmico com um elemento `<keep-alive>`:

```vue-html
<!-- Componentes inativos serão armazenados em cache -->
<keep-alive>
  <component :is="currentTabComponent"></component>
</keep-alive>
```

Confira o resultado abaixo:

<common-codepen-snippet title="Componentes Dinâmicos: com keep-alive" slug="VwLJQvP" />

Agora, a guia _Postagens_ mantém seu estado (a postagem selecionada) mesmo quando não é renderizada.

Confira mais detalhes sobre `<keep-alive>` em [Referências de API](../api/built-in-components.html#keep-alive).

## Componentes Assíncronos

Em aplicativos grandes, talvez seja necessário dividí-lo em partes menores e carregar apenas um componente do servidor quando necessário.. Para tornar isso possível, o Vue tem o método `defineAsyncComponent`:

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

Como você pode ver, esse método aceita uma função de fabricação (_factory function_) que retorna uma `Promise`. O retorno de chamada `resolve` da Promise deve ser chamado quando você recuperar sua definição de componente do servidor. Você também pode chamar `reject(reason)` para indicar que o carregamento falhou.

Você também pode retornar uma `Promise` na função de fabricação. Portanto, com a sintaxe do Webpack 2+ ou ES2015, você pode fazer:

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
