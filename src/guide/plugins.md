# Plugins

_Plugins_ são códigos autocontidos que geralmente adicionam funcionalidade de nível global ao Vue. É um `object` que expõe um método `install()` ou uma `function`.

Não há escopo estritamente definido para um _plugin_, mas os cenários comuns em que os _plugins_ são úteis incluem:

1. Adicionar alguns métodos e propriedades globais (Ex.: [vue-custom-element](https://github.com/karol-f/vue-custom-element)).

2. Adicionar um ou mais recursos globais: diretivas/filtros/transições etc. (Ex.: [vue-touch](https://github.com/vuejs/vue-touch)).

3. Adicionar algumas opções de componente via _mixin_ global. (Ex.: [vue-router](https://github.com/vuejs/vue-router)).

4. Adicionar alguns métodos de instância globais, anexando-os a `config.globalProperties`.

5. Uma biblioteca com uma API própria, que ao mesmo tempo injeta alguma combinação dos anteriores. (Ex.: [vue-router](https://github.com/vuejs/vue-router)).

## Escrevendo um Plugin

Para entender melhor como criar seus próprios _plugins_ Vue.js, criaremos uma versão muito simplificada de um _plugin_ que exibe strings prontas para `i18n`.

Sempre que este plugin é adicionado a uma aplicação, o método `install` será chamado se for um objeto. Se for uma `function`, a própria função será chamada. Em ambos os casos, ele receberá dois parâmetros - o objeto `app` resultante do `createApp` do Vue, e as opções passadas pelo usuário.

Vamos começar configurando o objeto de _plugin_. Recomenda-se criá-lo em um arquivo separado e exportá-lo, conforme mostrado a seguir, para manter a lógica contida e separada.

```js
// plugins/i18n.js
export default {
  install: (app, options) => {
    // O código do plugin vai aqui
  }
}
```

Queremos fazer uma função para traduzir as chaves disponíveis para toda a aplicação, então vamos expô-la usando `app.config.globalProperties`.

Esta função receberá uma _string_ `key`, que usaremos para pesquisar a _string_ traduzida nas opções fornecidas pelo usuário.

```js
// plugins/i18n.js
export default {
  install: (app, options) => {
    app.config.globalProperties.$translate = key => {
      return key.split('.').reduce((o, i) => {
        if (o) return o[i]
      }, i18n)
    }
  }
}
```

Assumiremos que nossos usuários passarão um objeto contendo as chaves traduzidas no parâmetro `options` quando usarem o _plugin_. Nossa função `$translate` pegará uma _string_ como `greetings.hello`, olhará dentro da configuração fornecida pelo usuário e vai retornar o valor traduzido - neste caso, `Bonjour!`

Ex.:

```js
greetings: {
  hello: 'Bonjour!',
}
```

Os _plugins_ também nos permitem usar `inject` para fornecer uma função ou atributo aos usuários do _plugin_. Por exemplo, podemos permitir que a aplicação tenha acesso ao parâmetro `options` para poder usar o objeto de traduções.

```js
// plugins/i18n.js
export default {
  install: (app, options) => {
    app.config.globalProperties.$translate = key => {
      return key.split('.').reduce((o, i) => {
        if (o) return o[i]
      }, i18n)
    }

    app.provide('i18n', options)
  }
}
```

Os usuários do `plugin` agora serão capazes de fazer `inject['i18n']` em seus componentes e acessar o objeto.

Além disso, como temos acesso ao objeto `app`, todos os outros recursos como o uso de `mixin` e `directive` estão disponíveis para o _plugin_. Para aprender mais sobre `createApp` e a instância do aplicativo, verifique a [documentação da Referência da API](/api/application-api.html).

```js
// plugins/i18n.js
export default {
  install: (app, options) => {
    app.config.globalProperties.$translate = (key) => {
      return key.split('.')
        .reduce((o, i) => { if (o) return o[i] }, i18n)
    }

    app.provide('i18n', options)

    app.directive('my-directive', {
      mounted (el, binding, vnode, oldVnode) {
        // alguma lógica...
      }
      ...
    })

    app.mixin({
      created() {
        // alguma lógica...
      }
      ...
    })
  }
}
```

## Usando um Plugin

Depois que um aplicativo Vue foi inicializado com `createApp()`, você pode adicionar um _plugin_ ao seu aplicativo chamando o método `use()`.

Usaremos o `i18nPlugin` que criamos na seção [Escrevendo um Plugin](#escrevendo-um-plugin) para fins de demonstração.

O método `use()` recebe dois parâmetros. O primeiro é o `plugin` a ser instalado, neste caso `i18nPlugin`.

Ele também impede automaticamente que você use o mesmo _plugin_ mais de uma vez, portanto, chamá-lo várias vezes no mesmo _plugin_ instalará o _plugin_ apenas uma vez.

O segundo parâmetro é opcional e depende de cada _plugin_ específico. No caso do demo `i18nPlugin`, é um objeto com as _strings_ traduzidas.

:::info
Se você estiver usando _plugins_ de terceiros, como `Vuex` ou `Vue Router`, sempre verifique a documentação para saber o que aquele _plugin_ específico espera receber como um segundo parâmetro.
:::

```js
import { createApp } from 'vue'
import Root from './App.vue'
import i18nPlugin from './plugins/i18n'

const app = createApp(Root)
const i18nStrings = {
  greetings: {
    hi: 'Hallo!'
  }
}

app.use(i18nPlugin, i18nStrings)
app.mount('#app')
```

Verifique [awesome-vue](https://github.com/vuejs/awesome-vue#components--libraries) para uma enorme coleção de _plugins_ e bibliotecas disponibilizados pela comunidade.
