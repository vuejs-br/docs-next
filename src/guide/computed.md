# Dados Computados e Observadores

## Dados Computados

> Originalmente, Propriedades Computadas _(Computed Properties)_. Entretanto, a adaptação "Dados Computados" foi eleita devido suas utilizações em _templates_ serem efetivamente similares às utilizações de `data`.

Expressões dentro de _templates_ são muito convenientes, mas são destinadas à realização de operações simples. Adicionar muita lógica em seus _templates_ podem torná-los abarrotados de código e dificultar a manutenção. Vamos tomar como exemplo o seguinte objeto, com um vetor _(array)_ incluso:

```js
Vue.createApp({
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Guia Avançado ',
          'Vue 3 - Guia Básico',
          'Vue 4 - O Mistério'
        ]
      }
    }
  }
})
```

Desejamos mostrar diferentes mensagens, ao verificarmos se `author` possuir livros _(books)_ ou não:

```html
<div id="computed-basics">
  <p>Possui livros publicados:</p>
  <span>{{ author.books.length > 0 ? 'Sim' : 'Não' }}</span>
</div>
```

Deste modo, o _template_ já não é mais simples e declarativo. Você sempre terá que observar este pedaço de código por um momento para, então, entender que ele realiza um cálculo baseando-se na quantidade de itens disponíveis em `author.books`. O problema se agravará ainda mais se você desejar incluir mais de uma vez este mesmo cálculo no seu _template_.

É por razões como estas — para lógicas complexas que dependem de dados reativos — que você deve utilizar **dados computados** _(computed properties)_.

### Exemplo Básico

```html
<div id="computed-basics">
  <p>Possui livros publicados:</p>
  <span>{{ publishedBooksMessage }}</span>
</div>
```

```js
Vue.createApp({
  data() {
    return {
      author: {
        name: 'John Doe',
        books: [
          'Vue 2 - Guia Avançado',
          'Vue 3 - Guia Básico',
          'Vue 4 - O Mistério'
        ]
      }
    }
  },
  computed: {
    // uma função "getter" computada (computed getter)
    publishedBooksMessage() {
      // `this` aponta para a instância Vue `vm` de `Vue.createApp(...)`
      return this.author.books.length > 0 ? 'Sim' : 'Não'
    }
  }
}).mount('#computed-basics')
```

Resultado:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="vuejs-br" data-slug-hash="vYGVjaO" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Exemplo básico de Dados Computados">
  <span>See the Pen <a href="https://codepen.io/vuejs-br/pen/vYGVjaO">
  Exemplo básico de Dados Computados</a> by Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Aqui declaramos um dado computado _(computed property)_ chamado `publishedBooksMessage`.

Tente remover os valores do vetor _(array)_ `books`, encontrado dentro de `data`, e você visualizará o valor de `publishedBooksMessage` sendo atualizado.

É possível vincular dados computados em _templates_ como qualquer outro tipo de dado. Vue tem ciência de que `vm.publishedBooksMessage` depende de `vm.author.books` para prover seu valor, portanto, ele irá atualizar qualquer vínculo de `vm.publishedBooksMessage` sempre que `vm.author.books` sofrer alterações. A melhor parte disto é que criamos tal relação de dependência de forma declarativa: a função _getter_ computada não possui quaisquer efeitos colaterais, tornando-se mais fácil de testar e entender.

### _Caching_ de `computed` vs. Métodos

Você pode ter notado que é possível obtermos o mesmo resultado ao chamarmos um método, como:

```html
<p>{{ calculateBooksMessage() }}</p>
```

```js
// no componente
methods: {
  calculateBooksMessage() {
    return this.author.books.length > 0 ? 'Yes' : 'No'
  }
}
```

Ao invés de utilizar dados computados, podemos obter os mesmos resultados ao simplesmente utilizar métodos. No entanto, a diferença é que **dados computados possuem _cache_ de acordo com suas dependências reativas**. Um dado computado, portanto, apenas será atualizado quando alguma de suas dependências sofrer alteração. Isto significa, por exemplo, que enquanto `author.books` não for alterado, o dado computado `publishedBooksMessage` irá retornar, imediatamente, o último resultado calculado, independente da quantidade de acessos que obter, sem ter de executar a função novamente.

Isto também significa que o dado computado a seguir nunca será atualizado, já que `Date.now()` não é reconhecido como uma dependência reativa:

```js
computed: {
  now() {
    return Date.now()
  }
}
```

Por outro lado, invocar um método **sempre** irá executar sua função quando ocorrer uma nova renderização.

Por que precisamos de _caching_? Imagine que temos um dado computado `list` extremamente custoso, que requer iterar por um extenso vetor _(array)_ e realizar cálculos com seus valores. Além disto, há outros dados computados que dependem de `list`. Sem realizarmos o _cache_, `list` seria executado muito mais vezes do que o necessário! Se você realmente não desejar realizar o _cache_ do valor calculado, utilize um método.

### Atribuição em Dados Computados

Dados computados _(computed properties)_ são, por padrão, _getter-only_ — ou seja, somente retornam valor. Entretanto, também é possível fornecer um _setter_ _(Computed Setter)_, se necessário:

```js
// ...
computed: {
  fullName: {
    // getter
    get() {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set(newValue) {
      const names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

Portanto, se executarmos `vm.fullName = 'John Doe'`, o _setter_ será executado e `vm.firstName` e `vm.lastName` serão atualizados, respectivamente.

## Observadores

Enquanto dados computados são mais adequados na maioria dos casos, há certos momentos em que um observador _(watcher)_ personalizado se faz necessário. É por isto que o Vue fornece uma maneira mais genérica de reagir à alteração de dados, `watch`, que se faz útil a partir do momento em que performamos operações assíncronas ou complexas quando certo dado sofre mudanças.

Por exemplo:

```html
<div id="watch-example">
  <p>
    Faça uma pergunta de sim ou não:
    <input v-model="question" />
  </p>
  <p>{{ answer }}</p>
</div>
```

```html
<!-- Como já existe coleções com métodos utilitários de     -->
<!-- propósitos gerais e um ecossistema rico de bibliotecas -->
<!-- Ajax, a base do Vue consegue se manter pequena por não -->
<!-- reinventar a roda e dar liberdade para que você possa  -->
<!-- utilizar o que ser mais familiar.                      -->
<script src="https://cdn.jsdelivr.net/npm/axios@0.12.0/dist/axios.min.js"></script>
<script>
  const watchExampleVM = Vue.createApp({
    data() {
      return {
        question: '',
        answer: 'Perguntas possuem pontos de interrogação ;-)'
      }
    },
    watch: {
      // sempre que `question` sofrer alterações, esta função será executada
      question(newQuestion, oldQuestion) {
        if (newQuestion.indexOf('?') > -1) {
          this.getAnswer()
        }
      }
    },
    methods: {
      getAnswer() {
        this.answer = 'Pensando...'
        axios
          .get('https://yesno.wtf/api')
          .then(response => {
            const responseAnswer = response.data.answer
            this.answer = responseAnswer === 'yes' ? 'Sim.' :
                responseAnswer === 'no' ? 'Não.' : 'Talvez?'
          })
          .catch(error => {
            this.answer = 'Erro! Não foi possível adquirir resultados da API: ' + error
          })
      }
    }
  }).mount('#watch-example')
</script>
```

Resultado:

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="result" data-user="vuejs-br" data-slug-hash="XWdxqYx" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Exemplo básico de Observadores">
  <span>See the Pen <a href="https://codepen.io/vuejs-br/pen/XWdxqYx">
  Exemplo básico de Observadores</a> by Vue.js Brasil (<a href="https://codepen.io/vuejs-br">@vuejs-br</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

Neste caso, ao usarmos `watch`, podemos executar uma operação assíncrona — como adquirir informações de uma _API_ — e, até mesmo, condições para executá-la. Isto não seria possível através da utilização de dados computados _(computed properties)_.

Além da opção `watch`, também é possível utilizar, de maneira imperativa, a [API vm.\$watch](../api/instance-methods.html#watch).

### Dados Computados vs Observadores

Devido ao Vue oferecer **observadores** _(watchers)_ como uma maneira mais genérica de observar e reagir à mudanças em dados de uma instância, é tentador utilizar, excessivamente, o `watch` — especialmente se você possui conhecimentos prévios em AngularJS, por exemplo. No entanto, às vezes, é melhor utilizar um dado computado ao invés de `watch`. Considere o seguinte exemplo:

```html
<div id="demo">{{ fullName }}</div>
```

```js
const vm = Vue.createApp({
  data() {
    return {
      firstName: 'Foo',
      lastName: 'Bar',
      fullName: 'Foo Bar'
    }
  },
  watch: {
    firstName(val) {
      this.fullName = val + ' ' + this.lastName
    },
    lastName(val) {
      this.fullName = this.firstName + ' ' + val
    }
  }
}).mount('#demo')
```

O código acima é imperativo e repetitivo. A seguir, uma versão do código utilizando dados computados, a fim de compará-los:

```js
const vm = Vue.createApp({
  data() {
    return {
      firstName: 'Foo',
      lastName: 'Bar'
    }
  },
  computed: {
    fullName() {
      return this.firstName + ' ' + this.lastName
    }
  }
}).mount('#demo')
```

Muito melhor, não é?
