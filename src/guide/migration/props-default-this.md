---
title: Acesso ao this na função padrão props
badges:
  - breaking
---

# Acesso ao `this` na função padrão Props <MigrationBadges :badges="$frontmatter.badges" />

A função padrão props não terá mais acesso a variavel `this`.

Ao invez disso:

- Propriedades brutas são recebidads pelo componente e passadas para as funções como argumento;

- A API de [injeção](../composition-api-provide-inject.md) pode ser utilizada dentro da função padrão.

```js
import { inject } from 'vue'

export default {
  props: {
    theme: {
      default (props) {
        // `props` é o novo valor bruto passado para o componente.
        // antes de algum tipo / coerção padrao
        // tambem pode ser utilizado `inject` para acessar as propriedades
        return inject('theme', 'default-theme')
      }
    }
  }
}
```
