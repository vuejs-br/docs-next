# Suporte ao TypeScript

> O [Vue CLI](https://cli.vuejs.org) fornece ferramentas integradas de suporte ao TypeScript.

## Declaração Oficial em Pacotes NPM

Um sistema de tipagem estática pode ajudar a prevenir diversos potenciais erros de _runtime_ conforme as aplicações crescem, é por isso que o Vue 3 é escrito em TypeScript. Isso significa que você não precisa de nenhum ferramental adicional para usar TypeScript com Vue - temos um suporte de primeiro mundo.

## Configuração Recomendada

```js
// tsconfig.json
{
  "compilerOptions": {
    "target": "esnext",
    "module": "esnext",
    // isso permite uma inferência mais estrita para propriedades de dados no `this`
    "strict": true,
    "jsx": "preserve",
    "moduleResolution": "node"
  }
}
```

Observe que você precisa incluir `strict: true` (ou ao menos `noImplicitThis: true`, que é uma parte da _flag_ `strict`) para aumentar a checagem do `this` em métodos de componentes. Caso contrário, será sempre tratado como tipo `any`.

Veja a [documentação das opções do compilador TypeScript](https://www.typescriptlang.org/docs/handbook/compiler-options.html) para mais detalhes.

## Ferramentas de Desenvolvimento

### Criação de Projeto

O [Vue CLI](https://github.com/vuejs/vue-cli) pode gerar novos projetos que usam TypeScript. Para começar:

```bash
# 1. Instale o Vue CLI, se não estiver instalado
npm install --global @vue/cli

# 2. Crie um novo projeto e então escolha a opção "Manually select features"
vue create my-project-name

# Se você já tem um projeto com Vue CLI sem TypeScript, por favor adicione o plugin apropriado:
vue add typescript
```

Certifique-se que a parte `script` do componente tem o TypeScript definido como linguagem:

```html
<script lang="ts">
  ...
</script>
```

### Suporte do Editor

Para o desenvolvimento de aplicações Vue com TypeScript, nós recomendamento fortemente usar o [Visual Studio Code](https://code.visualstudio.com/), que fornece um excelente suporte para o TypeScript. Se você está usando [componentes Single File](./single-file-component.html) (SFCs), obtenha a incrível [extensão Vetur](https://github.com/vuejs/vetur), que provê a inferência do TypeScript dentro de SFCs e muitos outros ótimos recursos.

O [WebStorm](https://www.jetbrains.com/webstorm/) também fornece um suporte pronto pra uso para ambos, TypeScript e Vue.

## Definindo componentes Vue

Para permitir o TypeScript inferir corretamente os tipos dentro das opções do componente Vue, você precisa definir os componentes com o método global `defineComponent`:

```ts
import { defineComponent } from 'vue'

const Component = defineComponent({
  // inferência de tipos habilitada
})
```

## Usando a API de Opções

O TypeScript deve ser capaz de inferir a maioria dos tipos sem defini-los explicitamente. Por exemplo, se você tem um componente com uma propriedade numérica `count`, você receberá um erro se tentar chamar um método específico de _string_ nela.

```ts
const Component = defineComponent({
  data() {
    return {
      count: 0
    }
  },
  mounted() {
    const result = this.count.split('') // => A propriedade 'slit' não existe no tipo 'number'
  }
})
```

Se você tem um tipo complexo ou uma interface, pode convertê-lo(a) usando a [afirmação de tipo](https://www.typescriptlang.org/docs/handbook/basic-types.html#type-assertions):

```ts
interface Book {
  title: string
  author: string
  year: number
}

const Component = defineComponent({
  data() {
    return {
      book: {
        title: 'Vue 3 Guide',
        author: 'Vue Team',
        year: 2020
      } as Book
    }
  }
})
```

### Anotando Tipos de Retorno

Por causa da natureza circular dos arquivos de declaração do Vue, o TypeScript pode ter dificuldades para inferir os tipos de dados computados. Por esse motivo, você precisa anotar o tipo do retorno de propriedades computadas.

```ts
import { defineComponent } from 'vue'

const Component = defineComponent({
  data() {
    return {
      message: 'Hello!'
    }
  },
  computed: {
    // precisa de uma anotação
    greeting(): string {
      return this.message + '!'
    }

    // em uma computada com um setter, o getter precisa ser anotado
    greetingUppercased: {
      get(): string {
        return this.greeting.toUpperCase();
      },
      set(newValue: string) {
        this.message = newValue.toUpperCase();
      },
    },
  }
})
```

### Anotando Propriedades

O Vue faz uma validação em runtime em propriedades com um `type` definido. Para fornecer esses tipos para o TypeScript, precisamos converter o construtor com `PropType`:

```ts
import { defineComponent, PropType } from 'vue'

interface ComplexMessage {
  title: string
  okMessage: string
  cancelMessage: string
}
const Component = defineComponent({
  props: {
    name: String,
    success: { type: String },
    callback: {
      type: Function as PropType<() => void>
    },
    message: {
      type: Object as PropType<ComplexMessage>,
      required: true,
      validator(message: ComplexMessage) {
        return !!message.title
      }
    }
  }
})
```

Se você descobrir que o validador não está obtendo a inferência de tipo ou a conclusão do membro não está funcionando, anotar o argumento com o tipo esperado pode ajudar a resolver esse problema.

## Usando com a API de Composição

Na função `setup()`, você não precisa passar um tipo para o parâmetro `props`, pois os tipos serão inferidos da opção `props` do componente.

```ts
import { defineComponent } from 'vue'

const Component = defineComponent({
  props: {
    message: {
      type: String,
      required: true
    }
  },

  setup(props) {
    const result = props.message.split('') // correto, 'message' é tipada como uma string
    const filtered = props.message.filter(p => p.value) // um erro será disparado: Propriedade 'filter' não existe no tipo 'string'
  }
})
```

### Tipando `ref`s

Refs inferem o tipo do valor inicial:

```ts
import { defineComponent, ref } from 'vue'

const Component = defineComponent({
  setup() {
    const year = ref(2020)

    const result = year.value.split('') // => Propriedade 'split' não existe no tipo 'number'
  }
})
```

Algumas vezes, podemos precisar especificar tipos complexos para o valor interno de uma ref. Podemos fazer isso simplesmente passando um argumento genérico ao chamar ref para sobrescrever a inferência padrão:

```ts
const year = ref<string | number>('2020') // tipo do year: Ref<string | number>

year.value = 2020 // ok!
```

::: tip Nota
Se o tipo do genérico é desconhecido, é recomendado converter `ref` para `Ref<T>`.
:::

### Tipando `reactive`

Ao tipar uma propriedade `reactive`, podemos usar interfaces:

```ts
import { defineComponent, reactive } from 'vue'

interface Book {
  title: string
  year?: number
}

export default defineComponent({
  name: 'HelloWorld',
  setup() {
    const book = reactive<Book>({ title: 'Vue 3 Guide' })
    // ou
    const book: Book = reactive({ title: 'Vue 3 Guide' })
    // ou
    const book = reactive({ title: 'Vue 3 Guide' }) as Book
  }
})
```

### Tipando `computed`

Valores computador irão inferir automaticamente o tipo do valor retornado

```ts
import { defineComponent, ref, computed } from 'vue'

export default defineComponent({
  name: 'CounterButton',
  setup() {
    let count = ref(0)

    // apenas leitura
    const doubleCount = computed(() => count.value * 2)

    const result = doubleCount.value.split('') // => Propriedade 'split' não existe no tipo 'number'
  }
})
```
