# Diretivas customizadas (Custom Directives)

## Introdução

Fora o conjunto de diretivas padrão existente por padrão no core (tipo `v-model` ou `v-show`), o Vue também possibilita registrar diretivas customizadas. Note que no Vue, a forma primária de reuso e abstração de código são os componentes. No entanto, pode haver casos em que você precise um acesso de nível mais baixo aos elementos no DOM, e é aqui que as diretivas customizadas são úteis. Um exemplo seria acionar o foco em um elemento de input, como esse:


<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="JjdxaJW" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Custom directives: basic example">
  <span>Veja o Pen <a href="https://codepen.io/team/Vue/pen/JjdxaJW">
  Exemplo básico de diretiva customizada</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  em <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Quando a página carregar , o elemento é focado (nota: o `autofocus` não funciona no Safari de mobile). Na verdade, se você não clicou em nada desde que visitou essa página, o input acima deve estar focado agora. Além disso, você pode clicar no botão`Rerun` e o input vai ganhar foco.

Agora vamos construir a diretiva que faz isso:

```js
const app = Vue.createApp({})
// Registar uma diretiva customizada e global chamada `v-focus`
app.directive('focus', {
  // Quando o elemento é montado ao DOM
  mounted(el) {
    // Foca o elemento
    el.focus()
  }
})
```
Se você quer registrar uma diretiva local, os componentes também aceitam a opção `directives`:

```js
directives: {
  focus: {
    // definição da diretiva
    mounted(el) {
      el.focus()
    }
  }
}
```

Então no template, você pode usar o novo atributo `v-focus` em qualquer elemento, tipo assim:

```html
<input v-focus />
```

## Funções de hook (Hook Functions)

As funções de hook são as funções que são executadas conforme o estado da diretiva e há uma variedade disponível para uso (todas opcionais):

- `beforeMount`: Executa quando a diretiva é ligada pela primeira vez ao elemento e antes que o componente pai seja montado. É aqui que você faz a configuração que é feita uma vez apenas.

- `mounted`: Executa quando o componente pai do elemento ligado é montado.

- `beforeUpdate`: Executa antes que os VNode contido no componente são atualizados.

> Nota:
> Vamos cobrir VNodes com mais detalhes [depois](render-function.html#the-virtual-dom-tree), quando discutirmos as funções de renderização (render functions).


- `updated`: Executado após os VNodes contidos no componente **e os filhos desses VNodes** terem sido atualizados.

- `beforeUnmount`: Executado antes do pai dos elementos ligados sejam desmontados.

- `unmounted`: Executado apenas uma vez, quando a diretiva é desligada do elemento e o componente pai é desmontado

Você pode checar os argumentos que foram passados para esses hooks (ex: `el`, `binding`, `vnode` e `prevNode`) em [API de diretivas customizadas](../api/application-api.html#directive)

### Argumentos dinâmicos da diretiva

Os argumentos da diretiva podem ser dinâmicos. Por exemplo, em `v-minhadiretiva:[argumento]="valor"`, o `argumento` pode ser atualizado baseada nas propriedades de dados na nossa instância do componente! Isso faz nossas diretivas customizadas flexíveis para utilizar na aplicação.

Digamos que você queira fazer uma diretiva customizada que permite "pregar" elementos na sua página utilizando posicionamento fixo. Nós poderiamos criar uma diretiva customizada onde o valor atualiza a posição vertical em pixels, desse jeito:

```vue-html
<div id="exemplo-argumentos-dinamicos" class="demo">
  <p>Role para baixo</p>
  <p v-pin="200">Me pregue 200px do topo da página</p>
</div>
```

```js
const app = Vue.createApp({})

app.directive('pin', {
  mounted(el, binding) {
    el.style.position = 'fixed'
    // binding.value é o valor que vamos passar para a diretiva - nesse caso, é 200.
    el.style.top = binding.value + 'px'
  }
})

app.mount('#exemplo-argumentos-dinamicos')
```

Isso iria pregar o elemento 200 px do topo da página. Mas o que acontece quando encontramos um cenário que precisamos pregar um elemento da esquerda, ao invés do topo? É aqui que os argumentos dinâmicos que podem ser atualizados por instância de componente são úteis:

```vue-html
<div id="exemplodinamico">
  <h3>Role para baixo nessa seção ↓</h3>
  <p v-pin:[direction]="200">Estou pregado na página a 200 px para a esquerda.</p>
</div>
```

```js
const app = Vue.createApp({
  data() {
    return {
      direction: 'right'
    }
  }
})

app.directive('pin', {
  mounted(el, binding) {
    el.style.position = 'fixed'
    // binding.arg é um argumento que passamos para a diretiva
    const s = binding.arg || 'top'
    el.style[s] = binding.value + 'px'
  }
})

app.mount('#exemplo-argumentos-dinamicos')
```

Resultado:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="YzXgGmv" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Custom directives: dynamic arguments">
  <span>Veja o Pen <a href="https://codepen.io/team/Vue/pen/YzXgGmv">
  Diretivas customizadas: argumentos dinâmicos</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  em <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

A nossa diretiva customizada agora está flexível o suficiente para atender a vários casos diferentes. Para deixá-lo mais dinâmico, podemos possibilitar alterar um valor ligado. Vamos criar uma propriedade adicional `pinPadding` e ligar ao `<input type="range">`

```vue-html{4}
<div id="exemplodinamico">
  <h2>Role a página para baixo</h2>
  <input type="range" min="0" max="500" v-model="pinPadding">
  <p v-pin:[direction]="pinPadding">Me pregue 200 px da {{ direction }} da página</p>
</div>
```

```js{5}
const app = Vue.createApp({
  data() {
    return {
      direction: 'right',
      pinPadding: 200
    }
  }
})
```

Agora vamos incrementar a lógica da diretiva para recalcular a distância dos elementos pregados quando atualizar o componente:

```js{7-10}
app.directive('pin', {
  mounted(el, binding) {
    el.style.position = 'fixed'
    const s = binding.arg || 'top'
    el.style[s] = binding.value + 'px'
  },
  updated(el, binding) {
    const s = binding.arg || 'top'
    el.style[s] = binding.value + 'px'
  }
})
```

Resultado:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="Vue" data-slug-hash="rNOaZpj" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Custom directives: dynamic arguments + dynamic binding">
  <span>Veja o Pen <a href="https://codepen.io/team/Vue/pen/rNOaZpj">
  Diretivas customizadas: argumentos dinâmicos + ligamento dinâmico</a> por Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  em <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Atalho de função (Function Shorthand)

No exemplo anterior, você pode querer o mesmo comportamento no `mounted` e `updated`, mas não liga para os outros hooks. Você pode fazer isso passando a callback para a diretiva:

```js
app.directive('pin', (el, binding) => {
  el.style.position = 'fixed'
  const s = binding.arg || 'top'
  el.style[s] = binding.value + 'px'
})
```

## Objetos literais (Object Literals)

Se a sua diretiva precisa de múltiplos valores, você também pode passar um objeto literal do javascript. Lembre-se que as diretivas pode receber qualquer expressão válida em javascript.

```vue-html
<div v-demo="{ color: 'white', text: 'ola!!' }"></div>
```

```js
app.directive('demo', (el, binding) => {
  console.log(binding.value.color) // => "white"
  console.log(binding.value.text) // => "ola!"
})
```

## Utilização nos componentes

No 3.0, com o suporte de fragmentos, os componentes podem ter mais de um nó raiz. Isso cria um problema quando a diretiva customizada é utilizada em um componente com múltiplos nós raiz.

Para explicar os detalhes de como as diretivas customizadas vão funcionar nos componentes no 3.0, nós precisamos primeiro entender como as diretivas customizadas são compiladas no 3.0. Para uma diretiva assim:

```vue-html
<div v-demo="test"></div>
```

Vai compilar mais ou menos para isso:

```js
const vDemo = resolveDirective('demo')

return withDirectives(h('div'), [[vDemo, test]])
```

Onde `vDemo` vai ser o objeto de diretiva escrita pelo usuário, que contem os hooks como `mounted` e `updated`.

`withDirectives` retorna um VNode clonada com os hooks do usuário embalados e injetados conforme os hooks do ciclo de vida aplicam ao VNode (Veja [Funções de renderização](render-function.html)) para mais detalhes:

```js
{
  onVnodeMounted(vnode) {
    // vai chamar o vDemo.mounted(...)
  }
}
```

**Como resultado, a diretiva customizada é incluida como parte dos dados do VNode. Quando a diretiva customizada é usada em um componente, esses hooks do `onVnodeXXX` são passados para o componente como atributos extras e ficam no `this.$attrs`**

Isso também significa que é possivel fazer o hook no ciclo de vida do elemento dessa maneira no template, o que pode ser útil quando uma diretiva customizada está envolvida:

```vue-html
<div @vnodeMounted="meuHook" />
```

Isso é consistente com o [comportamento de fallthrough do atributo](component-attrs.html). Então, a regra para as diretivas customizadas em um componente vai ser a mesma que outros atributos extras: vai do componente filho decidir onde e se vai aplica-lo. Quando o componente filho usa o `v-bind="$attrs"` em um elemento interno, ele vai aplicar em qualquer diretiva customizada utilizada nele também.
