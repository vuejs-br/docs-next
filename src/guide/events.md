# Manipulação de Eventos

## Escutando Eventos

Podemos usar a diretiva `v-on`, que normalmente abreviamos para o símbolo `@`, para escutar eventos do DOM e rodar algum JavaScript quando tal evento for disparado. A maneira de usar seria `v-on:click="nomeDoMétodo"` ou com o atalho, `@click="nomeDoMétodo"`

Por exemplo:

```html
<div id="basic-event">
  <button @click="counter += 1">Adicionar 1</button>
  <p>O botão acima foi clicado {{counter}} vezes.</p>
</div>
```

```js
Vue.createApp({
  data() {
    return {
      counter: 1
    }
  }
}).mount('#basic-event')
```

Resultado:

<common-codepen-snippet title="Exemplo básico de manipulação de eventos" slug="YzqMRBW" />

## Métodos em Manipuladores

A lógica para muitos manipuladores de evento será mais complexa, portanto, manter diretamente código JavaScript no valor do atributo `v-on` não é viável. É por isso que `v-on` também pode aceitar o nome de um método que você gostaria de chamar.

Por Exemplo:

```html
<div id="event-with-method">
  <!-- `greet` é o nome de um método definido abaixo -->
  <button @click="greet">Cumprimentar</button>
</div>
```

```js
Vue.createApp({
  data() {
    return {
      name: 'Vue.js'
    }
  },
  methods: {
    greet(event) {
      // `this` dentro de métodos aponta para a atual instância Vue ativa
      alert('Olá ' + this.name + '!')
      // `event` é o evento DOM nativo
      if (event) {
        alert(event.target.tagName)
      }
    }
  }
}).mount('#event-with-method')
```

Resultado:

<common-codepen-snippet title="Exemplo de manipulação de eventos com um método" slug="RwaOqvo" />

## Chamada Direta de Métodos

Em vez de interligar o evento diretamente ao nome de um método, também podemos chamar métodos diretamente com uma instrução JavaScript:

```html
<div id="inline-handler">
  <button @click="say('oi')">Diga oi</button>
  <button @click="say('tchau')">Diga tchau</button>
</div>
```

```js
Vue.createApp({
  methods: {
    say(message) {
      alert(message)
    }
  }
}).mount('#inline-handler')
```

Resultado:

<common-codepen-snippet title="Exemplo de evento com chamada direta de métodoo" slug="mdPgQvR" />

Às vezes, também precisamos acessar o evento original do DOM em um manipulador. Você pode passá-lo à um método usando a variável especial `$event`:

```html
<button @click="warn('O formulário ainda não pode ser enviado.', $event)">
  Enviar
</button>
```

```js
// ...
methods: {
  warn(message, event) {
    // agora temos acesso ao evento nativo
    if (event) {
      event.preventDefault()
    }
    alert(message)
  }
}
```

## Múltiplos Manipuladores de Eventos

Você pode ter vários métodos em um manipulador de eventos separados por vírgula, desta forma:

```html
<!-- ambos one() e two() serão executados no clique do botão -->
<button @click="one($event), two($event)">
  Enviar
</button>
```

```js
// ...
methods: {
  one(event) {
    // lógica do primeiro manipulador...
  },
  two(event) {
    // lógica do segundo manipulador...
  }
}
```

## Modificadores de Evento

É muito comum precisar chamar `event.preventDefault()` ou `event.stopPropagation()` em manipuladores de eventos. Embora possamos fazer isto facilmente dentro de métodos, seria melhor se os métodos pudessem lidar apenas com a lógica dos dados, em vez de ter que lidar com detalhes de eventos DOM.

Para resolver esse problema, Vue fornece **modificadores de evento** para `v-on`. É só se lembrar que modificadores são sufixos da diretiva, denotados por um ponto.

- `.stop`
- `.prevent`
- `.capture`
- `.self`
- `.once`
- `.passive`

```html
<!-- a propagação do evento click será interrompida -->
<a @click.stop="doThis"></a>

<!-- o evento submit deixará de recarregar a página -->
<form @submit.prevent="onSubmit"></form>

<!-- modificadores podem ser encadeados -->
<a @click.stop.prevent="doThat"></a>

<!-- é possível utilizar apenas o modificador -->
<form @submit.prevent></form>

<!-- usar modo de captura ao adicionar o evento -->
<!-- ou seja, um evento em um elemento interno é tratado aqui antes de ser tratado por aquele elemento -->
<div @click.capture="doThis">...</div>

<!-- só aciona o manipulador se event.target é o próprio elemento -->
<!-- isto é, não aciona a partir de um elemento filho -->
<div @click.self="doThat">...</div>
```

::: tip Nota
A ordem importa ao utilizar modificadores pois o código relevante é gerado na mesma ordem. Desta forma, `@click.prevent.self` irá prevenir **todos os cliques**, enquanto `@click.self.prevent` irá prevenir apenas cliques no próprio elemento.
:::

```html
<!-- o evento click será disparado apenas uma vez -->
<a @click.once="doThis"></a>
```

Diferente dos outros modificadores, que são exclusivos para eventos nativos, o modificador `.once` também pode ser usado em [eventos de componentes](component-custom-events.html). Se você ainda não leu sobre componentes, não se preocupe com isso neste momento.

Vue também oferece o modificador `.passive`, correspondendo à [opção `passive` do `addEventListener`](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Parameters).

```html
<!-- o comportamento padrão do evento _scroll_ (rolar) acontecerá -->
<!-- imediatamente, ao invés de aguardar `onScroll` completar   -->
<!-- para descobrir se ele chama `event.preventDefault()`       -->
<div @scroll.passive="onScroll">...</div>
```

O `.passive` é especialmente útil para otimizar desempenho em dispositivos móveis.

::: tip Nota
Não use `.passive` e `.prevent` juntos, pois `.prevent` será ignorado e seu navegador provavelmente exibirá um aviso. Lembre-se, `.passive` comunica ao navegador que você _não_ quer prevenir o comportamento padrão do evento.
:::

## Modificadores de Teclado

Quando escutamos eventos do teclado, precisamos muitas vezes verificar a ocorrência de teclas específicas. O Vue também permite a adição de modificadores `v-on` ou `@` ao escutar eventos de teclado:

```html
<!-- só chama `vm.submit()` quando o `key` é `Enter` -->
<input @keyup.enter="submit" />
```

Você pode usar diretamente qualquer nome de tecla válido exposto via [`KeyboardEvent.key`](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/key/Key_Values) como modificadores, convertendo-os em kebab-case.

```html
<input @keyup.page-down="onPageDown" />
```

No exemplo acima, o manipulador só será chamado se `$event.key` for igual a `'PageDown'`.

### Apelidos de Teclas

Vue fornece apelidos para os códigos de teclas mais comuns:

- `.enter`
- `.tab`
- `.delete` (captura tanto "Delete" quanto "Backspace")
- `.esc`
- `.space`
- `.up`
- `.down`
- `.left`
- `.right`

## Modificadores de Teclas do Sistema

Você pode utilizar os seguintes modificadores para acionar eventos de _mouse_ ou teclado apenas quando o modificador correspondente estiver pressionado:

- `.ctrl`
- `.alt`
- `.shift`
- `.meta`

::: tip Nota
Nos teclados Macintosh, meta é a tecla de comando (⌘). Nos teclados Windows, meta é a tecla Windows (⊞). Nos teclados Sun Microsystems, meta é marcada como um diamante sólido (◆). Em alguns teclados, especificamente em máquinas MIT e Lisp e suas sucessoras, assim como o teclado Knight e teclados space-cadet, meta é marcada como “META”. Em teclados Symbolics, meta é marcada como “META” ou “Meta”.
:::

Por exemplo:

```html
<!-- Alt + Enter -->
<input @keyup.alt.enter="clear" />

<!-- Ctrl + Click -->
<div @click.ctrl="doSomething">Faça alguma coisa</div>
```

::: tip
Teclas modificadoras são diferentes de teclas comuns, e quando utilizadas com eventos `keyup`, precisam estar pressionadas quando o evento é emitido. Em outras palavras, `keyup.ctrl` só vai disparar se você soltar alguma tecla enquanto ainda estiver segurando `ctrl`. E não irá disparar se você soltar a tecla `ctrl` sozinha.
:::

### Modificador `.exact`

O modificador `.exact` permite controlar a exata combinação de modificadores de sistema que deve ser pressionada para que o gatilho dispare.

```html
<!-- dispara mesmo se Alt ou Shift também estiverem pressionados -->
<button @click.ctrl="onClick">A</button>

<!-- dispara somente quando Ctrl (e nenhuma outra tecla) for pressionado -->
<button @click.ctrl.exact="onCtrlClick">A</button>

<!-- dispara somente se não houverem teclas do sistema pressionadas -->
<button @click.exact="onClick">A</button>
```

### Modificadores dos Botões do Mouse

- `.left`
- `.right`
- `.middle`

Estes modificadores restringem o manipulador à eventos disparados por um botão específico do _mouse_.

## Por Que Escutas no HTML?

Você pode estar pensando que esta abordagem de escutas de evento viola as boas e velhas práticas sobre "separação de responsabilidades". Fique tranquilo - como todas as funções de manipuladores e expressões Vue são estritamente ligadas ao _ViewModel_ que está manipulando o modo de exibição atual, essa abordagem não causará qualquer dificuldade de manutenção. Na verdade, há vários benefícios em usar `v-on` ou `@`:

1. É mais fácil localizar as implementações de função de manipulador dentro de seu código JS percorrendo o _template_ HTML.

2. Como você não tem que manualmente anexar escutas à eventos em JS, seu código de _ViewModel_ pode conter apenas a lógica pura e ser livre de manipulação do DOM. Isto torna mais fácil de testar.

3. Quando um _ViewModel_ é destruído, todas as escutas de eventos são removidas automaticamente. Você não precisa se preocupar em removê-las explicitamente.
