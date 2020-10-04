---
badges:
- breaking
---

# v-for Array Refs

No Vue 2, usar o atributo `ref` dentro de `v-for` irá preencher a propriedade `$refs` correspondente com uma Array de `refs`. Este comportamento se torna ambíguo e ineficiente quando estão aninhados de `v-for` presentes.

No Vue 3, tal uso não criará mais automaticamente uma Array em `$refs`. Para recuperar vários `refs` de uma única ligação, vincule `ref` a uma função que forneça mais flexibilidade (este é um novo recurso):

```html
<div v-for="item in list" :ref="setItemRef"></div>
```

Com opções API:

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

Com composição API:

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

- `itemRefs` não precisa ser uma Array: também pode ser um objeto onde os `refs` são definidos por suas chaves de iteração.

- Isso também permite que `itemRefs` sejam reativos e observados, se necessário.
