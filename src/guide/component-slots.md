# _Slots_

> Esta página pressupõe que você já leu o [Básico sobre Componentes](component-basics.md). Se você não está familizariado com componentes, recomendamos lê-lo primeiro.

## Conteúdo do _Slot_

Vue implementa uma API de distribuição de conteúdo inspirada em [um dos rascunhos de especificação de _Web Components_](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md), utilizando o elemento `<slot>` como saída de distribuição de conteúdo.

Isto permite a criação de componentes como o seguinte exemplo:

```html
<todo-button>
  Adicionar afazer
</todo-button>
```

E, no _template_ de `<todo-button>`, possuímos:

```html
<!-- template do componente todo-button -->
<button class="btn-primary">
  <slot></slot>
</button>
```

Quando o componente renderizar, `<slot></slot>` será substituído por "Adicionar afazer".

```html
<!-- HTML renderizado -->
<button class="btn-primary">
  Adicionar afazer
</button>
```

_Strings_ são apenas um exemplo simples! _Slots_ podem servir vários tipos de conteúdo também, incluindo HTML:

```html
<todo-button>
  <!-- Adiciona um ícone do Font Awesome -->
  <i class="fas fa-plus"></i>
  Adicionar afazer
</todo-button>
```

Ou, até mesmo, outros componentes:

```html
<todo-button>
  <!-- Utilizando um componente para adicionar um ícone -->
  <font-awesome-icon name="plus"></font-awesome-icon>
  Seu perfil
</todo-button>
```

Se o _template_ de `<todo-button>` **não** possuísse o elemento `<slot>`, qualquer conteúdo fornecido seria descartado.

```html
<!-- template do componente todo-button, agora sem <slot></slot>  -->
<button class="btn-primary">
  Criar um novo item
</button>
```

```html
<todo-button>
  <!-- O texto a seguir não será renderizado -->
  Adicionar afazer
</todo-button>
```

## Escopo de Compilação

Quando você desejar utilizar dados dentro de um _slot_, como em:

```html
<todo-button>
  Remover {{ item.name }}
</todo-button>
```

Este _slot_ possui acesso às propriedades de sua instância (isto é, ao "escopo" de sua instância), assim como o resto do _template_.

<img src="/images/slot.png" width="447" height="auto" style="display: block; margin: 0 auto; max-width: 100%" loading="lazy" alt="Diagrama para explicar o Escopo de Compilação">

Entretanto, o _slot_ *não* possui acesso ao escopo de `<todo-button>`. Por exemplo, se tentarmos adquirir o valor de `action`, não será possível:

```html
<todo-button action="delete">
  Ao clicar aqui, este item realizará a ação {{ action }}
  <!--
  `action` retornará `undefined` neste caso, já que
  o conteúdo de `action` é apenas _transmitido_ para `<todo-button>`,
  ao invés de ser _definido_ como um dado de `<todo-button>`.
  -->
</todo-button>
```

Lembre-se, como regra, de que:

> Tudo do _template_ pai _(parent template)_ é compilado no escopo pai _(parent scope)_; tudo do _template_ filho _(child template)_, é compilado no escopo filho _(child scope)_.

## Conteúdo padrão


Há certos casos onde é interessante definir um conteúdo padrão (ou de _fallback_) para um _slot_ — ou seja, conteúdo que será apenas renderizado caso nenhum outro for informado. Por exemplo, no componente `<submit-button>`:

```html
<button type="submit">
  <slot></slot>
</button>
```

Podemos querer mostrar "Enviar" dentro do `<button>` quando nenhum outro texto for informado. Para isto, basta apenas colocar "Enviar" entre as _tags_ `<slot>` para defini-lo como o conteúdo padrão.

```html
<button type="submit">
  <slot>Enviar</slot>
</button>
```

Agora, ao utilizarmos o componente `<submit-button>`, sem informar um conteúdo...

```html
<submit-button></submit-button>
```

"Enviar" será renderizado, por ser o conteúdo padrão:

```html
<button type="submit">
  Enviar
</button>
```

Entretanto, se especificarmos um conteúdo...

```html
<submit-button>
  Salvar
</submit-button>
```

O conteúdo informado que será renderizado no lugar de "Enviar":

```html
<button type="submit">
  Salvar
</button>
```

## _Slots_ nomeados

Há casos onde é interessante utilizar vários slots. Por exemplo, em um componente `<base-layout>`, com o seguinte _template_:

```html
<div class="container">
  <header>
    <!-- Queremos nosso conteúdo para o cabeçalho aqui -->
  </header>
  <main>
    <!-- Queremos nosso conteúdo principal aqui -->
  </main>
  <footer>
    <!-- Queremos nosso conteúdo para o rodapé aqui -->
  </footer>
</div>
```

Para estes casos, o elemento `<slot>` possui um atributo especial, `name`, que pode ser utilizado para designar uma identificação única a cada `<slot>` e, assim, determinar onde certos conteúdos devem ser renderizados:

```html
<div class="container">
  <header>
    <slot name="header"></slot>
  </header>
  <main>
    <slot></slot>
  </main>
  <footer>
    <slot name="footer"></slot>
  </footer>
</div>
```

Um elemento `<slot>` sem o atributo `name`, implicitamente, possui `name="default"`.

Para fornecer conteúdo a um _slot_ nomeado, precisamos utilizar a diretiva `v-slot` em um elemento `<template>`, indicando o nome do _slot_ como argumento, logo em seguida:

```html
<base-layout>
  <template v-slot:header>
    <h1>Aqui podemos ter um título para a página.</h1>
  </template>

  <template v-slot:default>
    <p>Um parágrafo para o conteúdo principal.</p>
    <p>E aqui mais um.</p>
  </template>

  <template v-slot:footer>
    <p>Aqui temos informações para contato.</p>
  </template>
</base-layout>
```

A partir deste momento, o conteúdo dos elementos `<template>` será transferido para os _slots_ correspondentes.

O HTML renderizado será:

```html
<div class="container">
  <header>
    <h1>Aqui podemos ter um título para a página.</h1>
  </header>
  <main>
    <p>Um parágrafo para o conteúdo principal.</p>
    <p>E aqui mais um.</p>
  </main>
  <footer>
    <p>Aqui temos informações para contato.</p>
  </footer>
</div>
```

É importante destacar que **`v-slot` pode ser adicionado apenas a um `<template>`** ([exceto neste caso](#sintaxe-abreviada-para-slot-unico-e-default)).

## Definição de Escopo em _Slots_

Em certos momentos, é interessante acessar dados apenas disponíveis no componente filho _(child component)_ no conteúdo de um _slot_. Um caso muito comum é quando utilizamos um componente específico para renderizarmos cada item de um vetor _(array)_ ou, simplesmente, para personalizarmos como cada item é renderizado.

Por exemplo, em um componente que possui uma lista de afazeres:

```js
app.component('todo-list', {
  data() {
    return {
      items: ['Alimentar o gato', 'Comprar leite']
    }
  },
  template: `
    <ul>
      <li v-for="(item, index) in items">
        {{ item }}
      </li>
    </ul>
  `
})
```

Podemos desejar personalizar o _slot_ diretamente no componente pai _(parent component)_:

```html
<todo-list>
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
</todo-list>
```

Entretanto, isto não funcionará por estarmos definindo o conteúdo do _slot_ no componente pai, e apenas o componente `<todo-list>` possuir acesso ao dado `item`.

Para fazer com que `item` esteja disponível para uso no conteúdo do _slot_ definido no componente pai, podemos passá-lo como um atributo de `<slot>`, através de `v-bind`:

```html
<ul>
  <li v-for="( item, index ) in items">
    <slot v-bind:item="item"></slot>
  </li>
</ul>
```

Atributos passados para um elemento `<slot>` são chamados de **_props_ do _slot_** _(slot props)_. Agora, no escopo do componente pai _(parent scope)_, podemos utilizar `v-slot` com um valor, que será utilizado, neste caso, como o nome de acesso a estes _props_ do _slot_:

```html
<todo-list>
  <template v-slot:default="slotProps">
    <i class="fas fa-check"></i>
    <span class="green">{{ slotProps.item }}</span>
  </template>
</todo-list>
```

<img src="/images/scoped-slot.png" width="611" height="auto" style="display: block; margin: 0 auto; max-width: 100%;" loading="lazy" alt="Diagrama para explicar a Definição de Escopo em Slots">

Neste exemplo, escolhemos `slotProps` como valor, a fim de tê-lo como o nome de acesso aos _props_ do _slot_. É possível utilizar qualquer nome que desejar.

### Sintaxe abreviada para Slot Único e _Default_

Em casos como o acima, quando _apenas_ o _slot_ padrão _(default)_ é fornecido, podemos utilizar o próprio conteúdo do componente como o _template_ do _slot_. Deste modo, utilizamos `v-slot` diretamente no componente:

```html
<todo-list v-slot:default="slotProps">
  <i class="fas fa-check"></i>
  <span class="green">{{ slotProps.item }}</span>
</todo-list>
```

Podemos deixar isto ainda mais simples, já que, assim como não especificar um `name` para um elemento `<slot>` o faz possuir `name="default"`, não especificar um argumento para um `v-slot` o faz referenciar `default`, implicitamente:

```html
<todo-list v-slot="slotProps">
  <i class="fas fa-check"></i>
  <span class="green">{{ slotProps.item }}</span>
</todo-list>
```

É importante mencionar que **não é possível** utilizar esta sintaxe abreviada para _slot_ padrão em conjunto de _slots_ nomeados, já que isto resultaria em ambiguidade de escopo _(scope ambiguity)_:

```html
<!-- INVÁLIDO, resultará em um aviso -->
<todo-list v-slot="slotProps">
  <todo-list v-slot:default="slotProps">
    <i class="fas fa-check"></i>
    <span class="green">{{ slotProps.item }}</span>
  </todo-list>
  <template v-slot:other="otherSlotProps">
    slotProps NÃO está disponível aqui
  </template>
</todo-list>
```

Sempre que houver mais de um _slot_, utilize da sintaxe completa, com `<template>`, para _todos_ os _slots_:

```html
<todo-list>
  <template v-slot:default="slotProps">
    <i class="fas fa-check"></i>
    <span class="green">{{ slotProps.item }}</span>
  </template>

  <template v-slot:other="otherSlotProps">
    ...
  </template>
</current-user>
```

### Desestruturando _Props_ em um _Slot_

Internamente, _slots_ com escopo definido encapsulam o conteúdo do seu respectivo _slot_ em uma função com um único argumento:

```js
function (slotProps) {
  // ... conteúdo do slot ...
}
```

Isto significa que o valor passado a `v-slot` pode aceitar, na verdade, qualquer expressão JavaScript válida de definição de argumentos de uma função. Portanto, você também pode utilizar a [desestruturação de objetos, da ES2015](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring), a fim de trabalhar apenas com os _props_ que você desejar no conteúdo do _slot_, como no exemplo abaixo:

```html
<todo-list v-slot="{ item }">
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
</todo-list>
```

Isto pode tornar o _template_ muito mais limpo, especialmente quando o _slot_ fornece vários _props_. Isto também abre portas para outras possibilidades, como renomear um _prop_ de nome `item` para `todo`:

```html
<todo-list v-slot="{ item: todo }">
  <i class="fas fa-check"></i>
  <span class="green">{{ todo }}</span>
</todo-list>
```

Você pode até definir valores padrão _(fallbacks)_, a fim de serem utilizados quando um _prop_ do _slot_ não possuir um valor definido _(undefined)_:

```html
<todo-list v-slot="{ item = 'Isto é um afazer' }">
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
</todo-list>
```

## Nomes Dinâmicos para _Slots_

[Argumentos dinâmicos de diretiva](template-syntax.md#argumentos-dinamicos) também funcionam com `v-slot`, permitindo a definição de nomes dinâmicos para _slots_:

```html
<base-layout>
  <template v-slot:[dynamicSlotName]>
    ...
  </template>
</base-layout>
```

## Forma Abreviada para _Slots_ Nomeados

Assim como em `v-on` e `v-bind`, `v-slot` também possui uma forma abreviada, basta substituirmos `v-slot:` (ou seja, tudo antes do argumento) pelo símbolo especial `#`. Portanto, `v-slot:header` também pode ser escrito como `#header`, por exemplo.

```html
<base-layout>
  <template #header>
    <h1>Aqui podemos ter um título para a página.</h1>
  </template>

  <template #default>
    <p>Um parágrafo para o conteúdo principal.</p>
    <p>E aqui mais um.</p>
  </template>

  <template #footer>
    <p>Aqui temos informações para contato.</p>
  </template>
</base-layout>
```

Entretanto, assim como em qualquer outra diretiva, a forma abreviada está apenas disponível quando esta recebe um argumento. Isto significa que o exemplo a seguir possui sintaxe inválida:

```html
<!-- Isto acionará um aviso -->

<todo-list #="{ item }">
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
</todo-list>
```

Deste modo, você sempre precisa especificar o nome do _slot_ que você deseja definir ao utilizar a forma abreviada, como em:

```html
<todo-list #default="{ item }">
  <i class="fas fa-check"></i>
  <span class="green">{{ item }}</span>
</todo-list>
```
