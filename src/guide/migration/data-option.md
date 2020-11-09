---
title: Data Option
badges:
  - breaking
---

# {{ $frontmatter.title }} <MigrationBadges :badges="$frontmatter.badges" />

## Overview

- **BREAKING**: A declaração do componente `data` não aceita mais um `objeto` JavaScript e espera uma declaração de `função`.

- **BREAKING**: Quando mesclando multiplos valores de retorno `data` de mixins ou extends, a mescla agora é superficial e não profunda (apenas propriedades de nivel raiz são mescladas).

## Sintaxe da Versão 2.x

Na versão 2.x, os desenvolvedores poderiam definir a opção `data` como um `objeto` ou uma `função`.

Por exemplo:

```html
<!-- Declaração como Objeto -->
<script>
  const app = new Vue({
    data: {
      apiKey: 'a1b2c3'
    }
  })
</script>

<!-- Declaração como Função -->
<script>
  const app = new Vue({
    data() {
      return {
        apiKey: 'a1b2c3'
      }
    }
  })
</script>
```

Apesar de fornecer algumas conveniências em termos de instâncias raiz possuirem um estado compartilhado, isso gerou confusão devido ao fato de que só é possível na instância raiz.

## Atualização 3.x

Na versão 3.x, a opção `data` foi padronizada para somente receber uma `função` que retorna um `objeto`.

Usando o exemplo acima, só haveria uma forma possivel de implementar o código:

```html
<script>
  import { createApp } from 'vue'

  createApp({
    data() {
      return {
        apiKey: 'a1b2c3'
      }
    }
  }).mount('#app')
</script>
```

## Mudança de Comportamento na Mescla de Mixin (Mixin Merge Behavior Change)

Além disso, quando a `data()` de um componente e seus mixins ou extends de base são mesclados, a mescla agora é feita *superficialmente*:

```js
const Mixin = {
  data() {
    return {
      user: {
        name: 'Jack',
        id: 1
      }
    }
  }
}

const CompA = {
  mixins: [Mixin],
  data() {
    return {
      user: {
        id: 2
      }
    }
  }
}
```

No Vue 2.x, a `$data` resultante seria:

```json
{
  user: {
    id: 2,
    name: 'Jack'
  }
}
```

Na versão 3.0, o resultado será:

```json
{
  user: {
    id: 2
  }
}
```

## Estratégias de Migração

Para usuários que dependem da declaração em objeto, é recomendado:

- Extrair a informação compartilhada em um objeto externo e usá-lo como propriedade em `data`
- Reescrever as referencias a informação compartilhada para apontar a um novo objeto compartilhado

Para usuários que dependem do comportamento de deep merge dos mixins, é recomendado refatorar seu código para evitar tal dependência completamente, já que deep merges de mixins são muito implicitos e podem fazer a lógica do código ser mais difícil de entender e depurar.
