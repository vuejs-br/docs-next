---
title: Array Refs no v-for
badges:
- breaking
---

# {{ $frontmatter.title }} <MigrationBadges :badges="$frontmatter.badges" />

No Vue 2, usar o atributo `ref` dentro do `v-for` irá preencher a propriedade `$refs` correspondente com um Array de `refs`. Este comportamento se torna ambíguo e ineficiente quando existem `v-for` aninhados.

No Vue 3, tal uso não mais criará automaticamente um Array em `$refs`. Para recuperar várias referências de uma única ligação, vincule `ref` à uma função que forneça mais flexibilidade (este é um novo recurso):

```html
<div v-for="item in list" :ref="setItemRef"></div>
```

Com API de Opções:

```js
export default {
  data() {
    return {
      itemRefs: []
    }
  },
  methods: {
    setItemRef(el) {
      this.itemRefs.push(el)
    }
  },
  beforeUpdate() {
    this.itemRefs = []
  },
  updated() {
    console.log(this.itemRefs)
  }
}
```

Com API de Composição:

```js
import { ref, onBeforeUpdate, onUpdated } from 'vue'

export default {
  setup() {
    let itemRefs = []
    const setItemRef = el => {
      itemRefs.push(el)
    }
    onBeforeUpdate(() => {
      itemRefs = []
    })
    onUpdated(() => {
      console.log(itemRefs)
    })
    return {
      itemRefs,
      setItemRef
    }
  }
}
```

Observe que:

- `itemRefs` não precisa ser um Array: também pode ser um objeto onde as referências são definidas por suas chaves de iteração.

- Isso também permite que `itemRefs` sejam reativos e observados, se necessário.
