---
badges:
  - breaking
---

# Observar Arrays

## Visão Geral

- **QUEBRA**: Ao observar uma Array, o retorno de chamada só será acionado quando a Array for substituída. Se você precisar acionar na mutação, a opção `deep` deve ser especificada.

## 3.x Sintaxe

Ao usar [a opção `watch`] (/api/options-data.html#watch) para observar uma Array, o retorno de chamada só será disparado quando a Array for substituída. Em outras palavras, o observador do retorno da chamada não será mais acionado na mutação da Array. Para acionar a mutação, a opção `deep` deve ser especificada.

```js
watch: {
  bookList: {
    handler(val, oldVal) {
      console.log('book list alterado')
    },
    deep: true
  },
}
```

## Estratégia de Migração

Se você depende da observação de mutações de Array, adicione a propriedade `deep` para garantir que seu retorno de chamada seja disparado corretamente.