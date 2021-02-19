# Registro de Componentes

> Esta página assume que você já leu o [Básico sobre Componentes](component-basics.md). Leia lá primeiro se você for novo no assunto de componentização.

## Nomes de Componentes

Ao registrar um componente, ele sempre receberá um nome. Por exemplo, no registro global que vimos até agora:

```js
const app = Vue.createApp({...})

app.component('my-component-name', {
  /* ... */
})
```

O nome do componente é o primeiro argumento de `app.component`. No exemplo acima, o nome do componente é "my-component-name".

O nome que você dá a um componente pode depender de onde você pretende usá-lo. Ao usar um componente diretamente no DOM (ao contrário de em um template string ou [componente single file](../guide/single-file-component.html)), recomendamos seguir as [regras do W3C](https://html.spec.whatwg.org/multipage/custom-elements.html#valid-custom-element-name) para nomes de tags personalizadas:

1. Todas as letras minúsculas;
2. Conter hífen (ou seja, ter várias palavras conectadas com o símbolo de hífen).

Ao nomear dessa maneira, isso o ajudará a evitar conflitos com elementos HTML atuais e futuros.

Você pode ver outras recomendações para nomes de componentes no [Guia de Estilo](../style-guide/#base-component-names-strongly-recommended).

### Padrão de Nomeação

Ao definir componentes em um template string ou um componente single file, você tem duas opções ao definir os nomes dos componentes:

#### Com kebab-case

```js
app.component('my-component-name', {
  /* ... */
})
```

Ao definir um componente com kebab-case, você também deve usar kebab-case ao fazer referência a seu elemento personalizado, como em `<my-component-name>`.

#### Com PascalCase

```js
app.component('MyComponentName', {
  /* ... */
})
```

Ao definir um componente com PascalCase, você pode usar qualquer um dos padrões ao fazer referência a seu elemento personalizado. Isso significa que ambos`<my-component-name>` e `<MyComponentName>` são aceitáveis. Observe, no entanto, que apenas os nomes kebab-case são válidos diretamente no DOM (ou seja, templates não-string).

## Registro Global

Até agora, só criamos componentes usando `app.component`:

```js
Vue.createApp({...}).component('my-component-name', {
  // ... opções ...
})
```

Esses componentes são **globalmente registrados** para a aplicação. Isso significa que eles podem ser usados no template de qualquer instância de componente nesta aplicação:

```js
const app = Vue.createApp({})

app.component('component-a', {
  /* ... */
})
app.component('component-b', {
  /* ... */
})
app.component('component-c', {
  /* ... */
})

app.mount('#app')
```

```html
<div id="app">
  <component-a></component-a>
  <component-b></component-b>
  <component-c></component-c>
</div>
```

Isso se aplica até mesmo a todos os subcomponentes, o que significa que estes três componentes também estarão disponíveis _dentro uns dos outros_.

## Registro Local

O registro global geralmente não é o ideal. Por exemplo, se você estiver usando um sistema de empacotador de módulos como o Webpack, registrar globalmente todos os componentes significa que, mesmo se você parar de usar um componente, ele ainda poderá ser incluído na sua distribuição final. Isso aumenta desnecessariamente a quantidade de código JavaScript que seus usuários precisam baixar.

Nesses casos, você pode definir seus componentes como objetos JavaScript simples:

```js
const ComponentA = {
  /* ... */
}
const ComponentB = {
  /* ... */
}
const ComponentC = {
  /* ... */
}
```

Em seguida, defina os componentes que deseja usar na opção `components`:

```js
const app = Vue.createApp({
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```

Para cada propriedade no objeto `components`, a chave será o nome do elemento personalizado, enquanto o valor conterá o objeto de opções para o componente.

Observe que **componentes registrados localmente _não_ estão disponíveis em subcomponentes**. Por exemplo, se você quiser que `ComponentA` esteja disponível em `ComponentB`, você terá que usar:

```js
const ComponentA = {
  /* ... */
}

const ComponentB = {
  components: {
    'component-a': ComponentA
  }
  // ...
}
```

Ou se você estiver usando módulos ES2015, como por meio de Babel e Webpack, isso pode se parecer mais com:

```js
import ComponentA from './ComponentA.vue'

export default {
  components: {
    ComponentA
  }
  // ...
}
```

Observe que na ES2015+, colocar um nome de variável como `ComponentA` dentro de um objeto é uma abreviação de `ComponentA: ComponentA`, o que significa que o nome da variável é tanto:

- o nome do elemento personalizado para usar no modelo, quanto
- o nome da variável que contém as opções do componente.

## Sistemas de Módulo

Se você não estiver usando um sistema de módulo com `import`/`require`, você provavelmente pode pular esta seção por enquanto. Se estiver, temos algumas instruções e dicas especiais para você.

### Registro Local em um Sistema de Módulo

Se você ainda está aqui, é provável que esteja usando um sistema de módulo, como o Babel e o Webpack. Nestes casos, recomendamos a criação de um diretório `components`, com cada componente em seu próprio arquivo.

Em seguida, você precisará importar cada componente que deseja usar, antes de registrá-lo localmente. Por exemplo, em um hipotético arquivo `ComponentB.js` ou `ComponentB.vue`:

```js
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  }
  // ...
}
```

Agora, tanto `ComponentA` quanto `ComponentC` podem ser usados dentro do template de `ComponentB`.
