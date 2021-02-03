---
badges:
  - removed
---

# Filtros <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

Filtros foram removidos do Vue 3.0 e não são mais suportados.

## Sintaxe v2.x

Na v2.x, desenvolvedores poderiam utilizar `filters` para aplicar formatações comuns em texto.

Por exemplo:

```html
<template>
  <h1>Saldo da Conta Bancária</h1>
  <p>{{ accountBalance | currencyUSD }}</p>
</template>

<script>
  export default {
    props: {
      accountBalance: {
        type: Number,
        required: true
      }
    },
    filters: {
      currencyUSD(value) {
        return '$' + value
      }
    }
  }
</script>
```

Embora isso pareça ser uma conveniência, requer uma sintaxe personalizada que quebra a suposição de que as expressões entre chaves sejam "apenas JavaScript", o que tem custos de aprendizado e implementação.

## Atualização v3.x

Na v3.x, `filters` foram removidos e não mais suportados. Ao invés disso, recomendamos utilizar chamadas à metodos ou propriedades computadas.

Utilizando o exemplo acima, aqui está um exemplo de como isso pode ser implementado.

```html
<template>
  <h1>Saldo da Conta Bancária</h1>
  <p>{{ accountInUSD }}</p>
</template>

<script>
  export default {
    props: {
      accountBalance: {
        type: Number,
        required: true
      }
    },
    computed: {
      accountInUSD() {
        return '$' + this.accountBalance
      }
    }
  }
</script>
```

## Estratégia de Migração

Ao invés de utilizar filtros, recomendamos substituí-los por propriedades computadas ou métodos.

### Filtros Globais

Se você estiver usando filtros que foram registrados globalmente e usados em todo o seu aplicativo, provavelmente não é conveniente substituí-los por propriedades computadas ou métodos em cada componente individual.

Em vez disso, você pode disponibilizar seus filtros globais para todos os componentes por meio de [propriedades globais](../../api/application-config.html#globalproperties):

```javascript
// main.js
const app = createApp(App)

app.config.globalProperties.$filters = {
  currencyUSD(value) {
    return '$' + value
  }
}
```

Então você pode corrigir todos os _templates_ usando este objeto `$filters` assim:

```html
<template>
  <h1>Saldo da Conta Bancária</h1>
  <p>{{ $filters.currencyUSD(accountBalance) }}</p>
</template>
```

Observe que, com essa abordagem, você só pode usar métodos, não propriedades computadas, quais só fazem sentido quando definidas no contexto de um componente individual.