# Instalação

## Notas de Lançamento

Última versão: ![npm](https://img.shields.io/npm/v/vue/next.svg)

Notas de lançamento detalhadas para cada versão estão disponíveis no [GitHub](https://github.com/vuejs/vue-next/blob/master/CHANGELOG.md).

## Vue Devtools

> Atualmente em versão Beta - integração com Vuex e Router ainda em andamento

Ao utilizar o Vue, recomendamos também instalar a extensão [Vue Devtools](https://github.com/vuejs/vue-devtools#vue-devtools) no seu navegador, que lhe permite inspecionar e realizar _debug_ de suas aplicações Vue através de uma interface mais amigável.

[Adquirir a extensão para Chrome](https://chrome.google.com/webstore/detail/vuejs-devtools/ljjemllljcmogpfapbkkighbhhppjdbg)

[Adquirir a extensão para Firefox](https://addons.mozilla.org/en-US/firefox/addon/vue-js-devtools/)

[Adquirir a aplicação individual, baseada em Electron](https://github.com/vuejs/vue-devtools/blob/dev/packages/shell-electron/README.md)

## CDN

Para prototipação ou aprendizagem, você pode utilizar a última versão com:

```html
<script src="https://unpkg.com/vue@next"></script>
```

Para produção, recomendamos vinculá-lo a uma versão e uma distribuição _(build)_ específicas, a fim de evitar incompatibilidade de funcionalidades devido a uma nova versão.

## NPM

NPM é o método de instalação que recomendamos ao construir aplicações de larga escala com o Vue. Ele combina perfeitamente com empacotadores de módulos _(module bundlers)_, como o [Webpack](https://webpack.js.org/) ou o [Browserify](http://browserify.org/). Vue também oferece ferramentas que auxiliam na criação de [Componentes Single File](../guide/single-file-component.html).

```bash
# última versão estável
$ npm install vue@next
```

## CLI

Vue oferece uma [CLI oficial](https://github.com/vuejs/vue-cli) para projetar, rapidamente, ambiciosas _Single Page Applications_. Esta oferece conjuntos prontos de configurações de distribuição para um processo de desenvolvimento _frontend_ moderno. Basta apenas alguns minutos para ter tudo funcionando e sendo executado com _hot-reload_, _lint-on-save_ e distribuições _(builds)_ prontas para produção. Confira a [documentação da Vue CLI](https://cli.vuejs.org) para mais detalhes.

::: tip
A CLI pressupõe que você já possui conhecimento prévio em Node.js e das ferramentas de distribuição _(build tools)_ associadas. Se Vue ou as ferramentas de distribuição _frontend_ associadas são assuntos novos para você, recomendamos fortemente que você passe por todo <a href="./">o guia</a> antes de utilizar a CLI.
:::

Para o Vue 3, você deve utilizar, no mínimo, a Vue CLI v4.5, disponível no `npm` através do `@vue/cli@next`. Para atualizar sua Vue CLI, você deve reinstalar o pacote `@vue/cli` globalmente, em sua última versão:

```bash
yarn global add @vue/cli@next
# OU
npm install -g @vue/cli@next
```

Logo em seguida, em seus projetos Vue, execute:

```bash
vue upgrade --next
```

## Vite

[Vite](https://github.com/vitejs/vite) é uma ferramenta de distribuição _(build tool)_ para desenvolvimento _web_ que permite servir código de maneira ultra rápida devido à sua abordagem de importação _ES Module_ nativa.

Projetos Vue podem ser rapidamente inicializados com Vite ao executar os seguintes comandos no seu _terminal_:

Com NPM:

```bash
$ npm init vite-app <nome-do-projeto>
$ cd <nome-do-projeto>
$ npm install
$ npm run dev
```

Ou com Yarn:

```bash
$ yarn create vite-app <nome-do-projeto>
$ cd <nome-do-projeto>
$ yarn
$ yarn dev
```

## Esclarecimento sobre as diferentes Distribuições

No [diretório `dist/` do pacote NPM](https://cdn.jsdelivr.net/npm/vue@3.0.0-rc.1/dist/), você encontrará diversas distribuições _(builds)_ do Vue.js. A seguir, temos uma visão geral de qual arquivo do `dist` você deve utilizar, dependendo do seu caso de uso:

### Utilizando a partir de uma CDN ou sem um Empacotador

#### `vue(.runtime).global(.prod).js`:

- Para uso direto no navegador, através de `<script src="...">`, ao expor uma variável Vue global.
- Compilação de _templates_ diretamente no navegador:
  - `vue.global.js` é a distribuição _(build)_ "completa", que inclui compilador e _runtime_, a fim de dar suporte à compilação de _templates_ sob demanda.
  - `vue.runtime.global.js` possui apenas o _runtime_ e exige _templates_ pré-compilados.
- Inclui todos os pacotes internos básicos do Vue - em outras palavras, é apenas um arquivo, sem dependências externas. Isto significa que você deve importar tudo deste arquivo - e apenas deste arquivo, a fim de garantir que você sempre estará utilizando a mesma instância de código.
- Possui distribuições _(builds)_ _hard-coded_ para produção e desenvolvimento, sendo a distribuição de produção já pré-minificada. Utilize arquivos `*.prod.js` em produção.

:::tip Note
Distribuições globais _(global builds)_ não são distribuições [UMD](https://github.com/umdjs/umd). Elas são geradas como [IIFEs](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) e são destinadas apenas para uso direto através de `<script src="...">`.
:::

#### vue(.runtime).esm-browser(.prod).js:

- Para uso através de importação _ES modules_ nativa (no navegador, através de `<script type="module">`).
- Compartilha o mesmo _runtime_ de compilação, o mesmo _inlining_ de dependências e o mesmo comportamento de produção/desenvolvimento, _hard-coded_, com a distribuição _(build)_ global.

### Utilizando a partir de um Empacotador

#### vue(.runtime).esm-bundler.js:

- Para uso de empacotadores, como `webpack`, `rollup` e `parcel`.
- Gera distribuições de produção e desenvolvimento a partir de `process.env.NODE_ENV guards` (deve ser substituído pelo empacotador)
- Não inclui distribuições _(builds)_ minificadas (a fim deste processo ser realizado junto do resto do código, pelo empacotador)
- Importa dependências (por exemplo, `@vue/runtime-core`, `@vue/runtime-compiler`)
  - Dependências importadas também são distribuições _(builds)_ geradas pelo _esm-bundler_ e, por sua vez, importarão suas dependências (por exemplo, `@vue/runtime-core` importa `@vue/reactivity`)
  - Isto significa que **é possível** instalar ou importar estas dependências individualmente, ficando sem instâncias diferentes. Entretanto, você deve garantir a unicidade, utilizando sempre das mesmas versões.
- Compilação de _template_ diretamente no navegador:
  - `vue.runtime.esm-bundler.js` **(padrão)** compreende apenas o _runtime_, e requer que todos os _templates_ sejam pré-compilados. Por esta razão, empacotadores _(bundlers)_ acabam utilizando este arquivo, por padrão (definido em `module` no `package.json`), uma vez que, normalmente, já pré-compilam os _templates_ do projeto (por exemplo, arquivos `*.vue`)
  - `vue.esm-bundler.js`: inclui o compilador e o _runtime_. Utilize este arquivo se você está utilizando um empacotador, mas ainda assim deseja compilar _templates_ sob demanda (por exemplo, _templates_ disponíveis no DOM ou _templates_ em _strings_ do JavaScript)

### Para Renderização no lado do Servidor

#### `vue.cjs(.prod).js`:

- Para renderização no lado do servidor, em Node.js, via `require()`.
- Esta é a distribuição _(build)_ que será carregada se você empacota sua aplicação com _webpack_ utilizando `target: 'node'` e externalizando `vue` apropriadamente.
- Os arquivos de desenvolvimento e produção são previamente gerados, mas o arquivo mais apropriado é automaticamente adquirido baseado em `process.env.NODE_ENV`.

## _Runtime_ + Compilador vs. somente _Runtime_

Se você precisar compilar _templates_ no cliente (por exemplo, passando uma _string_ para uma propriedade `template` ou utilizando do conteúdo de um elemento já existente no DOM do HTML), você precisará do compilador e, portanto, da distribuição _(build)_ completa:

```js
// isto requer o compilador
Vue.createApp({
  template: '<div>{{ hi }}</div>'
})

// isto não requer o compilador
Vue.createApp({
  render() {
    return Vue.h('div', {}, this.hi)
  }
})
```

Ao utilizar o `vue-loader`, _templates_ dentro de arquivos `*.vue` são pré-compilados para JavaScript durante o processo de construção _(build time)_. Você não precisa, necessariamente, do compilador no seu pacote _(bundle)_ final e, portanto, pode utilizar apenas a distribuição _runtime_.
