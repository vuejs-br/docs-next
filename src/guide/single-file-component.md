# Componentes Single-File

## Introdução

Em muitos projetos Vue, os componentes globais serão definidos usando `app.component()`, seguido por `app.mount('#app')` para atingir um elemento _container_ no corpo de cada página.

Isto pode funcionar bem em projetos pequenos e médios, onde o JavaScript só é utilizado para incrementar certas interfaces. No entanto, em projetos mais complexos ou quando o _frontend_ é totalmente orientado pelo JavaScript, tais desvantagens se tornam aparentes:

- **Definições globais** forçam nomes únicos para cada componente
- **Templates com Strings** não têm _syntax highlighting_ e são difíceis de ler em múltiplas linhas
- **Sem suporte ao CSS** enquanto HTML e JavaScript são modularizados em componentes, CSS é notavelmente deixado de fora
- **Sem processamento no build** ou seja, nos restringindo a HTML e JavaScript ES5, em vez de preprocessadores como Pug (anteriormente Jade) e Babel

Tudo isso é resolvido através dos **Componentes Single-File** com a extensão `.vue`, possibilitados graças a ferramentas de _build_ como Webpack ou Browserify.

Aqui está um exemplo de um arquivo que chamaremos de `Hello.vue`:

<a href="https://codepen.io/team/Vue/pen/3de13b5cd0133df4ecf307b6cf2c5f94" target="_blank" rel="noopener noreferrer"><img src="/images/sfc.png" width="403" alt="Single-file component example (click for code as text)" style="display: block; margin: 15px auto; max-width: 100%"></a>

Agora nós obtivemos:

- [_Syntax Highlighting_ completo](https://github.com/vuejs/awesome-vue#source-code-editing)
- [Módulos tipo CommonJS](https://webpack.js.org/concepts/modules/#what-is-a-webpack-module)
- [Escopo de CSS por componente](https://vue-loader.vuejs.org/en/features/scoped-css.html)

Como prometido, podemos também utilizar pré-processadores como Pug (Jade), Babel (com módulos ES2015+) e Stylus para componentes mais limpos e mais ricos em recursos.

<a href="https://codesandbox.io/s/vue-single-file-component-with-pre-processors-mr3ik?file=/src/App.vue" target="_blank" rel="noopener noreferrer"><img src="/images/sfc-with-preprocessors.png" width="563" alt="Single-file component with pre-processors example (click for code as text)" style="display: block; margin: 15px auto; max-width: 100%"></a>

Estas linguagens específicas são meros exemplos. Poderíamos tão facilmente usar TypeScript, SCSS, PostCSS - ou qualquer outro que acreditarmos nos tornar mais produtivos. Se usar Webpack com `vue-loader`, também há suporte de primeira classe a CSS Modules.

### E a Separação de Responsabilidades?

Uma coisa importante a observar é que **separação de responsabilidades não é igual a separação de tipos de arquivo**. No desenvolvimento moderno de interfaces, chegamos à conclusão de que, ao invés de dividir a base de código em três grandes camadas que se entrelaçam umas com as outras, faz muito mais sentido dividi-las em componentes fracamente acoplados e utilizá-los para composição. Dentro de um componente, seu template, sua lógica e seus estilos são inerentemente acoplados, e encará-los dessa forma os torna componentes mais coesos e manuteníveis.

Se mesmo assim não gostar da ideia de Componentes _Single-File_, você ainda pode tirar vantagem de seus recursos de _hot-reloading_ (recompilação e atualização em tempo real ao salvar o código) e de pré-compilação, separando JavaScript e CSS em arquivos distintos:

```html
<!-- my-component.vue -->
<template>
  <div>Isso será pré-compilado</div>
</template>
<script src="./my-component.js"></script>
<style src="./my-component.css"></style>
```

## Primeiros Passos

### Exemplo Sandbox

Se você quiser se mergulhar agora mesmo e começar a brincar com Componentes _Single-File_, dê uma olhada [neste aplicativo de lista de tarefas simples](https://codesandbox.io/s/vue-todo-list-app-with-single-file-component-vzkl3?file=/src/App.vue) no CodeSandbox.

### Para Iniciantes com Empacotadores

Com componentes `.vue`, estamos entrando no reino das aplicações JavaScript avançadas. Significa aprender a utilizar algumas ferramentas adicionais se ainda não teve oportunidade:

- **Node Package Manager (npm)**: Leia o guia de [Primeiros Passos](https://docs.npmjs.com/packages-and-modules/getting-packages-from-the-registry) do NPM na seção sobre como obter pacotes de registro.

- **JavaScript Moderno com ES2015+**: Leia o guia [Aprendendo ES2015](https://babeljs.io/docs/en/learn) do Babel. Não é necessário memorizar cada recurso agora, mas mantenha esta página como referência.

Depois que tiver tirado um dia para mergulhar nestes tópicos, recomendamos dar uma olhada no [Vue CLI](https://cli.vuejs.org/). Seguindo as instruções, você terá em mãos um projeto Vue com componentes `.vue`, ES2015, Webpack e _hot-reloading_, rodando em pouquíssimo tempo!

### Para Usuários Avançados

A CLI cuida da maioria das configurações de ferramentas para você, mas também permite a personalização refinada através de suas próprias [opções de configuração](https://cli.vuejs.org/config/).

Caso você prefira configurar seu próprio _build_ a partir do zero, você precisará configurar manualmente o webpack com o [vue-loader](https://vue-loader.vuejs.org). Para saber mais sobre o webpack, confira os [documentos oficiais](https://webpack.js.org/configuration/) e a [Webpack Academy](https://webpack.academy/p/the-core-concepts).
