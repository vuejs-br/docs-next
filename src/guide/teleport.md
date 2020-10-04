# Teleporte

<div class="vueschool"><a href="https://vueschool.io/lessons/vue-3-teleport?friend=vuejs" target="_blank" rel="sponsored noopener" title="Learn how to use teleport with Vue School">Aprenda como usar o teleporte em uma aula grátis do Vue School</a></div>

O Vue nos encoraja a construir nossas interfaces de usuário (UIs) encapsulando a UI e seu respectivo comportamento em componentes. Podemos aninhar componentes dentro de outros componentes e montar uma árvore que constitui a UI da aplicação.

Entretanto, algumas vezes, uma parte do _template_ do componente pertence a esse componente de forma lógica, enquanto do ponto de vista técnico, seria preferível mover essa parte do _template_ para outro lugar no DOM, fora da aplicação Vue.

Um cenário comum para isso é a criação de um componente que inclui um _modal_ em tela cheia. Na maioria dos casos, você gostaria que a lógica do modal residisse dentro do componente, mas o posicionamento do modal se torna difícil de resolver por meio de CSS ou requer uma mudança na composição do componente.

Considere a seguinte estrutura HTML.

```html
<body>
  <div style="position: relative;">
    <h3>Tooltips with Vue 3 Teleport</h3>
    <div>
      <modal-button></modal-button>
    </div>
  </div>
</body>
```

Vamos olhar o `modal-button` de perto.

O componente terá um elemento `button` para acionar a abertura do modal e um elemento `div` com uma classe `.modal` que irá conter o conteúdo do modal e um botão para fechá-lo.

```js
const app = Vue.createApp({});

app.component('modal-button', {
  template: `
    <button @click="modalOpen = true">
        Open full screen modal!
    </button>

    <div v-if="modalOpen" class="modal">
      <div>
        I'm a modal! 
        <button @click="modalOpen = false">
          Close
        </button>
      </div>
    </div>
  `,
  data() {
    return { 
      modalOpen: false
    }
  }
})
```

Ao usar esse componente dentro da estrutura HTML inicial, podemos ver um problema - o modal está sendo renderizado dentro de uma `div` profundamente aninhada e a `position: absolute` do modal toma como referência a `div` pai posicionada relativamente. 

O teleporte fornece uma maneira limpa para nos permitir controlar sob que elemento pai no DOM nós queremos que uma parte do HTML seja rederizada, sem ter que recorrer ao estado global ou dividí-lo em dois componentes.

Vamos modificar nosso `modal-button` para usar o `<teleport>` e dizer ao Vue "**teleporte** esse HTML **para** a _tag_ "**body**"". 

```js
app.component('modal-button', {
  template: `
    <button @click="modalOpen = true">
        Open full screen modal! (With teleport!)
    </button>

    <teleport to="body">
      <div v-if="modalOpen" class="modal">
        <div>
          I'm a teleported modal! 
          (My parent is "body")
          <button @click="modalOpen = false">
            Close
          </button>
        </div>
      </div>
    </teleport>
  `,
  data() {
    return { 
      modalOpen: false
    }
  }
})
```

Como resultado, uma vez que clicamos no botão para abrir o modal, o Vue irá renderizar corretamente o conteúdo como um filho da _tag_ `body`.

<p class="codepen" data-height="300" data-theme-id="39028" data-default-tab="js,result" data-user="Vue" data-slug-hash="gOPNvjR" data-preview="true" data-editable="true" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;" data-pen-title="Vue 3 Teleport">
  <span>See the Pen <a href="https://codepen.io/team/Vue/pen/gOPNvjR">
  Vue 3 Teleport</a> by Vue (<a href="https://codepen.io/Vue">@Vue</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

## Usando com componentes Vue

Se o `<teleport>` contém um componente Vue, o componente permanecerá um filho lógico do pai do `<teleport>`:

```js
const app = Vue.createApp({
  template: `
    <h1>Root instance</h1>
    <parent-component />
  `
})

app.component('parent-component', {
  template: `
    <h2>This is a parent component</h2>
    <teleport to="#endofbody">
      <child-component name="John" />
    </teleport>
  `
})

app.component('child-component', {
  props: ['name'],
  template: `
    <div>Hello, {{ name }}</div>
  `
})
```

Neste caso, mesmo quando o `child-component` é renderizado em um lugar diferente, permanecerá com um filho do `parent-component` e receberá a propriedade `name` dele.

Isso também significa que injeções vindas do componente pai funcionam como o esperado, e que o componente filho será aninhado embaixo do componente pai no _Vue Devtools_, em vez de ser colocado no lugar para o qual o conteúdo foi movido.

## Usando múltiplos teleportes no mesmo alvo

Um cenário de caso de uso comum pode ser um componente `<Modal>` reutilizável, do qual pode haver várias instâncias ativas ao mesmo tempo. Para esse tipo de cenário, múltiplos componentes `<teleport>` podem montar seus respectivos conteúdos no mesmo elemento alvo. A ordem será simplemente anexá-los - as montagens mais antigas serão localizadas depois das primeiras montagens realizadas no elemento alvo.

```html
<teleport to="#modals">
  <div>A</div>
</teleport>
<teleport to="#modals">
  <div>B</div>
</teleport>

<!-- result-->
<div id="modals">
  <div>A</div>
  <div>B</div>
</div>
```

Você pode checar as opções do componente `<teleport>` nas [referências da API](../api/built-in-components.html#teleport)
