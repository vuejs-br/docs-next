# Introdução

## Porque Composition API?

::: tip NOTA
Chegando até esta etapa da documentação, você já deve estar familiarizado com [os fundamentos do Vue](introduction.md) e o [básico sobre componentes](component-basics.md).
:::

<VideoLesson href="https://www.vuemastery.com/courses/vue-3-essentials/why-the-composition-api" title="Aprenda como a Composition API funciona em profundidade com o Vue Mastery">Assista a um vídeo gratuito sobre a Composition API no Vue Mastery</VideoLesson>

A criação de componentes Vue nos permite extrair partes repetíveis da interface, juntamente com sua funcionalidade, em partes reutilizáveis de código. Apenas isso já pode levar nossa aplicação bem longe em termos de manutenibilidade e flexibilidade. No entanto, nossa experiência coletiva provou que isso por si só pode não ser suficiente, especialmente quando a aplicação está ficando muito grande - algo como centenas de componentes. Ao lidar com aplicações tão grandes, compartilhar e reutilizar o código torna-se importantíssimo.

Vamos imaginar que em nossa aplicação temos uma visão para mostrar uma lista de repositórios de um determinado usuário. Além disso, queremos aplicar recursos de pesquisa e de filtros. O componente que manipula essa visualização pode ser assim:

```js
// src/components/UserRepositories.vue

export default {
  components: { RepositoriesFilters, RepositoriesSortBy, RepositoriesList },
  props: {
    user: { type: String }
  },
  data () {
    return {
      repositories: [], // 1
      filters: { ... }, // 3
      searchQuery: '' // 2
    }
  },
  computed: {
    filteredRepositories () { ... }, // 3
    repositoriesMatchingSearchQuery () { ... }, // 2
  },
  watch: {
    user: 'getUserRepositories' // 1
  },
  methods: {
    getUserRepositories () {
      // usando `this.user` para buscar os repositórios de usuário
    }, // 1
    updateFilters () { ... }, // 3
  },
  mounted () {
    this.getUserRepositories() // 1
  }
}
```

Este componente tem várias responsabilidades:

1. Obter repositórios para esse nome de usuário de uma API, supostamente externa, e atualizá-los sempre que o usuário mudar;
2. Procurar repositórios usando uma string `searchQuery`; e
3. Filtrar repositórios usando um objeto `filters`.

Organizar lógicas com as opções de componentes (`data`, `computed`, `methods`, `watch`) funciona na maioria dos casos. No entanto, quando nossos componentes ficam maiores, a lista de **preocupações lógicas** também aumenta. Isso pode levar a componentes que são difíceis de ler e entender, especialmente para pessoas que não os escreveram.

![Vue Option API: código agrupado por tipo de opção](https://user-images.githubusercontent.com/499550/62783021-7ce24400-ba89-11e9-9dd3-36f4f6b1fae2.png)

Exemplo apresentando um grande componente em que suas **preocupações lógicas** são agrupadas por cores.

Essa fragmentação é o que torna difícil entender e manter um componente complexo. A separação de opções obscurece as preocupações lógicas subjacentes. Além disso, ao trabalhar em uma única preocupação lógica, temos que "pular" constantemente os blocos de opções para o código relevante.

Seria muito mais agradável se pudéssemos colocar o código relacionado à mesma preocupação lógica. E é exatamente isso que a Composition API nos permite fazer.

## Noções Básicas da Composition API

Agora que já sabemos o **porquê**, podemos chegar ao **como**. Para começar a trabalhar com a Composition API, primeiro precisamos de um lugar onde possamos realmente usá-la. Em um componente Vue, chamamos esse local de `setup`.

### Opção de Componente `setup`

<VideoLesson href="https://www.vuemastery.com/courses/vue-3-essentials/setup-and-reactive-references" title="Aprenda como a opção setup funciona com o Vue Mastery"Assista a um vídeo grátis sobre a opção `setup` no Vue Mastery</VideoLesson>

A nova opção de componente `setup` é executada **antes** do componente ser criado, uma vez que as `props` estão resolvidas, e serve como ponto de entrada para APIs de composição.

::: warning
Como a instância do componente ainda não foi criada quando `setup` é executado, não há `this` dentro da opção `setup`. Isso significa que, com exceção de `props`, você não poderá acessar nenhuma propriedade declarada no componente – **estado local**, **propriedades _computed_** ou **métodos**.
:::

A opção `setup` deve ser uma função que aceita `props` e `context`, sobre os quais falaremos [depois] (composition-api-setup.html#arguments). Além disso, tudo o que retornamos de `setup` será exposto ao resto do nosso componente (propriedades _computed_, métodos, _lifecycle hooks_ e assim por diante), bem como ao template do componente.

Vamos adicionar `setup` ao nosso componente:

```js
// src/components/UserRepositories.vue

export default {
  components: { RepositoriesFilters, RepositoriesSortBy, RepositoriesList },
  props: {
    user: { type: String }
  },
  setup(props) {
    console.log(props) // { user: '' }

    return {} // qualquer dado retornado aqui estará disponível para o restante do componente
  }
  // o restante do componente
}
```

Agora vamos começar extraindo a primeira preocupação lógica (marcada como "1" no trecho original).

> 1. Obter repositórios para esse nome de usuário de uma API, presumivelmente externa, e atualizá-los sempre que o usuário mudar

Começaremos com as partes mais óbvias:

- A lista de repositórios;
- A função para atualizar a lista de repositórios; e
- Retornar a lista e a função para que sejam acessíveis por outras opções de componentes.


```js
// src/components/UserRepositories.vue função `setup`
import { fetchUserRepositories } from '@/api/repositories'

// dentro do nosso componente
setup (props) {
  let repositories = []
  const getUserRepositories = async () => {
    repositories = await fetchUserRepositories(props.user)
  }

  return {
    repositories,
    getUserRepositories // as funções retornadas se comportam da mesma forma que os métodos
  }
}
```

Este é o nosso ponto de partida, exceto que ainda não está funcionando porque nossa variável `repositories` não é reativa. Isso significa que, da perspectiva do usuário, a lista de repositórios permaneceria vazia. Vamos consertar isso!

### Variáveis reativas com `ref`

No Vue 3.0, podemos tornar qualquer variável reativa em qualquer lugar com uma nova função `ref`, como:

```js
import { ref } from 'vue'

const counter = ref(0)
```

`ref` pega o argumento e o retorna encapsulado em um objeto com uma propriedade `value`, que pode ser usada para acessar ou alterar o valor da variável reativa:

```js
import { ref } from 'vue'

const counter = ref(0)

console.log(counter) // { value: 0 }
console.log(counter.value) // 0

counter.value++
console.log(counter.value) // 1
```

Encapsular valores dentro de um objeto pode parecer desnecessário, mas é preciso para manter o comportamento unificado em diferentes tipos de dados em JavaScript. Isso ocorre porque, em JavaScript, tipos primitivos como `Number` ou `String` são passados por valor, não por referência:

![Passagem por referência vs passagem por valor](https://blog.penjee.com/wp-content/uploads/2015/02/pass-by-reference-vs-pass-by-value-animation.gif)

Ter um objeto encapsulado com qualquer valor nos permite passá-lo com segurança por toda a nossa aplicação, sem a preocupação de perder sua reatividade em algum lugar ao longo do caminho.

::: tip NOTA
Em outras palavras, `ref` cria uma **referência reativa** para nosso valor. O conceito de trabalhar com **referências** será usado frequentemente em toda a Composition API.
:::

De volta ao nosso exemplo, vamos criar uma variável `repositories` reativa:

```js
// src/components/UserRepositories.vue função `setup`
import { fetchUserRepositories } from '@/api/repositories'
import { ref } from 'vue'

// no nosso componente
setup (props) {
  const repositories = ref([])
  const getUserRepositories = async () => {
    repositories.value = await fetchUserRepositories(props.user)
  }

  return {
    repositories,
    getUserRepositories
  }
}
```

Feito! Agora, sempre que chamarmos `getUserRepositories`, a variável `repositories` será modificada e a visualização será atualizada para refletir a mudança. Nosso componente agora deve ter a seguinte aparência:

```js
// src/components/UserRepositories.vue
import { fetchUserRepositories } from '@/api/repositories'
import { ref } from 'vue'

export default {
  components: { RepositoriesFilters, RepositoriesSortBy, RepositoriesList },
  props: {
    user: { type: String }
  },
  setup (props) {
    const repositories = ref([])
    const getUserRepositories = async () => {
      repositories.value = await fetchUserRepositories(props.user)
    }

    return {
      repositories,
      getUserRepositories
    }
  },
  data () {
    return {
      filters: { ... }, // 3
      searchQuery: '' // 2
    }
  },
  computed: {
    filteredRepositories () { ... }, // 3
    repositoriesMatchingSearchQuery () { ... }, // 2
  },
  watch: {
    user: 'getUserRepositories' // 1
  },
  methods: {
    updateFilters () { ... }, // 3
  },
  mounted () {
    this.getUserRepositories() // 1
  }
}
```

Nós movemos várias partes de nossa primeira preocupação lógica para o método `setup`, bem colocadas próximas umas das outras. O que resta é chamar `getUserRepositories` no `mounted` e configurar um observador para fazer isso sempre que a propriedade `user` mudar.

Começaremos com o _lifecycle hook_.

### Registro de _Lifecycle Hook_ no `setup`


Para tornar a Composition API completa em comparação com a API de opções, também precisamos de uma maneira de registrar _lifecycle hooks_ dentro de `setup`. Isso é possível graças a várias novas funções exportadas do Vue. _Lifecycle hooks_ na Composition API têm o mesmo nome da API de opções, mas são prefixados com `on`: ou seja, `mounted` seria parecido com `onMounted`.

Essas funções aceitam um retorno de chamada que será executado quando o _hook_ for chamado pelo componente.

Vamos adicioná-lo à nossa função `setup`:

```js
// src/components/UserRepositories.vue função `setup`
import { fetchUserRepositories } from '@/api/repositories'
import { ref, onMounted } from 'vue'

// no nosso componente
setup (props) {
  const repositories = ref([])
  const getUserRepositories = async () => {
    repositories.value = await fetchUserRepositories(props.user)
  }

  onMounted(getUserRepositories) // quando `mounted`, chamar `getUserRepositories`

  return {
    repositories,
    getUserRepositories
  }
}
```

Agora precisamos reagir às mudanças feitas na propriedade `user`. Para isso, usaremos a função independente `watch`.

### Reagindo às mudanças com `watch`

Assim como configuramos um observador na propriedade `user` dentro de nosso componente usando a opção `watch`, podemos fazer o mesmo usando a função `watch` importada do Vue. Três argumentos são aceitos:

- Uma **referência reativa** ou função `getter` que queremos observar;
- Um retorno de chamada; ou
- Opções de configuração opcionais.

**Aqui está um exemplo de como funciona:*

```js
import { ref, watch } from 'vue'

const counter = ref(0)
watch(counter, (newValue, oldValue) => {
  console.log('O novo valor do contador é: ' + counter.value)
})
```

Sempre que `counter` é modificado, por exemplo `counter.value = 5`, o `watch` irá disparar e executar o retorno de chamada (segundo argumento) que neste caso irá registrar `'O novo valor do contador é: 5'` em nosso console.

**Abaixo está o equivalente da API de opções:**

```js
export default {
  data() {
    return {
      counter: 0
    }
  },
  watch: {
    counter(newValue, oldValue) {
      console.log('O novo valor do contador é: ' + this.counter)
    }
  }
}
```

Para mais detalhes sobre `watch`, consulte nosso [guia aprofundado]().

**Vamos agora aplicá-lo ao nosso exemplo:**

```js
// src/components/UserRepositories.vue função `setup`
import { fetchUserRepositories } from '@/api/repositories'
import { ref, onMounted, watch, toRefs } from 'vue'

// in our component
setup (props) {
  // usa `toRefs` para criar uma referência reativa para a propriedade `user`
  const { user } = toRefs(props)

  const repositories = ref([])
  const getUserRepositories = async () => {
    // atualiza `props.user` para `user.value`, para acessar o valor de referência
    repositories.value = await fetchUserRepositories(user.value)
  }

  onMounted(getUserRepositories)

  // define um observador na referência reativa para a propriedade `user`
  watch(user, getUserRepositories)

  return {
    repositories,
    getUserRepositories
  }
}
```

Você provavelmente notou o uso de `toRefs` no topo de nosso `setup`. Isso é para garantir que nosso observador reaja às mudanças feitas na propriedade `user`.

Com essas mudanças em vigor, acabamos de mover toda a primeira preocupação lógica para um único lugar. Agora podemos fazer o mesmo com a segunda preocupação - filtrar com base em `searchQuery`, desta vez com uma propriedade computada.

### Propriedades `computed` independentes

Semelhante a `ref` e `watch`, as propriedades `computed` também podem ser criadas fora de um componente Vue com a função `computed` importada do Vue. Vamos voltar ao nosso contra-exemplo:

```js
import { ref, computed } from 'vue'

const counter = ref(0)
const twiceTheCounter = computed(() => counter.value * 2)

counter.value++
console.log(counter.value) // 1
console.log(twiceTheCounter.value) // 2
```

Aqui, a função `computed` retorna uma **referência reativa** de somente leitura na saída do retorno da chamada "tipo getter", passado como o primeiro argumento para `computed`. Para acessar o atributo **value** da variável computada recém-criada, precisamos usar a propriedade `.value` assim como com `ref`.

Vamos mover nossa funcionalidade de pesquisa para `setup`:

```js
// src/components/UserRepositories.vue função `setup`
import { fetchUserRepositories } from '@/api/repositories'
import { ref, onMounted, watch, toRefs, computed } from 'vue'

// no nosso componente
setup (props) {
  // usa `toRefs` para criar uma referência reativa para a propriedade `user`
  const { user } = toRefs(props)

  const repositories = ref([])
  const getUserRepositories = async () => {
    // atualiza `props.user` para `user.value`, para acessar o valor de referência
    repositories.value = await fetchUserRepositories(user.value)
  }

  onMounted(getUserRepositories)

  // define um observador na referência reativa para a propriedade `user`
  watch(user, getUserRepositories)

  const searchQuery = ref('')
  const repositoriesMatchingSearchQuery = computed(() => {
    return repositories.value.filter(
      repository => repository.name.includes(searchQuery.value)
    )
  })

  return {
    repositories,
    getUserRepositories,
    searchQuery,
    repositoriesMatchingSearchQuery
  }
}
```

Poderíamos fazer o mesmo com outras **preocupações lógicas**, mas você já deve estar se perguntando – _Isso não é apenas mover o código para a opção `setup` e torná-lo extremamente grande?_ Bem, é verdade. É por isso que antes de prosseguirmos com as outras responsabilidades, primeiro iremos extrair o código acima em uma **função de composição** autônoma. Vamos começar criando `useUserRepositories`:

```js
// src/composables/useUserRepositories.js

import { fetchUserRepositories } from '@/api/repositories'
import { ref, onMounted, watch } from 'vue'

export default function useUserRepositories(user) {
  const repositories = ref([])
  const getUserRepositories = async () => {
    repositories.value = await fetchUserRepositories(user.value)
  }

  onMounted(getUserRepositories)
  watch(user, getUserRepositories)

  return {
    repositories,
    getUserRepositories
  }
}
```

E então a funcionalidade de pesquisa:

```js
// src/composables/useRepositoryNameSearch.js

import { ref, computed } from 'vue'

export default function useRepositoryNameSearch(repositories) {
  const searchQuery = ref('')
  const repositoriesMatchingSearchQuery = computed(() => {
    return repositories.value.filter(repository => {
      return repository.name.includes(searchQuery.value)
    })
  })

  return {
    searchQuery,
    repositoriesMatchingSearchQuery
  }
}
```

**Agora que temos essas duas funcionalidades em arquivos separados, podemos começar a usá-las em nosso componente. Veja como isso pode ser feito:**

```js
// src/components/UserRepositories.vue
import useUserRepositories from '@/composables/useUserRepositories'
import useRepositoryNameSearch from '@/composables/useRepositoryNameSearch'
import { toRefs } from 'vue'

export default {
  components: { RepositoriesFilters, RepositoriesSortBy, RepositoriesList },
  props: {
    user: { type: String }
  },
  setup (props) {
    const { user } = toRefs(props)

    const { repositories, getUserRepositories } = useUserRepositories(user)

    const {
      searchQuery,
      repositoriesMatchingSearchQuery
    } = useRepositoryNameSearch(repositories)

    return {
      // Uma vez que realmente não nos importamos com os repositórios não filtrados
      // podemos expor os resultados filtrados sob o nome de `repositories`
      repositories: repositoriesMatchingSearchQuery,
      getUserRepositories,
      searchQuery,
    }
  },
  data () {
    return {
      filters: { ... }, // 3
    }
  },
  computed: {
    filteredRepositories () { ... }, // 3
  },
  methods: {
    updateFilters () { ... }, // 3
  }
}
```

Neste ponto, você provavelmente já conhece o procedimento, então vamos pular para o final e migrar a funcionalidade de filtragem restante. Na verdade, não precisamos entrar em detalhes de implementação, pois esse não é o objetivo deste guia.

```js
// src/components/UserRepositories.vue
import { toRefs } from 'vue'
import useUserRepositories from '@/composables/useUserRepositories'
import useRepositoryNameSearch from '@/composables/useRepositoryNameSearch'
import useRepositoryFilters from '@/composables/useRepositoryFilters'

export default {
  components: { RepositoriesFilters, RepositoriesSortBy, RepositoriesList },
  props: {
    user: { type: String }
  },
  setup(props) {
    const { user } = toRefs(props)

    const { repositories, getUserRepositories } = useUserRepositories(user)

    const {
      searchQuery,
      repositoriesMatchingSearchQuery
    } = useRepositoryNameSearch(repositories)

    const {
      filters,
      updateFilters,
      filteredRepositories
    } = useRepositoryFilters(repositoriesMatchingSearchQuery)

    return {
      // Uma vez que realmente não nos importamos com os repositórios não filtrados
      // podemos expor os resultados finais sob o nome de `repositories`
      repositories: filteredRepositories,
      getUserRepositories,
      searchQuery,
      filters,
      updateFilters
    }
  }
}
```

E nós terminamos!

Lembre-se de que apenas arranhamos a superfície da Composition API e o que ela nos permite fazer. Para saber mais sobre isso, consulte o guia detalhado.
