# Interligações de Classe e Estilo

Uma necessidade comum de interligação de dados é manipular as classes dos elementos e seus estilos _inline_. Uma vez que ambos são atributos, podemos usar `v-bind` para lidar com eles: apenas precisamos calcular uma String final com nossas expressões. No entanto, mexer com concatenação de String é irritante e propenso a erros. Por esta razão, Vue fornece aprimoramentos especiais quando `v-bind` é usado com `class` e `style`. Além de Strings, as expressões também podem avaliar Objetos ou Arrays.

## Interligando Classes HTML

### Sintaxe do Objeto

Podemos passar um objeto para `:class` (abreviação de `v-bind:class`) para alternar classes dinamicamente:

```html
<div :class="{ active: isActive }"></div>
```

A sintaxe acima significa que a presença da classe `active` será determinada pela [veracidade](https://developer.mozilla.org/pt-BR/docs/Glossary/Truthy) do valor do dado `isActive`.

Você pode ter múltiplas classes alternadas tendo mais campos no objeto. Além disso, a diretiva `:class` também pode coexistir com um atributo de classe "normal". Então, dado o seguinte modelo:

```html
<div
  class="static"
  :class="{ active: isActive, 'text-danger': hasError }"
></div>
```

E os seguintes dados:

```js
data() {
  return {
    isActive: true,
    hasError: false
  }
}
```

Irá renderizar:

```html
<div class="static active"></div>
```

Quando `isActive` ou `hasError` mudar, a lista de classes será atualizada correspondentemente. Por exemplo, se `hasError` é `true`, a lista de classes será `"static active text-danger"`.

O objeto vinculado não precisa estar diretamente no _template_:

```html
<div :class="classObject"></div>
```

```js
data() {
  return {
    classObject: {
      active: true,
      'text-danger': false
    }
  }
}
```

Isso irá renderizar o mesmo resultado. Podemos também interligar a um [dado computado](computed.md) que retorne um objeto. Este é um padrão comum e poderoso:

```html
<div :class="classObject"></div>
```

```js
data() {
  return {
    isActive: true,
    error: null
  }
},
computed: {
  classObject() {
    return {
      active: this.isActive && !this.error,
      'text-danger': this.error && this.error.type === 'fatal'
    }
  }
}
```

### Sintaxe de Array

Podemos passar um Array para `:class` para aplicar uma lista de classes:

```html
<div :class="[activeClass, errorClass]"></div>
```

```js
data() {
  return {
    activeClass: 'active',
    errorClass: 'text-danger'
  }
}
```

Que renderizará:

```html
<div class="active text-danger"></div>
```

Se você preferir também alternar entre uma classe na lista condicionalmente, use uma expressão ternária:

```html
<div :class="[isActive ? activeClass : '', errorClass]"></div>
```

Isso sempre aplicará `errorClass`, mas somente aplicará `activeClass` quando `isActive` for verdadeiro.

No entanto, isso pode ser um tanto prolixo se você tiver várias classes condicionais. Por isso também é possível usar a sintaxe de objeto dentro da sintaxe de Array:

```html
<div :class="[{ active: isActive }, errorClass]"></div>
```

### Em Componentes

> Esta seção assume conhecimento sobre [Componentes Vue](component-basics.md). Pode pular esta parte e voltar depois.

Quando você usa o atributo `class` em um componente personalizado com um único elemento raiz, essas classes serão adicionadas a este elemento. As classes existentes neste elemento não serão substituídas.

Por exemplo, se você declarar este componente:

```js
const app = Vue.createApp({})

app.component('my-component', {
  template: `<p class="foo bar">Oi!</p>`
})
```

E então adicionar mais classes quando for utilizá-lo:

```html
<div id="app">
  <my-component class="baz boo"></my-component>
</div>
```

O HTML renderizado será:

```html
<p class="foo bar baz boo">Oi!</p>
```

O mesmo vale para a interligação de classe:

```html
<my-component :class="{ active: isActive }"></my-component>
```

Quando `isActive` for verdadeiro, o HTML renderizado será:

```html
<p class="foo bar active">Oi!</p>
```

Se o seu componente tiver vários elementos raiz, você precisará definir qual componente receberá essa classe utilizando a propriedade do componente `$attrs`:

```html
<div id="app">
  <my-component class="baz"></my-component>
</div>
```

```js
const app = Vue.createApp({})

app.component('my-component', {
  template: `
    <p :class="$attrs.class">Oi!</p>
    <span>Este é um component filho</span>
  `
})
```

Você pode aprender mais sobre herança de atributo de componente na seção [Atributos Não-Prop](component-attrs.html).

## Interligando Estilos Inline

### Object Syntax
### Sintaxe de Objeto

A sintaxe de objeto para `:style` é bem direta - parece quase como CSS, mas é um objeto JavaScript. Você pode usar tanto _camelCase_ quanto _kebab-case_ (use aspas com _kebab-case_) para os nomes das propriedades CSS.

```html
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```js
data() {
  return {
    activeColor: 'red',
    fontSize: 30
  }
}
```

Muitas vezes é uma boa ideia vincular os estilos diretamente em um objeto para que assim o _template_ fique mais limpo:

```html
<div :style="styleObject"></div>
```

```js
data() {
  return {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
}
```

Novamente, a sintaxe de objeto é frequentemente usada em conjunto com dados computados que retornam objetos.

### Sintaxe de Array

A sintaxe Array para `:style` permite que você aplique múltiplos objetos de estilo para o mesmo elemento:

```html
<div :style="[baseStyles, overridingStyles]"></div>
```

### Auto-Prefixação

Quando você usa uma propriedade CSS que requer [prefixos de fabricantes](https://developer.mozilla.org/pt-BR/docs/Glossary/Vendor_Prefix) em `:style`, por exemplo `transform`, Vue irá automaticamente detectar e adicionar os prefixos apropriados para os estilos aplicados.

### Valores Múltiplos

Você pode prover um Array com múltiplos valores (prefixados) para estilizar um atributo, por exemplo:

```html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

Isto irá renderizar apenas o último valor do Array que o navegador suportar. Neste exemplo, irá renderizar `display: flex` nos navegadores que suportam a versão sem prefixo do módulo Flexbox.
