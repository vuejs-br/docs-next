---
badges:
  - breaking
---

# `v-model` <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

Olhando por cima o que mudou:

- **QUEBRA:** Quando usado em componentes customizados, o nome padrão da propriedade e do evento do `v-model` mudaram:
  - prop: `value` -> `modelValue`;
  - event: `input` -> `update:modelValue`;
- **QUEBRA:** O modificador `.sync` que existia no `v-bind` e a opção `model` de componentes foram substituídos pelo `v-model` com um argumento;
- **NOVO:** Múltiplos vínculos do `v-model` no mesmo componente são possíveis agora;
- **NOVO:** Adicionado a possibilidade de criar modificadores para o `v-model`.

Para mais informações, continue lendo!

## Introdução

Quando o Vue 2.0 foi lançado, a diretiva `v-model` exigia para os desenvolvedores sempre usar a propriedade `value`. E se os desenvolvedores precisassem usar uma propriedade diferente para um outro propósito, eles deveriam recorrer ao uso do `v-bind.sync`. Além disso, essa relação fixa entre `v-model` e` value` levou à problemas com a forma como os elementos nativos e personalizados eram tratados.

Na versão 2.2, introduzimos a opção de componente `model` que permite ao componente personalizar a propriedade e o evento para usar no `v-model`. No entanto, isso ainda apenas permitia que um único `v-model` fosse usado no componente.

Com o Vue 3, a API para vinculação de dados bidirecional está sendo padronizada para reduzir a confusão e permitir aos desenvolvedores mais flexibilidade com a diretiva `v-model`.

## Sintaxe v2.x

Na v2.x, usando um `v-model` em um componente era equivalente a passar uma propriedade `value` e emitir um evento `input`:

```html
<ChildComponent v-model="pageTitle" />

<!-- seria um atalho para: -->

<ChildComponent :value="pageTitle" @input="pageTitle = $event" />
```

Se quiséssemos mudar os nomes da propriedade ou evento para algo diferente, precisaríamos adicionar uma opção `model` ao componente `ChildComponent`:

```html
<!-- ParentComponent.vue -->

<ChildComponent v-model="pageTitle" />
```

```js
// ChildComponent.vue

export default {
  model: {
    prop: 'title',
    event: 'change'
  },
  props: {
    // isso permite a utilização da propriedade `value` para um propósito diferente
    value: String,
    // utilizando `title` como uma propriedade que irá tomar o lugar do `value`
    title: {
      type: String,
      default: 'Título padrão'
    }
  }
}
```

Então, o `v-model` nesse caso seria um atalho para:

```html
<ChildComponent :title="pageTitle" @change="pageTitle = $event" />
```

### Usando `v-bind.sync`

Em alguns casos, podemos precisar de uma "ligação bidirecional" para uma propriedade (às vezes além do `v-model` existente para uma propriedade diferente). Para fazer isso, recomendamos a emissão de eventos no padrão de `update:myPropName`. Por exemplo, para `ChildComponent` do exemplo anterior com a propriedade `title`, poderíamos comunicar a intenção de atribuir um novo valor com:

```js
this.$emit('update:title', newValue)
```

Em seguida, o pai pode ouvir esse evento e atualizar uma propriedade de dados local, se quiser. Por exemplo:

```html
<ChildComponent :title="pageTitle" @update:title="pageTitle = $event" />
```

Por conveniência, tínhamos uma abreviatura para esse padrão com o modificador .sync:

```html
<ChildComponent :title.sync="pageTitle" />
```

## Sintaxe v3.x

Na versão 3.x o `v-model` em um componente personalizado é equivalente a passar um prop `modelValue` e emitir um evento `update:modelValue`:

```html
<ChildComponent v-model="pageTitle" />

<!-- seria um atalho para: -->

<ChildComponent
  :modelValue="pageTitle"
  @update:modelValue="pageTitle = $event"
/>
```

### Argumentos do `v-model`

Para alterar o nome de um modelo, em vez de uma opção de componente `model`, agora podemos passar um _argumento_ para` v-model`:

```html
<ChildComponent v-model:title="pageTitle" />

<!-- seria um atalho para: -->

<ChildComponent :title="pageTitle" @update:title="pageTitle = $event" />
```

![Anatomia do v-bind](/images/v-bind-instead-of-sync.png)

Isso também serve como um substituto para o modificador `.sync` e nos permite ter vários `v-model`s no componente personalizado.

```html
<ChildComponent v-model:title="pageTitle" v-model:content="pageContent" />

<!-- seria um atalho para: -->

<ChildComponent
  :title="pageTitle"
  @update:title="pageTitle = $event"
  :content="pageContent"
  @update:content="pageContent = $event"
/>
```

### Modificadores do `v-model`

Além dos modificadores do `v-model` fixos já existentes na v2.x, como `.trim`, agora v3.x suporta modificadores personalizados:

```html
<ChildComponent v-model.capitalize="pageTitle" />
```

Leia mais sobre modificadores do `v-model` customizados na seção [Eventos Customizados](../component-custom-events.html#handling-v-model-modifiers).

## Estratégia de Migração

Nós recomendamos:

- verificar seu código aonde utilizado o `.sync` e substituí-lo por `v-model`:

  ```html
  <ChildComponent :title.sync="pageTitle" />

  <!-- substituir por -->

  <ChildComponent v-model:title="pageTitle" />
  ```

- para todos os `v-model`s sem argumentos, certifique-se de alterar o nome das propriedades e eventos para `modelValue` e `update:modelValue` respectivamente

  ```html
  <ChildComponent v-model="pageTitle" />
  ```

  ```js
  // ChildComponent.vue

  export default {
    props: {
      modelValue: String // anteriormente era `value: String`
    },
    methods: {
      changePageTitle(title) {
        this.$emit('update:modelValue', title) // anteriormente era `this.$emit('input', title)`
      }
    }
  }
  ```

## Próximos Passos

Para mais informações na nova sintaxe do `v-model`, veja:

- [Utilizando `v-model` em Componentes](../component-basics.html#using-v-model-on-components)
- [Argumentos do `v-model`](../component-custom-events.html#v-model-arguments)
- [Tratando modificadores do `v-model`](../component-custom-events.html#v-model-arguments)
