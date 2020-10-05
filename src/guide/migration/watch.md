---
title: Observar Arrays
badges:
  - breaking
---

# {{ $frontmatter.title }} <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

- **QUEBRA**: Ao observar um Array, o _callback_ só será acionado quando o Array for completamente substituído. Se você precisar acionar em mutação, a opção `deep` deve ser especificada.

## Sintaxe v3.x

Ao usar [a opção `watch`](/api/options-data.html#watch) para observar um Array, o _callback_ só será disparado quando o Array for substituído. Em outras palavras, o _callback_ do observador não será mais acionado na mutação do Array. Para acionar em mutação, a opção `deep` deve ser especificada.

```js
watch: {
  bookList: {
    handler(val, oldVal) {
      console.log('lista de livros alterada')
    },
    deep: true
  },
}
```

## Estratégia de Migração

Se você depende da observação de mutações de Arrays, adicione a propriedade `deep` para garantir que seu retorno de chamada (_callback_) seja disparado corretamente.