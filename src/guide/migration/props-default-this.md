---
title: Acesso ao this na Função Padrão de Props
badges:
  - breaking
---

# Acesso ao `this` na Função Padrão de Props <MigrationBadges :badges="$frontmatter.badges" />

As funções fabricadoras de valores padrão de `props` não têm mais acesso à `this`.

Ao invés disso:

- `props` brutas recebidas pelo componente são passadas para as funções padrão como argumento;

- A API de [injeção](../composition-api-provide-inject.md) pode ser utilizada dentro de funções padrão.

```js
import { inject } from 'vue'

export default {
  props: {
    theme: {
      default (props) {
        // `props` são os valores brutos passados para o componente,
        // antes de qualquer tipo / coerção padrão.
        // Também pode ser utilizado `inject` para acessar propriedades injetadas
        return inject('theme', 'default-theme')
      }
    }
  }
}
```
