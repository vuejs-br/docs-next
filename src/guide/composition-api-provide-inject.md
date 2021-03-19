# Prover e Injetar Dados

> Este guia assume que você já leu as seções [Prover e Injetar Dados](component-provide-inject.html), [Introdução a API de Composição](composition-api-introduction.html) e [Fundamentos de Reatividade](reactivity-fundamentals.html).

Podemos [prover e injetar dados](component-provide-inject.html) com a API de Composição também. Ambos só podem ser chamados durante [`setup()`](composition-api-setup.html) com uma instância ativa atual.

## Exemplo de Cenário

Vamos supor que queremos reescrever o código a seguir, que contém um componente `MyMap` que provê um componente `MyMarker` com a localização do usuário, usando a API de Composição.

```vue
<!-- src/components/MyMap.vue -->
<template>
  <MyMarker />
</template>

<script>
import MyMarker from './MyMarker.vue'

export default {
  components: {
    MyMarker
  },
  provide: {
    location: 'Polo Norte',
    geolocation: {
      longitude: 90,
      latitude: 135
    }
  }
}
</script>
```

```vue
<!-- src/components/MyMarker.vue -->
<script>
export default {
  inject: ['location', 'geolocation']
}
</script>
```

## Usando Provide

Ao usar `provide` no `setup()`, começamos importando explicitamente o método de `vue`. Isso nos permite definir cada propriedade com sua própria invocação de `provide`.

A função `provide` permite definir a propriedade por meio de dois parâmetros:

1. O nome da propriedade (do tipo `<String>`); e
2. O valor da propriedade.

Usando nosso componente `MyMap`, nossos valores providos podem ser refatorados da seguinte forma:

```vue{7,14-20}
<!-- src/components/MyMap.vue -->
<template>
  <MyMarker />
</template>

<script>
import { provide } from 'vue'
import MyMarker from './MyMarker.vue

export default {
  components: {
    MyMarker
  },
  setup() {
    provide('location', 'Polo Norte')
    provide('geolocation', {
      longitude: 90,
      latitude: 135
    })
  }
}
</script>
```

## Usando Inject

Ao usar `inject` no `setup()`, também precisamos importá-lo explicitamente de `vue`. Assim que fizermos isso, isso nos permitirá invocá-lo para definir como queremos expô-lo ao nosso componente.

A função `inject` leva dois parâmetros:

1. O nome da propriedade a injetar; e
2. Um valor padrão (**Opcional**).

Usando nosso componente `MyMarker`, podemos refatorá-lo com o seguinte código:

```vue{3,6-14}
<!-- src/components/MyMarker.vue -->
<script>
import { inject } from 'vue'

export default {
  setup() {
    const userLocation = inject('location', 'O Universo')
    const userGeolocation = inject('geolocation')

    return {
      userLocation,
      userGeolocation
    }
  }
}
</script>
```

## Reatividade

### Adicionando Reatividade

Para adicionar reatividade entre os valores providos e injetados, podemos usar uma [ref](reactivity-fundamentals.html#criacao-de-valores-reativos-avulsos-como-refs) ou [reactive](reactivity-fundamentals.html#declarando-estado-reativo) ao prover um valor.

Usando nosso componente `MyMap`, nosso código pode ser atualizado da seguinte forma:

```vue{7,15-22}
<!-- src/components/MyMap.vue -->
<template>
  <MyMarker />
</template>

<script>
import { provide, reactive, ref } from 'vue'
import MyMarker from './MyMarker.vue

export default {
  components: {
    MyMarker
  },
  setup() {
    const location = ref('Polo Norte')
    const geolocation = reactive({
      longitude: 90,
      latitude: 135
    })

    provide('location', location)
    provide('geolocation', geolocation)
  }
}
</script>
```

Agora, se alguma coisa mudar em qualquer uma das propriedades, o componente `MyMarker` também será atualizado automaticamente!

### Mutando Propriedades Reativas

Ao usar dados providos e injetados reativos, **é recomendado manter quaisquer mutações nas propriedades reativas dentro do _provider_ sempre que possível**.

Por exemplo, no caso de precisarmos alterar a localização do usuário, o ideal seria fazer isso dentro de nosso componente `MyMap`.

```vue{28-32}
<!-- src/components/MyMap.vue -->
<template>
  <MyMarker />
</template>

<script>
import { provide, reactive, ref } from 'vue'
import MyMarker from './MyMarker.vue

export default {
  components: {
    MyMarker
  },
  setup() {
    const location = ref('Polo Norte')
    const geolocation = reactive({
      longitude: 90,
      latitude: 135
    })

    provide('location', location)
    provide('geolocation', geolocation)

    return {
      location
    }
  },
  methods: {
    updateLocation() {
      this.location = 'Polo Sul'
    }
  }
}
</script>
```

No entanto, há momentos em que precisamos atualizar os dados dentro do componente onde os dados são injetados. Nesse cenário, recomendamos prover um método que seja responsável por mutar a propriedade reativa.

```vue{21-23,27}
<!-- src/components/MyMap.vue -->
<template>
  <MyMarker />
</template>

<script>
import { provide, reactive, ref } from 'vue'
import MyMarker from './MyMarker.vue

export default {
  components: {
    MyMarker
  },
  setup() {
    const location = ref('Polo Norte')
    const geolocation = reactive({
      longitude: 90,
      latitude: 135
    })

    const updateLocation = () => {
      location.value = 'Polo Sul'
    }

    provide('location', location)
    provide('geolocation', geolocation)
    provide('updateLocation', updateLocation)
  }
}
</script>
```

```vue{9,14}
<!-- src/components/MyMarker.vue -->
<script>
import { inject } from 'vue'

export default {
  setup() {
    const userLocation = inject('location', 'O Universo')
    const userGeolocation = inject('geolocation')
    const updateUserLocation = inject('updateLocation')

    return {
      userLocation,
      userGeolocation,
      updateUserLocation
    }
  }
}
</script>
```

Por fim, recomendamos o uso de `readonly` na propriedade provida, se você quiser garantir que os dados passados por `provide` não possam sofrer mutação pelo componente injetado.

```vue{7,25-26}
<!-- src/components/MyMap.vue -->
<template>
  <MyMarker />
</template>

<script>
import { provide, reactive, readonly, ref } from 'vue'
import MyMarker from './MyMarker.vue

export default {
  components: {
    MyMarker
  },
  setup() {
    const location = ref('Polo Norte')
    const geolocation = reactive({
      longitude: 90,
      latitude: 135
    })

    const updateLocation = () => {
      location.value = 'Polo Sul'
    }

    provide('location', readonly(location))
    provide('geolocation', readonly(geolocation))
    provide('updateLocation', updateLocation)
  }
}
</script>
```
