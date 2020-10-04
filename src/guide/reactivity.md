# Aprofundando-se na Reatividade

Agora é o momento de mergulhar fundo! Uma das características mais distintas do Vue é o seu discreto sistema de reatividade. Models são _proxies_ de objetos JavaScript. Quando você os modifica, a _view_ é atualizada. Isso faz com que a administração de estado seja simples e intuitiva, porém é importante entender como isso funciona para evitar algumas pegadinhas. Nesta seção, vamos nos aprofundar em alguns dos detalhes de baixo nível do sistema de reatividade do Vue.

<VideoLesson href="https://www.vuemastery.com/courses/vue-3-reactivity/vue3-reactivity" title="Aprenda a fundo como a reatividade funciona com o Vue Mastery">Assista um vídeo gratuito sobre Aprofundando-se na Reatividade no Vue Mastery</VideoLesson>

## O Que é Reatividade?

Esse termo aparece na programação com uma certa frequência atualmente, mas o que as pessoas entendem por isso? Reatividade é um paradigma de programação que nos permite nos ajustar a mudanças de uma maneira declarativa. O exemplo canônico que as pessoas geralmente mostram, por ser um ótimo exemplo, é uma planilha do excel.

<video width="550" height="400" controls>
  <source src="/images/reactivity-spreadsheet.mp4" type="video/mp4">
  Seu navegador não possui suporte para a tag video.
</video>

Se você colocar o número dois na primeira célula, e o número 3 na segunda e então utilizar o SUM, a planilha te dará o resultado. Nenhuma surpresa aqui. Mas se você atualizar o primeiro número, o SUM é automagicamente atualizado.

O JavaScript, geralmente, não funciona assim -- Se escrevêssemos algo semelhante em Javascript:

```js
var val1 = 2
var val2 = 3
var sum = val1 + val2

// sum
// 5

val1 = 3

// sum
// 5
```

Se atualizarmos o primeiro valor, a soma não é ajustada.

Então como fazemos isso em JavaScript?

- Detecte quando há uma mudança em algum dos valores
- Rastreie a função que o modifica
- Dispare a função de modo que ela possa atualizar o valor final

## Como o Vue Rastreia Essas Mudanças

Quando você passa um objeto Javascript simples para uma aplicação ou instância de componente, como sua opção `data`, o Vue irá iterar sobre todas as suas propriedades e irá convertê-las em [Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) utilizando um _handler_ com getters e setters. Isso é uma _feature_ do ES6, somente, mas nos oferecemos uma versão do Vue 3 que utiliza o `Object.defineProperty` mais antigo, para dar suporte a navegadores IE. Ambas possuem a mesma API, na superfície, mas a versão com Proxy é mais elegante e oferece melhor performance.

<div class="reactivecontent">
  <iframe height="500" style="width: 100%;" scrolling="no" title="Proxies e a Reatividade do Vue Explicados Visualmente" src="https://codepen.io/sdras/embed/zYYzjBg?height=500&theme-id=light&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
    Veja o Pen <a href='https://codepen.io/sdras/pen/zYYzjBg'>Proxies e a Reatividade do Vue Explicados Visualmente</a> por Sarah Drasner
    (<a href='https://codepen.io/sdras'>@sdras</a>) em <a href='https://codepen.io'>CodePen</a>.
  </iframe>
</div>

A explicação anterior foi breve e requer algum conhecimento de [Proxies](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) para ser entendida! Então vamos nos aprofundar um pouco. Há muita literatura sobre Proxies, mas o que você realmente precisa saber é que um **Proxy é um objeto que encapsula um outro objeto ou função e permite que você o intercepte.**

Nós o utilizamos assim: `new Proxy(target, handler)`

```js
const dinner = {
  meal: 'tacos'
}

const handler = {
  get(target, prop) {
    return target[prop]
  }
}

const proxy = new Proxy(dinner, handler)
console.log(proxy.meal)

// tacos
```

Ok, até agora, estamos apenas encapsulando o objeto e o retornando. Legal, mas ainda não muito útil. Mas observe isso, nós também podemos interceptar este objetos enquanto temos ele encapsulado no Proxy. Essa interceptação é chamada de armadilha (_trap_).

```js
const dinner = {
  meal: 'tacos'
}

const handler = {
  get(target, prop) {
    console.log(‘intercepted!’)
    return target[prop]
  }
}

const proxy = new Proxy(dinner, handler)
console.log(proxy.meal)

// intercepted!
// tacos
```

Indo além de um _console log_, nós poderíamos fazer aqui qualquer coisa que desejássemos. Poderíamos até mesmo _não_ retornar o valor real se quiséssemos. Isso é o que torna os Proxies tão poderosos para a criação de APIs.

Além disso, há uma outra _feature_ que os Proxies nos oferecem. Ao invés de apenas retornar valores como esse: `target[prop]`, nós poderíamos levar isso um passo adiante e usar uma _feature_ chamada `Reflect`, que nos permite fazer a interligação apropriada do `this`. Isso leva a algo parecido com isso:

```js{7}
const dinner = {
  meal: 'tacos'
}

const handler = {
  get(target, prop, receiver) {
    return Reflect.get(...arguments)
  }
}

const proxy = new Proxy(dinner, handler)
console.log(proxy.meal)

// intercepted!
// tacos
```

Nós mencionamos antes que para ter uma API que atualiza o valor final quando algo é modificado, vamos precisar configurar novos valores quando alguma coisa muda. Nós fazemos isso no _handler_, em uma função chamada `track`, onde passamos os parâmetros `target` e `key`.

```js{7}
const dinner = {
  meal: 'tacos'
}

const handler = {
  get(target, prop, receiver) {
    track(target, prop)
    return Reflect.get(...arguments)
  }
}

const proxy = new Proxy(dinner, handler)
console.log(proxy.meal)

// intercepted!
// tacos
```

Finalmente, nós também configuramos novos valores quando alguma coisa muda. Para isso, vamos configurar as mudanças no nosso novo Proxy, disparando essas mudanças:

```js
const dinner = {
  meal: 'tacos'
}

const handler = {
  get(target, prop, receiver) {
    track(target, prop)
    return Reflect.get(...arguments)
  },
  set(target, key, value, receiver) {
    trigger(target, key)
    return Reflect.set(...arguments)
  }
}

const proxy = new Proxy(dinner, handler)
console.log(proxy.meal)

// intercepted!
// tacos
```

Lembra-se desta lista de alguns parágrafos atrás? Agora nós temos algumas respostas sobre como o Vue manipula essas mudanças:

- <strike>Detecte quando há uma mudança em algum dos valores</strike>: não precisamos mais fazer isso, uma vez que os Proxies nos permitem interceptar essas mudanças
- **Rastreie a função que o modifica**: Nós fazemos isso em um getter dentro do proxy, chamado `effect`
- **Dispare a função de modo que ela possa atualizar o valor final**: Nós fazemos isso em um setter dentro do proxy, chamado `trigger`

O objeto com o _proxy_ aplicado é invisível para o usuário, mas por baixo dos panos ele possibilita que o Vue faça o rastreamento-de-dependência (_dependency-tracking_) e a notificação-de-mudança (_change-notification_) quando propriedades são acessadas ou modificadas. A partir do Vue 3, nossa reatividade está agora disponível em um [pacote separado](https://github.com/vuejs/vue-next/tree/master/packages/reactivity). Um problema é que o _console_ de navegadores formatam diferentemente quando objetos de dados convertidos são registrados no _log_, então pode ser que você queria instalar o [vue-devtools](https://github.com/vuejs/vue-devtools) para uma interface mais amigável à inspeção.

### Objetos com o Proxy aplicado

O Vue rastreia internamente todos os objetos que foram transformados em reativos, então ele sempre retorna o mesmo proxy para o mesmo objeto.

Quando um objeto aninhado é acessado através de um proxy reativo, esse objeto é _também_ convertido em um proxy antes de ser retornado:

```js
const handler = {
  get(target, prop, receiver) {
    track(target, prop)
    const value = Reflect.get(...arguments)
    if (isObject(value)) {
      return reactive(value)
    } else {
      return value
    }
  }
  // ...
}
```

### Proxy vs. identidade original

O uso do Proxy de fato introduz um novo empecilho a ser considerado: o objeto com o proxy aplicado não é igual ao objeto original em termos de comparação de identidade (`===`). Por exemplo:

```js
const obj = {}
const wrapped = new Proxy(obj, handlers)

console.log(obj === wrapped) // false
```

A versão original e a versão encapsulada se comportarão da mesma forma na maioria dos casos, mas esteja ciente de que elas irão falhar em operações que dependem de comparações fortes de identidade, como `.filter()` ou `.map()`. Esse empecilho tem pouca chance de ocorrer quando da utilização a API de opções, porque todo o estado reativo é acessado a partir do `this` e é garantido que já sejam _proxies_.

Entretanto, quanto utilizando a API de composição para criar objetos reativos explicitamente, a melhor prática é a de nunca guardar uma referência para o objeto cru original e trabalhar somente com a versão reativa:

```js
const obj = reactive({
  count: 0
}) // nenhuma referência ao original
```

## Observadores

Toda instância de componente tem uma instância de observador correspondente, que registra quaisquer propriedades "tocadas" durante a renderização do componente como dependências. Depois, quando um setter de uma dependência é disparado, ele notifica o observador, que por sua vez faz o componente re-renderizar.

<div class="reactivecontent">
  <iframe height="500" style="width: 100%;" scrolling="no" title="Explicação da Segunda Reatividade com Proxies no Vue 3" src="https://codepen.io/sdras/embed/GRJZddR?height=500&theme-id=light&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
    See the Pen <a href='https://codepen.io/sdras/pen/GRJZddR'>Explicação da Segunda Reatividade com Proxies no Vue 3</a> por Sarah Drasner
    (<a href='https://codepen.io/sdras'>@sdras</a>) no <a href='https://codepen.io'>CodePen</a>.
  </iframe>
</div>

Quando você passa um objeto para uma instância de um componente como _data_, o Vue o converte em um proxy. Esse proxy permite ao Vue realizar o rastreamento-de-dependência (_dependency-tracking_) e a notificação-de-mudança (_change-notification_), quando as propriedades são acessadas ou modificadas. Cada propriedade é considerada uma dependência.

Após a primeira renderização, um componente teria rastreado a lista de dependências &mdash; as propriedades que acessou durante a renderização. Inversamente, o componente se torna um assinante de cada uma dessas propriedades. Quando um proxy intercepta uma operação _set_, a propriedade notificará cada um de seus componentes assinantes para re-renderizarem.

[//]: # 'TODO: Diagrama de Insert'

> Se você está utilizando Vue 2.x e abaixo, pode estar interessado em alguns empecilhos de detecção de mudanças que existem nessas versões, [explorados em mais detalhes aqui](change-detection.md).
