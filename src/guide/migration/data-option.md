---
title: Opção Data
badges:
  - breaking
---

# {{ $frontmatter.title }} <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

- **BREAKING**: A declaração da opção `data` do componente não aceita mais um `objeto` JavaScript e espera uma declaração de `função`.

- **BREAKING**: Quando mesclando múltiplos valores de retorno do `data` de _mixins_ ou _extends_, a mescla agora é superficial e não profunda (apenas propriedades de nível raiz são mescladas).

## Sintaxe v2.x

Na v2.x, os desenvolvedores poderiam definir a opção `data` como um `objeto` ou uma `função`.

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

## Atualização v3.x

Na v3.x, a opção `data` foi padronizada para somente receber uma `função` que retorna um `objeto`.

Usando o exemplo acima, haveria apenas uma implementação possível do código:

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

## Mudança de Comportamento na Mescla de _Mixins_

Além disso, quando a `data()` de um componente e seus _mixins_ ou _extends_ de base são mesclados, a mescla agora é feita *superficialmente*:

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

Na v3.0, o resultado será:

```json
{
  user: {
    id: 2
  }
}
```

## Estratégias de Migração

Para usuários que dependem da declaração em objeto, é recomendado:

- Extrair os dados compartilhados em um objeto externo e usá-los como propriedade em `data`
- Reescrever as referências à informação compartilhada apontando para um novo objeto compartilhado

Para usuários que dependem do comportamento de mesclagem profunda dos _mixins_, é recomendado refatorar seu código para evitar tal dependência completamente, já que mesclagens profundas de _mixins_ são muito implícitas e podem fazer a lógica do código ser mais difícil de entender e depurar.
