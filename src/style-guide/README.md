---
sidebar: auto
---

# Guia de Estilo

Este é o guia de estilo oficial para código específico do Vue. Se você usa Vue em um projeto, esta é uma ótima referência para evitar erros, trivialidades e antipadrões. Entretanto, não acreditamos que exista um guia de estilo ideal para todos os times ou projetos, então variações conscientes são incentivadas com base em experiências passadas, tecnologias do ambiente, e valores pessoais.

Para a maior parte, evitamos também sugestões sobre JavaScript ou HTML em geral. Não ligamos se você usa ponto e vírgula ou vírgulas. Não ligamos se seu HTML usa aspas simples ou aspas duplas para valores de atributos. Algumas exceções existem, entretanto, onde achamos que um padrão em particular é útil no contexto do Vue.

Finalmente, separamos as regras em quatro categorias:

## Categorias

### Prioridade A: Essencial

Estas regras ajudam a prevenir erros, então aprenda e siga-as a qualquer custo. Exceções podem existir, mas devem ser muito raras e feitas somente por aqueles com conhecimento especializado em JavaScript e Vue. 

### Prioridade B: Fortemente Recomendado

Estas regras foram verificadas para melhorar a legibilidade e/ou a experiência do desenvolvedor na maioria dos projetos. Seu código ainda funcionará se você violá-las, mas as violações devem ser raras e bem justificadas.

### Prioridade C: Recomendado

Onde opções múltiplas e igualmente boas existem, uma escolha arbitrária pode ser feita para garantir consistência. Nestas regras, descrevemos cada opção aceitável e sugerimos uma escolha padrão. Isso significa que você pode ficar à vontade para realizar uma escolha diferente em seu próprio código, desde que você seja consistente e tenha um bom motivo. Mas por favor, tenha um bom motivo! Ao adotar o padrão da comunidade, você irá:

1. treinar seu cérebro para analisar a maior parte do código da comunidade que encontrar
2. ser capaz de copiar e colar a maior parte dos exemplos de código da comunidade sem modificações
3. frequentemente encontrar novos parceiros que já estão acostumados ao seu estilo de código preferido, ao menos no âmbito Vue.

### Prioridade D: Use com Cautela

Algumas funcionalidades do Vue existem para lidar com casos extremos ou migrações mais suaves de um código legado. Quando usadas em excesso, entretanto, podem tornar o seu código mais difícil de manter ou até mesmo tornar o seu código em um reduto de bugs. Estas regras esclarecem funcionalidades potencialmente arriscadas, descrevendo quando e porque elas devem ser evitadas.

## Regras Prioridade A: Essencial <span class="hide-from-sidebar">(Prevenção de Erros)</span>

### Nomes de componente multipalavras <sup data-p="a">essencial</sup>

**Nomes de componente sempre devem ser multipalavras, exceto para componentes raiz `App`, e componentes internos fornecidos pelo Vue, como `<transition>`, ou `<component>`.**

Isto [previne conflitos](http://w3c.github.io/webcomponents/spec/custom/#valid-custom-element-name) com elementos HTML existentes e futuros, visto que todos os elementos HTML são formados por apenas uma palavra.

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` js
app.component('todo', {
  // ...
})
```

``` js
export default {
  name: 'Todo',
  // ...
}
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` js
app.component('todo-item', {
  // ...
})
```

``` js
export default {
  name: 'TodoItem',
  // ...
}
```
</div>

### Definições de propriedades <sup data-p="a">essencial</sup>

**Definições de propriedades devem ser as mais detalhadas possíveis.**

No código desenvolvido, definições de propriedades devem ser tão detalhadas quanto possível, especificando ao menos os seus tipos.

::: details Explicação Detalhada
[Definições de propriedades](https://vuejs.org/v2/guide/components.html#Prop-Validation) detalhadas possuem duas vantagens:

- Elas documentam a API do componente, para que seja fácil ver como o componente deve ser usado.
- No desenvolvimento, o Vue irá lhe avisar se um componente receber propriedades formatadas incorretamente, ajudando-o a capturar potenciais causas de erro.
:::

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` js
// Isto é aceitável apenas ao prototipar
props: ['status']
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` js
props: {
  status: String
}
```

``` js
// Ainda melhor!
props: {
  status: {
    type: String,
    required: true,

    validator: value => {
      return [
        'syncing',
        'synced',
        'version-conflict',
        'error'
      ].includes(value)
    }
  }
}
```
</div>

### Chaves de identificação no `v-for` <sup data-p="a">essencial</sup>

**Sempre use `key` com `v-for`.**

`key` com `v-for` é _sempre_ exigido em componentes, de forma a manter o estado do componente interno até a subárvore. E mesmo para elementos, é uma boa prática manter o comportamento previsível, como a [constância de objetos](https://bost.ocks.org/mike/constancy/) em animações.

::: details Explicação Detalhada
Digamos que você tenha uma lista de tarefas:

``` js
data() {
  return {
    todos: [
      {
        id: 1,
        text: 'Aprender a usar v-for'
      },
      {
        id: 2,
        text: 'Aprender a usar key'
      }
    ]
  }
}
```

E então você as separa alfabeticamente. Ao atualizar o DOM, o Vue irá aperfeiçoar a renderização para desempenhar o menor número possível de mutações no DOM. Isto pode significar apagar o primeiro elemento de tarefa, e então adicioná-lo novamente ao final da lista.

O problema é que há casos em que é importante não apagar qualquer elemento que permanecerá no DOM. Por exemplo, você pode querer usar `<transition-group>` para animar a ordenação da lista, ou manter o foco se o elemento renderizado é um `<input>`. Nestes casos, adicionar uma chave única para cada item (ex.: `:key="todo.id"`) irá dizer ao Vue como se comportar de forma mais previsível.

Em nossa experiência, o melhor é _sempre_ adicionar uma chave única, para que você e seu time nunca precisem se preocupar com esses casos extremos. Então, em raros cenários com questões críticas de desempenho onde a constância de objetos não é necessária, você pode fazer uma exceção de forma consciente.
:::

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` html
<ul>
  <li v-for="todo in todos">
    {{ todo.text }}
  </li>
</ul>
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` html
<ul>
  <li
    v-for="todo in todos"
    :key="todo.id"
  >
    {{ todo.text }}
  </li>
</ul>
```
</div>

### Evitar `v-if` com `v-for` <sup data-p="a">essencial</sup>

**Nunca use `v-if` com `v-for` no mesmo elemento.**

Geralmente existem dois casos onde isso pode ser tentador:

- Para filtrar itens em uma lista (ex.: `v-for="user in users" v-if="user.isActive"`). Nestes casos, substitua `users` com um novo dado computado que retorne a sua lista filtrada (ex.: `activeUsers`).

- Para evitar renderizar uma lista se ela deverá ser escondida (ex.: `v-for="user in users" v-if="shouldShowUsers"`). Nestes casos, mova o `v-if` para o elemento pai. (ex.: `ul`, `ol`).

::: details Explicação Detalhada
Quando o Vue processa diretrizes, o `v-if` tem uma prioridade maior do que o `v-for`, então para este template:

``` html
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

Acontecerá um erro, porque a diretriz `v-if` deve ser examinada primeiro e a variável de iteração `user` não existe neste momento.

Isto pode ser arrumado ao iterar sobre um dado computado, assim:

``` js
computed: {
  activeUsers() {
    return this.users.filter(user => user.isActive)
  }
}
```

``` html
<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

Alternativamente, podemos usar uma tag `<template>` com `v-for` para envolver o elemento `<li>`:

```html
<ul>
  <template v-for="user in users" :key="user.id">
    <li v-if="user.isActive">
      {{ user.name }}
    </li>
  </template>
</ul>
```

:::

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` html
<ul>
  <li
    v-for="user in users"
    v-if="user.isActive"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` html
<ul>
  <li
    v-for="user in activeUsers"
    :key="user.id"
  >
    {{ user.name }}
  </li>
</ul>
```

```html
<ul>
  <template v-for="user in users" :key="user.id">
    <li v-if="user.isActive">
      {{ user.name }}
    </li>
  </template>
</ul>
```
</div>

### Estilos de componente em escopo <sup data-p="a">essencial</sup>

**Para aplicações, estilos em um componente de nível maior `App` e em componentes de layout podem ser globais, mas todos os outros componentes devem utilizar estilo em escopo.**

Isto é relevante apenas para [componentes single file](../guide/single-file-component.html). _Não_ é exigido que o [atributo `scoped`](https://vue-loader.vuejs.org/en/features/scoped-css.html) seja usado. O escopo pode ser realizado através de [módulos CSS](https://vue-loader.vuejs.org/en/features/css-modules.html), uma estratégia baseada em classes como [BEM](http://getbem.com/), ou outra biblioteca/convenção.

**Bibliotecas de componente, entretanto, devem preferir uma estratégia baseada em classes ao invés de usar o atributo `scoped`.**

Isto torna mais fácil a sobreposição de estilos internos, com nomes de classes de fácil leitura que não possuem especificidade muito alta, e que são muito improváveis de resultarem em conflito.

::: details Explicação Detalhada
Se você está desenvolvendo um projeto grande, trabalhando com outros desenvolvedores, ou às vezes incluindo algum HTML/CSS de terceiros (ex.: do Auth0), um escopo consistente irá garantir que seus estilos se apliquem somente aos componentes para que foram designados.

Além do atributo `scoped`, usar nomes de classe únicos podem ajudar a garantir que o CSS de terceiros não se apliquem em seu próprio HTML. Por exemplo, muitos projetos usam nomes de classe `button`, `btn`, ou `icon`, então mesmo ao não usar uma estratégia como o BEM, adicionar um prefixo específico do app e/ou específico do componente (ex.: `ButtonClose-Icon`) pode fornecer alguma proteção.

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` html
<template>
  <button class="btn btn-close">×</button>
</template>

<style>
.btn-close {
  background-color: red;
}
</style>
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` html
<template>
  <button class="button button-close">×</button>
</template>

<!-- Usando o atributo `scoped` -->
<style scoped>
.button {
  border: none;
  border-radius: 2px;
}

.button-close {
  background-color: red;
}
</style>
```

``` html
<template>
  <button :class="[$style.button, $style.buttonClose]">×</button>
</template>

<!-- Usando módulos CSS -->
<style module>
.button {
  border: none;
  border-radius: 2px;
}

.buttonClose {
  background-color: red;
}
</style>
```

``` html
<template>
  <button class="c-Button c-Button--close">×</button>
</template>

<!-- Usando a convenção BEM -->
<style>
.c-Button {
  border: none;
  border-radius: 2px;
}

.c-Button--close {
  background-color: red;
}
</style>
```
</div>

### Nomes de propriedades privadas <sup data-p="a">essencial</sup>

**Use o módulo de escopo para manter funções privadas inacessíveis do exterior. Se isto não for possível, sempre use o prefixo `$_` para propriedades privadas customizadas em um plugin, mixin, e afim, que não deva ser considerado como uma API pública. Para evitar conflitos com o código de outros autores, também inclua um nome de escopo (ex.: `$_nomeDoSeuPlugin_`).**

::: details Explicação Detalhada
Vue usa o prefixo `_` para definir suas próprias propriedades privadas, então usar o mesmo prefixo (ex.: `_update`) pode sobrescrever uma propriedade da instância. Mesmo que você tenha conferido e que o Vue não esteja atualmente usando um nome de propriedade privada, não há garantias de que um conflito não acontecerá em uma versão posterior.

O propósito do prefixo `$` dentro do ecossistema Vue é uma instância especial de propriedades que estão expostas ao usuário, então usá-las em propriedades _privadas_ não seria apropriado.

Ao invés disso, recomendamos combinar os dois prefixos no `$_`, como convenção para propriedades privadas definidas pelo usuário que garantem que não haja conflitos com o Vue.
:::

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` js
const myGreatMixin = {
  // ...
  methods: {
    update() {
      // ...
    }
  }
}
```

``` js
const myGreatMixin = {
  // ...
  methods: {
    _update() {
      // ...
    }
  }
}
```

``` js
const myGreatMixin = {
  // ...
  methods: {
    $update() {
      // ...
    }
  }
}
```

``` js
const myGreatMixin = {
  // ...
  methods: {
    $_update() {
      // ...
    }
  }
}
```

</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` js
const myGreatMixin = {
  // ...
  methods: {
    $_myGreatMixin_update() {
      // ...
    }
  }
}
```

``` js
// Ainda melhor!
const myGreatMixin = {
  // ...
  methods: {
    publicMethod() {
      // ...
      myPrivateFunction()
    }
  }
}

function myPrivateFunction() {
  // ...
}

export default myGreatMixin
```
</div>

## Regras da Prioridade B: Fortemente Recomendadas <span class="hide-from-sidebar">(Aprimoram Legibilidade)</span>

### Arquivos de componente <sup data-p="b">fortemente recomendado</sup>

**Quando houver um sistema compilado disponível para concatenar arquivos, cada componente deve estar em seu próprio arquivo.**

Isto ajuda você a encontrar mais rapidamente um componente quando precisar editá-lo ou verificar como usá-lo.

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` js
app.component('TodoList', {
  // ...
})

app.component('TodoItem', {
  // ...
})
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

```
components/
|- TodoList.js
|- TodoItem.js
```

```
components/
|- TodoList.vue
|- TodoItem.vue
```
</div>

### Notação de nomes de Componentes Single File <sup data-p="b">fortemente recomendado</sup>

**Nomes de arquivo de [componentes single file](../guide/single-file-component.html) devem ser sempre PascalCase ou kebab-case.**

A notação com PascalCase funciona melhor com o preenchimento automático de editores de código, pois é consistente com a forma que referenciamos componentes em JS(X) e templates, onde possível. Entretanto, diferentes tipos de nomes de arquivo podem às vezes causar problemas em sistemas de arquivo insensíveis a maiúsculas e minúsculas, é a razão de o kebab-case ser perfeitamente aceitável.

<div class="style-example style-example-bad">
<h4>Ruim</h4>

```
components/
|- mycomponent.vue
```

```
components/
|- myComponent.vue
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

```
components/
|- MyComponent.vue
```

```
components/
|- my-component.vue
```
</div>

### Nomes de componentes base <sup data-p="b">fortemente recomendado</sup>

**Componentes base (também conhecidos como componentes de apresentação, burros, ou puros) que aplicam estilos e convenções específicos da aplicação devem começar com um prefixo específico, como `Base`, `App` ou `V`.**

::: details Explicação Detalhada
Estes componentes estabelecem a base para o estilo e o comportamento consistente na sua aplicação. Eles podem conter **somente**:

- Elementos HTML,
- outros componentes base, e
- componentes UI de terceiros.

Mas eles **nunca** irão conter um estado global (ex.: de um Vuex store).

Seus nomes frequentemente incluem o nome do elemento que eles envolvem (ex.: `BaseButton`, `BaseTable`), a não ser que nenhum elemento exista para seu propósito específico (ex.: `BaseIcon`). Se você construir componentes similares para um contexto mais específico, eles quase sempre consumirão estes componentes (ex.: `BaseButton` será usado em `ButtonSubmit`).

Algumas vantagens desta convenção:

- Quando organizado alfabeticamente em editores, os componentes base da aplicação serão listados em conjunto, tornando-os mais fáceis de identificar.

- Como nomes de componente sempre possuem multipalavras, esta convenção previne que você tenha que escolher um prefixo arbitrário para simples componentes envoltórios (ex.: `MyButton`, `VueButton`).

- Como estes componentes são frequentemente usados, você pode simplesmente torná-los globais ao invés de importá-los em todos os lugares. Um prefixo torna isto possível com o Webpack:

  ``` js
  const requireComponent = require.context("./src", true, /Base[A-Z]\w+\.(vue|js)$/)
  requireComponent.keys().forEach(function (fileName) {
    let baseComponentConfig = requireComponent(fileName)
    baseComponentConfig = baseComponentConfig.default || baseComponentConfig
    const baseComponentName = baseComponentConfig.name || (
      fileName
        .replace(/^.+\//, '')
        .replace(/\.\w+$/, '')
    )
    app.component(baseComponentName, baseComponentConfig)
  })
  ```
:::

<div class="style-example style-example-bad">
<h4>Ruim</h4>

```
components/
|- MyButton.vue
|- VueTable.vue
|- Icon.vue
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

```
components/
|- BaseButton.vue
|- BaseTable.vue
|- BaseIcon.vue
```

```
components/
|- AppButton.vue
|- AppTable.vue
|- AppIcon.vue
```

```
components/
|- VButton.vue
|- VTable.vue
|- VIcon.vue
```
</div>

### Nomes de componentes de instância única <sup data-p="b">fortemente recomendado</sup>

**Componentes que devem ter somente uma única instância ativa devem começar com o prefixo `The`, para denotar que poderá existir somente um.**

Isto não significa que o componente é usado apenas em uma única página, mas que será usado uma vez _por página_. Estes componentes nunca aceitam quaisquer propriedades, pois são específicos à sua aplicação, e não ao contexto dentro da sua aplicação. Se você encontrar a necessidade de adicionar propriedades, é uma boa indicação de que este na verdade é um componente reutilizável que é usado uma vez por página _por hora_.

<div class="style-example style-example-bad">
<h4>Ruim</h4>

```
components/
|- Heading.vue
|- MySidebar.vue
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

```
components/
|- TheHeading.vue
|- TheSidebar.vue
```
</div>

### Nomes de componentes estreitamente acoplados <sup data-p="b">fortemente recomendado</sup>

**Componentes filho que são estreitamente acoplados com seus pais devem incluir o nome do componente pai como prefixo.**

Se um componente fizer sentido apenas no contexto de um único componente pai, este relacionamento deve ser evidente em seu nome. Como editores tipicamente organizam os arquivos alfabeticamente, isto irá ajudar a mantes estes arquivos relacionados próximos uns dos outros.

::: details Explicação Detalhada
Você pode ser atraído a resolver este problema aninhando componentes filho em diretórios nomeados com base em seu pai. Por exemplo:

```
components/
|- TodoList/
   |- Item/
      |- index.vue
      |- Button.vue
   |- index.vue
```

ou:

```
components/
|- TodoList/
   |- Item/
      |- Button.vue
   |- Item.vue
|- TodoList.vue
```

Isto não é recomendado, pois resulta em:

- Muitos arquivos com nomes similares, fazendo com que trocas de arquivos rápidas em editores de código tornem-se mais difíceis.
- Muitos subdiretórios aninhados, o que aumenta o tempo para procurar componentes na barra lateral do editor.
:::

<div class="style-example style-example-bad">
<h4>Ruim</h4>

```
components/
|- TodoList.vue
|- TodoItem.vue
|- TodoButton.vue
```

```
components/
|- SearchSidebar.vue
|- NavigationForSearchSidebar.vue
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

```
components/
|- TodoList.vue
|- TodoListItem.vue
|- TodoListItemButton.vue
```

```
components/
|- SearchSidebar.vue
|- SearchSidebarNavigation.vue
```
</div>

### Ordem das palavras em nomes de componente <sup data-p="b">fortemente recomendado</sup>

**Nomes de componente devem começar com palavras de nível mais alto (muitas vezes a mais geral) e terminar com palavras modificadoras descritivas.**

::: details Explicação Detalhada
Você pode estar se perguntando:

> "Por que forçamos nomes de componentes a usar uma linguagem menos natural?"

No inglês natural, adjetivos e outros descritores aparecem geralmente antes dos substantivos, enquanto exceções exigem palavras conectoras. Por exemplo:

- Café _com_ leite
- Sopa _do_ dia
- Visitante _do_ museu

Você definitivamente pode incluir estas palavras conectoras no nome dos componentes se quiser, mas a ordem ainda é importante. 

Também note que **o que é considerado "nível mais alto" será contextual à sua aplicação**. Por exemplo, imagine uma aplicação com um formulário de busca. Ele pode incluir componentes como este:

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```

Como você pode perceber, é difícil ver quais componentes são específicos da busca. Agora vamos renomear os componentes de acordo com a regra:

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputExcludeGlob.vue
|- SearchInputQuery.vue
|- SettingsCheckboxLaunchOnStartup.vue
|- SettingsCheckboxTerms.vue
```

Como editores tipicamente organizam os arquivos alfabeticamente, todas as relações importantes entre componentes agora estão evidentes à vista.

Você pode ser atraído a resolver este problema diferentemente, aninhando todos os componentes de busca em um diretório "search", e todos os componentes de configuração em um diretório "settings". Recomendamos considerar esta abordagem apenas em aplicações muito grandes (ex.: mais de 100 componentes), pelas seguintes razões:

- Geralmente leva mais tempo navegar por subdiretórios aninhados, do que percorrer um único diretório `components`.
- Conflitos com nomes (ex.: múltiplos componentes `ButtonDelete.vue`) tornam mais difícil navegar rapidamente para um componente específico no editor de código.
- Refatorar torna-se mais difícil, já que buscar-e-substituir nem sempre será suficiente para alterar as referências relativas para um componente deslocado.
:::

<div class="style-example style-example-bad">
<h4>Ruim</h4>

```
components/
|- ClearSearchButton.vue
|- ExcludeFromSearchInput.vue
|- LaunchOnStartupCheckbox.vue
|- RunSearchButton.vue
|- SearchInput.vue
|- TermsCheckbox.vue
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

```
components/
|- SearchButtonClear.vue
|- SearchButtonRun.vue
|- SearchInputQuery.vue
|- SearchInputExcludeGlob.vue
|- SettingsCheckboxTerms.vue
|- SettingsCheckboxLaunchOnStartup.vue
```
</div>

### Componentes com autofechamento <sup data-p="b">fortemente recomendado</sup>

**Componentes sem conteúdo devem ser autofechados em [componentes single file](../guide/single-file-component.html), templates string, e [JSX](../guide/render-function.html#jsx) - mas nunca em templates DOM.**

Componentes autofechados informam não apenas que não têm conteúdo, mas **garantem** que não devem ter conteúdo. É a diferença entre uma página em branco em um livro e uma rotulada "Esta página foi intencionalmente deixada em branco". Seu código também é mais limpo sem a tag de fechamento desnecessária.

Infelizmente, HTML não permite que elementos customizados tenham fechamento próprio - somente [elementos "void" oficiais](https://www.w3.org/TR/html/syntax.html#void-elements). É por isso que a estratégia só é possível quando o compilador de template do Vue pode alcançar o template antes do DOM, e então servir o HTML conforme especificado ao DOM.

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` html
<!-- Em componentes single file, templates string, e JSX -->
<MyComponent></MyComponent>
```

``` html
<!-- Em templates DOM -->
<my-component/>
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` html
<!-- Em componentes single file, templates string, e JSX -->
<MyComponent/>
```

``` html
<!-- Em templates DOM -->
<my-component></my-component>
```
</div>

### Notação de nomes de componente em templates <sup data-p="b">fortemente recomendado</sup>

**Na maioria dos projetos, os nomes de componente devem ser sempre PascalCase em [componentes single file](../guide/single-file-component.html) e templates de string - e kebab-case em templates DOM.**

PascalCase possui algumas vantagens sobre kebab-case:

- Editores podem autocompletar nomes de componentes em templates, pois o PascalCase também é utilizado no JavaScript.
- `<MyComponent>` é mais distintivo visualmente do que um simples elemento HTML de palavra única como `<my-component>`, pois há duas diferenças em caracteres (duas maiúsculas), ao invés de só uma (um hífen).
- Se você usar qualquer elemento personalizado em seus templates que não do Vue, como um web component, PascalCase garante que seus componentes Vue permaneçam distintamente visíveis.

Infelizmente, devido à insensibilidade do HTML quanto a maiúsculas e minúsculas, templates DOM ainda precisam utilizar kebab-case.

Também note que se você já investiu bastante em kebab-case, a consistência com convenções HTML e ser capaz de usar o mesmo padrão pelos seus projetos pode ser mais importante do que as vantagens listadas acima. Nestes casos, **utilizar kebab-case em todo lugar também é aceitável.**

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` html
<!-- Em componentes single file e templates string -->
<mycomponent/>
```

``` html
<!-- Em componentes single file e templates string -->
<myComponent/>
```

``` html
<!-- Em templates DOM -->
<MyComponent></MyComponent>
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` html
<!-- Em componentes single file e templates string -->
<MyComponent/>
```

``` html
<!-- Em templates DOM -->
<my-component></my-component>
```

OU

``` html
<!-- Em qualquer lugar -->
<my-component></my-component>
```
</div>

### Notação de nomes de componentes em JS/JSX <sup data-p="b">fortemente recomendado</sup>

**Nomes de componentes em JS/[JSX](../guide/render-function.html#jsx) devem ser sempre PascalCase, apesar de que podem ser kebab-case dentro de strings para aplicações mais simples, que usam apenas registros globais de componentes  através de `app.component`.**

::: details Explicação Detalhada
No JavaScript, PascalCase é a convenção para classes e construtores - essencialmente, qualquer coisa que possa ter instâncias diferentes. Componentes Vue também possuem instâncias, então faz sentido também usar PascalCase. Como um benefício extra, usar PascalCase com JSX (e templates) permite que os leitores do código consigam distinguir mais facilmente entre componentes e elementos HTML. 

Entretanto, para aplicações que usam **apenas** definições globais de componente via `app.component`, recomendamos que kebab-case seja usado. Os motivos são:

- É raro que componentes globais sejam referenciados no JavaScript, então seguir a convenção para o JavaScript faz menos sentido.
- Essas aplicações sempre incluem muito templates dentro do DOM, onde [kebab-case **deve** ser usado](#component-name-casing-in-templates-strongly-recommended).
:::

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` js
app.component('myComponent', {
  // ...
})
```

``` js
import myComponent from './MyComponent.vue'
```

``` js
export default {
  name: 'myComponent',
  // ...
}
```

``` js
export default {
  name: 'my-component',
  // ...
}
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` js
app.component('MyComponent', {
  // ...
})
```

``` js
app.component('my-component', {
  // ...
})
```

``` js
import MyComponent from './MyComponent.vue'
```

``` js
export default {
  name: 'MyComponent',
  // ...
}
```
</div>

### Palavras completas em nomes de componente <sup data-p="b">fortemente recomendado</sup>

**Nomes de componente devem preferir palavras completas ao invés de abreviações.**

O preenchimento automático em editores torna o custo de escrever nomes maiores muito baixo, enquanto a clareza que eles fornecem é inestimável. Abreviações incomuns, em particular, devem sempre ser evitadas.

<div class="style-example style-example-bad">
<h4>Ruim</h4>

```
components/
|- SdSettings.vue
|- UProfOpts.vue
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

```
components/
|- StudentDashboardSettings.vue
|- UserProfileOptions.vue
```
</div>

### Notação do nome de propriedades <sup data-p="b">fortemente recomendado</sup>

**Nomes de propriedades devem sempre usar camelCase em sua declaração, e kebab-case em templates e [JSX](../guide/render-function.html#jsx).**

Estamos simplesmente seguindo as convenções de cada linguagem. Dentro do JavaScript, camelCase é mais natural. Dentro do HTML é o kebab-case.

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` js
props: {
  'greeting-text': String
}
```

``` html
<WelcomeMessage greetingText="hi"/>
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` js
props: {
  greetingText: String
}
```

``` html
<WelcomeMessage greeting-text="hi"/>
```
</div>

### Elementos com multiatributos <sup data-p="b">fortemente recomendado</sup>

**Elementos com vários atributos devem sempre ocupar múltiplas linhas, com um atributo por linha.**

No JavaScript, dividir objetos com múltiplas propriedades por múltiplas linhas é considerada uma boa convenção, pois é muito mais fácil de se ler. Nossos templates e [JSX](../guide/render-function.html#jsx) merecem a mesma consideração.

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` html
<img src="https://vuejs.org/images/logo.png" alt="Vue Logo">
```

``` html
<MyComponent foo="a" bar="b" baz="c"/>
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` html
<img
  src="https://vuejs.org/images/logo.png"
  alt="Vue Logo"
>
```

``` html
<MyComponent
  foo="a"
  bar="b"
  baz="c"
/>
```
</div>

### Expressões simples em templates <sup data-p="b">fortemente recomendado</sup>

**Templates de componentes devem incluir apenas expressões simples, com expressões mais complexas sendo refatoradas em dados computados ou métodos.**

Expressões complexas em seus templates os tornam menos declarativos. Devemos nos esforçar para descrever _o quê_ deve aparecer, não _como_ estamos computando aquele valor. Dados computados e métodos também permitem que o código seja reutilizado.

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` html
{{
  fullName.split(' ').map((word) => {
    return word[0].toUpperCase() + word.slice(1)
  }).join(' ')
}}
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` html
<!-- Em um template -->
{{ normalizedFullName }}
```

``` js
// Esta expressão complexa foi movida para uma propriedade computada
computed: {
  normalizedFullName() {
    return this.fullName.split(' ')
      .map(word => word[0].toUpperCase() + word.slice(1))
      .join(' ')
  }
}
```
</div>

### Dados computados simples <sup data-p="b">fortemente recomendado</sup>

**Dados computados complexos devem ser divididos em dados computados mais simples sempre que possível.**

::: details Explicação Detalhada
Dados computados mais simples e bem nomeados são:

- __Mais fáceis de testar__

  Quando cada propriedade computada contém somente uma expressão muito simples, com poucas dependências, é muito mais fácil escrever testes confirmando que elas funcionam corretamente.

- __Mais fáceis de ler__

  Simplificar dados computados força você a dar a cada valor um nome descritivo, mesmo que não seja reutilizado. Isso torna mais fácil para outros desenvolvedores (e você no futuro) a focarem no código que manipulam  e no que está acontecendo.

- __Mais adaptáveis a novas exigências__

  Qualquer valor que possa ser nomeado pode ser útil para a view. Por exemplo, podemos decidir mostrar a mensagem informando ao usuário quanto dinheiro ele economizou. Também podemos decidir como calcular taxas das vendas, mas talvez mostrá-los separadamente, ao invés de uma parte do preço final.

  Dados computados pequenos e orientados trazem menos suposições sobre como a informação será usada, e exigem menos refatoração conforme as exigências mudam.
:::

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` js
computed: {
  price() {
    const basePrice = this.manufactureCost / (1 - this.profitMargin)
    return (
      basePrice -
      basePrice * (this.discountPercent || 0)
    )
  }
}
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` js
computed: {
  basePrice() {
    return this.manufactureCost / (1 - this.profitMargin)
  },

  discount() {
    return this.basePrice * (this.discountPercent || 0)
  },

  finalPrice() {
    return this.basePrice - this.discount
  }
}
```
</div>

### Aspas em valores de atributos <sup data-p="b">fortemente recomendado</sup>

**Valores de atributos HTML não vazios devem sempre estar dentro de aspas (simples ou duplas, a qual não for usada no JS).**

Enquanto valores de atributo sem qualquer espaço não exigem aspas no HTML, esta prática frequentemente leva a _evitar_ espaços, tornando os valores dos atributos menos legíveis.

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` html
<input type=text>
```

``` html
<AppSidebar :style={width:sidebarWidth+'px'}>
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` html
<input type="text">
```

``` html
<AppSidebar :style="{ width: sidebarWidth + 'px' }">
```
</div>

### Abreviação de diretrizes <sup data-p="b">fortemente recomendado</sup>

**Abreviações de diretrizes (`:` para `v-bind:`, `@` para `v-on:` e `#` para `v-slot`) devem ser usadas sempre ou nunca.**

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` html
<input
  v-bind:value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

``` html
<input
  v-on:input="onInput"
  @focus="onFocus"
>
```

``` html
<template v-slot:header>
  <h1>Aqui pode ser um título de página</h1>
</template>

<template #footer>
  <p>Aqui alguma informação de contato</p>
</template>
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` html
<input
  :value="newTodoText"
  :placeholder="newTodoInstructions"
>
```

``` html
<input
  v-bind:value="newTodoText"
  v-bind:placeholder="newTodoInstructions"
>
```

``` html
<input
  @input="onInput"
  @focus="onFocus"
>
```

``` html
<input
  v-on:input="onInput"
  v-on:focus="onFocus"
>
```

``` html
<template v-slot:header>
  <h1>>Aqui pode ser um título de página</h1>
</template>

<template v-slot:footer>
  <p>Aqui alguma informação de contato</p>
</template>
```

``` html
<template #header>
  <h1>Aqui pode ser um título de página</h1>
</template>

<template #footer>
  <p>Aqui alguma informação de contato</p>
</template>
```
</div>


## Regras Prioridade C: Recomendadas <span class="hide-from-sidebar">(Minimizam Escolhas Arbitrárias e Sobrecarga Cognitiva)</span>

### Ordenamento das opções de componente/instância <sup data-p="c">recomendado</sup>

**As opções de componente/instância devem ser sempre ordenadas consistentemente.**

Esta é a ordem padrão que recomendamos para opções de componente. Elas são divididas em categorias, então você irá saber onde adicionar novas propriedades para plugins.

1. **Consciência Global** (exige conhecimento além do componente)
    - `name`

2. **Modificadores de Template** (mudam a maneira que templates são compilados)
    - `delimiters`

3. **Dependências de Template** (recursos usados no template)
    - `components`
    - `directives`

4. **Composição** (mescla propriedades nas opções)
    - `extends`
    - `mixins`
    - `provide`/`inject`

5. **Interface** (a interface do componente)
    - `inheritAttrs`
    - `props`
    - `emits`

6. **Composition API** (ponto de entrada para usar a Composition API)
    - `setup`

7. **Estado Local** (propriedades locais reativas)
    - `data`
    - `computed`

8. **Eventos** (callbacks acionados por eventos reativos)
    - `watch`
    - Eventos do Ciclo de Vida (na ordem em que são chamados)
        - `beforeCreate`
        - `created`
        - `beforeMount`
        - `mounted`
        - `beforeUpdate`
        - `updated`
        - `activated`
        - `deactivated`
        - `beforeUnmount`
        - `unmounted`
        - `errorCaptured`
        - `renderTracked`
        - `renderTriggered`

9.  **Propriedades não reativas** (propriedades da instância independentes da reatividade do sistema)
    - `methods`

10. **Renderização** (a descrição declarativa da saída do componente)
    - `template`/`render`

### Ordem dos atributos de elementos <sup data-p="c">recomendado</sup>

**Os atributos dos elementos (incluindo componentes) devem ser ordenados consistentemente.**

Está é a ordenação padrão que recomendamos para os atributos de componentes. Eles são divididos em categorias, então você saberá onde adicionar atributos e diretrizes personalizadas.

1. **Definição** (fornece as opções do componente)
    - `is`

2. **Renderização de Listas** (cria múltiplas variações do mesmo elemento)
    - `v-for`

3. **Condicionais** (se o elemento é renderizado/mostrado)
    - `v-if`
    - `v-else-if`
    - `v-else`
    - `v-show`
    - `v-cloak`

4. **Modificadores de Renderização** (mudam a forma que o elemento é renderizado)
    - `v-pre`
    - `v-once`

5. **Consciência Global** (exige conhecimento além do componente)
    - `id`

6. **Atributos Únicos** (atributos que exigem valores únicos)
    - `ref`
    - `key`

7. **Vinculação Bidirecional** (combina eventos e vinculações)
    - `v-model`

8. **Outros Atributos** (todos os atributos não especificados vinculados ou não)

9. **Eventos** (escutadores de evento dos componentes)
    - `v-on`

10. **Conteúdo** (sobrescrevem o conteúdo do elemento)
    - `v-html`
    - `v-text`

### Linhas vazias em opções de componente/instância <sup data-p="c">recomendado</sup>

**Você pode querer adicionar uma linha vazia entre propriedades de várias linhas, especialmente se as opções não couberem mais na sua tela sem rolagem.**

Quando os componentes começam a parecer abarrotados ou difíceis de ler, adicionar espaços entre propriedades multilinhas pode torná-lo fácil para se ler novamente. Em alguns editores, como o Vim, opções de formatação como essa podem também fazer mais fácil de navegar com o teclado.

<div class="style-example style-example-good">
<h4>Bom</h4>

``` js
props: {
  value: {
    type: String,
    required: true
  },

  focused: {
    type: Boolean,
    default: false
  },

  label: String,
  icon: String
},

computed: {
  formattedValue() {
    // ...
  },

  inputClasses() {
    // ...
  }
}
```

``` js
// Também é bom não ter espaços, desde que o componente
// seja fácil de ler e de navegar.
props: {
  value: {
    type: String,
    required: true
  },
  focused: {
    type: Boolean,
    default: false
  },
  label: String,
  icon: String
},
computed: {
  formattedValue() {
    // ...
  },
  inputClasses() {
    // ...
  }
}
```
</div>

### Ordem dos elementos de nível superior de componentes single file <sup data-p="c">recomendado</sup>

**[Componentes Single File](../guide/single-file-component.html) devem sempre ordenar as tags `<script>`, `<template>`, e `<style>` consistentemente, com a `<style>` por último, porque ao menos uma das outras duas é sempre necessária.**

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` html
<style>/* ... */</style>
<script>/* ... */</script>
<template>...</template>
```

``` html
<!-- ComponentA.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` html
<!-- ComponentA.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<script>/* ... */</script>
<template>...</template>
<style>/* ... */</style>
```

``` html
<!-- ComponentA.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>

<!-- ComponentB.vue -->
<template>...</template>
<script>/* ... */</script>
<style>/* ... */</style>
```
</div>

## Regras Prioridade D: Use com Cautela <span class="hide-from-sidebar">(Padrões Potencialmente Perigosos)</span>

### Seletores de elemento em `scoped` <sup data-p="d">use com cautela</sup>

**Seletores de elemento devem ser evitados com o `scoped`.**

Prefira seletores de classe sobre seletores de elemento em estilos `scoped`, pois um grande número de seletores de elemento são lentos.

::: details Explicação Detalhada
Para usar o escopo em estilos, o Vue adiciona um atributo único aos elementos componentes, como um `data-v-f3f3eg9`. Os seletores então são modificados para que apenas os elementos correspondentes com este atributo sejam selecionados. (ex.: `button[data-v-f3f3eg9]`).

O problema é que um grande número de [seletores atribuídos por elementos](http://stevesouders.com/efws/css-selectors/csscreate.php?n=1000&sel=a%5Bhref%5D&body=background%3A+%23CFD&ne=1000) (ex.: `button[data-v-f3f3eg9]`) serão consideravelmente mais lentos do que [seletores atribuídos por classe](http://stevesouders.com/efws/css-selectors/csscreate.php?n=1000&sel=.class%5Bhref%5D&body=background%3A+%23CFD&ne=1000) (ex.: `.btn-close[data-v-f3f3eg9]`), então seletores de classe devem ser preferidos sempre que possível.

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` html
<template>
  <button>×</button>
</template>

<style scoped>
button {
  background-color: red;
}
</style>
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` html
<template>
  <button class="btn btn-close">×</button>
</template>

<style scoped>
.btn-close {
  background-color: red;
}
</style>
```
</div>

### Comunicação implícita entre componentes pai-filho <sup data-p="d">use com cautela</sup>

**Propriedades e eventos devem ser preferidos para a comunicação entre componentes pai-filho, ao invés de `this.$parent` ou propriedades mutantes.**

Uma aplicação Vue ideal passa propriedades para baixo, e eventos para cima. Ater-se a esta convenção irá tornar os seus componentes muito mais fáceis de entender. Entretanto, há casos extremos onde a mutação da propriedade ou o `this.$parent` poderá simplificar dois componentes que já estão profundamente atrelados.

O problema é que existem também muitos casos _simples_ onde estes padrões podem oferecer conveniência. Cuidado: não se deixe atrair por trocar simplicidade (ser capaz de entender o fluxo do seu estado) pela conveniência em curto prazo (escrever menos código).

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` js
app.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  template: '<input v-model="todo.text">'
})
```

``` js
app.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  methods: {
    removeTodo() {
      this.$parent.todos = this.$parent.todos.filter(todo => todo.id !== vm.todo.id)
    }
  },

  template: `
    <span>
      {{ todo.text }}
      <button @click="removeTodo">
        ×
      </button>
    </span>
  `
})
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` js
app.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  template: `
    <input
      :value="todo.text"
      @input="$emit('input', $event.target.value)"
    >
  `
})
```

``` js
app.component('TodoItem', {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  template: `
    <span>
      {{ todo.text }}
      <button @click="$emit('delete')">
        ×
      </button>
    </span>
  `
})
```
</div>

### Gerenciamento de estado sem fluxo <sup data-p="d">use com cautela</sup>

**[Vuex](https://github.com/vuejs/vuex) deve ser preferido para o gerenciamento de estado global, ao invés de `this.$root` ou um barreamento de eventos global.**

Gerenciar o estado em `this.$root` e/ou usando um [barramento de eventos global](https://vuejs.org/v2/guide/migration.html#dispatch-and-broadcast-replaced) pode ser conveniente para casos muito simples, mas é inapropriado para a maioria das aplicações.

Vuex é a [implementação oficial estilo flux](https://vuejs.org/v2/guide/state-management.html#Official-Flux-Like-Implementation) para o Vue, e oferece não apenas um local central para gerenciar o estado, mas também ferramentas para organizar, rastrear, e depurar alterações de estado. Ele integra bem o ecossistema Vue (incluindo o completo suporte [Vue DevTools](https://vuejs.org/v2/guide/installation.html#Vue-Devtools) ).

<div class="style-example style-example-bad">
<h4>Ruim</h4>

``` js
// main.js
import { createApp } from 'vue'
import mitt from 'mitt'
const app = createApp({
  data() {
    return {
      todos: [],
      emitter: mitt()
    }
  },

  created() {
    this.emitter.on('remove-todo', this.removeTodo)
  },

  methods: {
    removeTodo(todo) {
      const todoIdToRemove = todo.id
      this.todos = this.todos.filter(todo => todo.id !== todoIdToRemove)
    }
  }
})
```
</div>

<div class="style-example style-example-good">
<h4>Bom</h4>

``` js
// store/modules/todos.js
export default {
  state: {
    list: []
  },

  mutations: {
    REMOVE_TODO (state, todoId) {
      state.list = state.list.filter(todo => todo.id !== todoId)
    }
  },

  actions: {
    removeTodo ({ commit, state }, todo) {
      commit('REMOVE_TODO', todo.id)
    }
  }
}
```

``` html
<!-- TodoItem.vue -->
<template>
  <span>
    {{ todo.text }}
    <button @click="removeTodo(todo)">
      X
    </button>
  </span>
</template>

<script>
import { mapActions } from 'vuex'

export default {
  props: {
    todo: {
      type: Object,
      required: true
    }
  },

  methods: mapActions(['removeTodo'])
}
</script>
```
</div>
 
<style lang="scss" scoped>
$color-bgr-good: #d7efd7;
$color-bgr-bad: #f7e8e8;
$color-priority-a: #6b2a2a;
$color-priority-b: #8c480a;
$color-priority-c: #2b5a99;
$color-priority-d: #3f536d;

.style-example {
  border-radius: 7px;
  margin: 1.6em 0;
  padding: 1.6em 1.6em 1em;
  position: relative;
  border: 1px solid transparent;
  border-top-width: 5px;

  h4 {
    margin-top: 0;

    &::before {
      font-family: 'FontAwesome';
      margin-right: .4em;
    }
  }

  &-bad {
    background: $color-bgr-bad;
    border-color: darken($color-bgr-bad, 20%);
    
    h4 {
      color: darken($color-bgr-bad, 50%);
    }

    h4::before {
      content: '\f057';
    }
  }

  &-good {
    background: $color-bgr-good;
    border-color: darken($color-bgr-good, 20%);
    
    h4 {
      color: darken($color-bgr-good, 50%);
    }

    h4::before {
      content: '\f058';
    }
  }
}

.details summary {
  font-weight: bold !important;
}

h3 {
  a.header-anchor {
    // as we have too many h3 elements on this page, set the anchor to be always visible
    // to make them stand out more from paragraph texts.
    opacity: 1;
  }

  sup {
    text-transform: uppercase;
    font-size: 0.5em;
    padding: 2px 4px;
    border-radius: 3px;
    margin-left: 0.5em;

    &[data-p=a] {
      color: $color-priority-a;
      border: 1px solid $color-priority-a;
    }

    &[data-p=b] {
      color: $color-priority-b;
      border: 1px solid $color-priority-b;
    }

    &[data-p=c] {
      color: $color-priority-c;
      border: 1px solid $color-priority-c;
    }

    &[data-p=d] {
      color: $color-priority-d;
      border: 1px solid $color-priority-d;
    }
  }
}
</style>
