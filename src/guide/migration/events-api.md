---
badges:
  - breaking
---

# API de eventos <MigrationBadges :badges="$frontmatter.badges" />

## Visão geral

Os métodos `$on`, `$off` e `$once` foram retirados da instancia. A instancia da aplicação não implementa mais a interface de emissor de eventos.

## 2.x Sintaxe

Na 2.x, a instância Vue pode ser usada para acionar manipuladores anexados imperativamente por meio da API do emissor de eventos (`$on`,` $off` e `$once`). Isso foi usado para criar _event hubs_ para permitir a comunicação de eventos globais usados em todo o aplicativo:

```js
// eventHub.js

const eventHub = new Vue()

export default eventHub
```

```js
// ChildComponent.vue
import eventHub from './eventHub'

export default {
  mounted() {
    // adding eventHub listener
    eventHub.$on('custom-event', () => {
      console.log('Evento customizado disparado!')
    })
  },
  beforeDestroy() {
    // removing eventHub listener
    eventHub.$off('custom-event')
  }
}
```

```js
// ParentComponent.vue
import eventHub from './eventHub'

export default {
  methods: {
    callGlobalCustomEvent() {
      eventHub.$emit('custom-event') // Quando o componente ChildComponent é montado, nós teremos uma mensagem no console
    }
  }
}
```

## 3.x Atualização

Removemos os métodos `$on`,`$off` e `$once` da instância completamente. `$emit` ainda é uma parte da API existente, pois é usado para acionar manipuladores de eventos declarativamente anexados por um componente pai.

## Estratégia de Migração

As comunicações por _event hubs_ existentes podem ser substituídos usando uma biblioteca externa implementando a interface do emissor de eventos, por exemplo [mitt](https://github.com/developit/mitt) ou [tiny-emitter](https://github.com/scottcorgan/tiny-emitter).

Esses métodos também podem ser suportados em compilações de compatibilidade.