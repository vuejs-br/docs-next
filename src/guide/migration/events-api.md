---
badges:
  - breaking
---

# API de Eventos <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

Os métodos `$on`, `$off` e `$once` foram retirados da instância. A instância da aplicação não implementa mais a interface de emissor de eventos.

## Sintaxe v2.x

Na v2.x, a instância Vue pode ser usada para acionar manipuladores anexados imperativamente por meio da API do emissor de eventos (`$on`, `$off` e `$once`). Isso foi usado para criar _hubs de eventos_ para permitir a escuta de eventos globais usados em todo o aplicativo:

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
    // adicionando escutador eventHub
    eventHub.$on('custom-event', () => {
      console.log('Evento customizado disparado!')
    })
  },
  beforeDestroy() {
    // removendo o escutador eventHub
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
      eventHub.$emit('custom-event') // Quando o componente ChildComponent for montado, nós teremos uma mensagem no console
    }
  }
}
```

## Atualização v3.x

Removemos os métodos `$on`, `$off` e `$once` da instância completamente. `$emit` ainda é uma parte da API existente, pois é usado para acionar manipuladores de eventos declarativamente anexados por um componente pai.

## Estratégia de Migração

Os _hubs de eventos_ existentes podem ser substituídos usando uma biblioteca externa implementando a interface do emissor de eventos, por exemplo [mitt](https://github.com/developit/mitt) ou [tiny-emitter](https://github.com/scottcorgan/tiny-emitter).

Esses métodos também podem ser suportados em compilações de compatibilidade.