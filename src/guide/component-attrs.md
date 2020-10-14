# Atributos Não-Propriedades

> Esta página assume que você já leu o [Básico sobre Componentes](component-basics.md). Leia lá primeiro se você for novo em componentes.

Um atributo de componente não-propriedade é um atributo ou ouvinte de evento que é passado para um componente, mas não tem uma propriedade correspondente definida em [props](component-props) ou [emits](component-custom-events.html#defining-custom-events). Os exemplos comuns disto incluem atributos `class`, `style`, e `id`. Você pode acessar esses atributos por meio da propriedade `$attrs`.

## Herança de Atributos

Quando um componente retorna um único nó raiz, atributos não-propriedade serão automaticamente adicionados aos atributos do nó raiz. Por exemplo, na instância de um componente date-picker:

```js
app.component('date-picker', {
  template: `
    <div class="date-picker">
      <input type="datetime" />
    </div>
  `
})
```

No caso de precisarmos definir o status do componente date-picker por meio de uma propriedade `data-status`, ele será aplicado ao nó raiz (ou seja, `div.date-picker`).

```html
<!-- Componente date-picker com um atributo não-propriedade -->
<date-picker data-status="activated"></date-picker>

<!-- Componente date-picker renderizado -->
<div class="date-picker" data-status="activated">
  <input type="datetime" />
</div>
```

A mesma regra se aplica aos ouvintes de eventos:

```html
<date-picker @change="submitChange"></date-picker>
```

```js
app.component('date-picker', {
  created() {
    console.log(this.$attrs) // { onChange: () => {}  }
  }
})
```

Isso pode ser útil quando temos um elemento HTML com um evento `change` como elemento raiz de `date-picker`.

```js
app.component('date-picker', {
  template: `
    <select>
      <option value="1">Ontem</option>
      <option value="2">Hoje</option>
      <option value="3">Amanhã</option>
    </select>
  `
})
```

Nesse caso, o evento `change` é passado do componente pai para o filho e será acionado no evento nativo `change` do `<select>`. Não precisamos emitir um evento de `date-picker` explicitamente:

```html
<div id="date-picker" class="demo">
  <date-picker @change="showChange"></date-picker>
</div>
```

```js
const app = Vue.createApp({
  methods: {
    showChange(event) {
      console.log(event.target.value) // exibirá o valor da opção selecionada
    }
  }
})
```

## Desativando a Herança de Atributos

Se você **não** deseja que um componente herde atributos automaticamente, você pode definir `inheritAttrs: false` nas opções do componente.

O cenário comum para desativar uma herança de atributo é quando os atributos precisam ser aplicados a outros elementos além do nó raiz.

Ao definir a opção `inheritAttrs` para `false`, você pode controlar a aplicação em outros atributos dos elementos, para que usem a propriedade `$attrs` do componente, que inclui todos os atributos não incluídos às propriedades `props` e `emits` do componente (por exemplo, `class`, `style`, eventos `v-on`, etc.).

Usando nosso exemplo de componente date-picker da [seção anterior]('#heranca-de-atributos), no caso de precisarmos aplicar todos os atributos não-propriedade ao elemento `input` em vez do elemento `div` raiz, isso pode ser feito usando o atalho `v-bind`.

```js{5}
app.component('date-picker', {
  inheritAttrs: false,
  template: `
    <div class="date-picker">
      <input type="datetime" v-bind="$attrs" />
    </div>
  `
})
```

Com esta nova configuração, nosso atributo `data-status` será aplicado ao nosso elemento `input`!

```html
<!-- Componente date-picker com um atributo não-propriedade -->
<date-picker data-status="activated"></date-picker>

<!-- Componente date-picker renderizado -->
<div class="date-picker">
  <input type="datetime" data-status="activated" />
</div>
```

## Herança de Atributos em Vários Nós Raízes

Ao contrário dos componentes de um único nó raiz, os componentes com vários nós raízes não têm um comportamento de falha de atributo automático. Se `$attrs` não for vinculado explicitamente, um aviso de tempo de execução será emitido.

```html
<custom-layout id="custom-layout" @click="changeValue"></custom-layout>
```

```js
// Isso irá gerar um aviso
app.component('custom-layout', {
  template: `
    <header>...</header>
    <main>...</main>
    <footer>...</footer>
  `
})

// Sem avisos, $attrs são passados para o elemento <main>
app.component('custom-layout', {
  template: `
    <header>...</header>
    <main v-bind="$attrs">...</main>
    <footer>...</footer>
  `
})
```
