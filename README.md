# vuejs-br/docs-next

Este é o repositório oficial da tradução para português brasileiro da documentação do [Vue.js](https://v3.vuejs.org/). Este _site_ é produzido com [VuePress](https://vuepress.vuejs.org/). Todo o conteúdo é escrito em Markdown e os arquivos podem ser encontrados em `src`.

> Nota da equipe de tradução: o trabalho nesta nova versão da documentação ainda está em andamento, portanto muitos conteúdos estão em seu estado original (em inglês). Ajuda é muito bem-vinda!

## Traduzindo

Se você quer participar da tradução, ajudando a traduzir conteúdos que ainda permanecem em inglês e/ou ajudando a revisar conteúdos já traduzidos, sinta-se em casa. Para entender como tudo funciona e saber por onde começar, leia nosso guia de [Contribuição com a Documentação](https://vuejsbr-docs-next.netlify.app/guide/contributing/translations.html).

> O _workflow_ para a realização da tradução está detalhadamente descrito em [Como colaborar com a versão em português?](https://vuejsbr-docs-next.netlify.app/guide/contributing/translations.html#como-colaborar-com-a-versao-em-portugues) Ler é o primeiro passo para todo colaborador em potencial.

## Escrevendo

Se você quer participar ajudando na construção e atualização da documentação principal (em inglês), veja o [Guia de Escrita](https://v3.vuejs.org/guide/writing-guide.html) para regras e recomendações sobre escrever e manter a documentação.

> Neste momento a documentação principal (em inglês) está em beta: o time está atualmente no meio de muitas mudanças e não está recebendo contribuições. Todo o conteúdo está sujeito a mudanças. Se você viu algo que gostaria de trazer à atenção do time, [crie uma _issue_](https://github.com/vuejs/docs-next/issues/new) e todos farão o possível para analisar. No entanto, provavelmente você precisará esperar até que todo o conteúdo esteja finalizado.

## Desenvolvendo

Se deseja executar o projeto localmente, seja para contribuir com a documentação ou seja para olhar como as coisas funcionam, sigua os passos a seguir.

1. Clone este repositório:

```bash
git clone https://github.com/vuejs-br/docs-next.git
```

2. Instale as dependências:

```bash
yarn # or npm install
```

3. Inicie um ambiente de desenvolvimento local:

```bash
yarn serve # or npm run serve
```

## Publicando

[![Netlify Status](https://api.netlify.com/api/v1/badges/35e551aa-2430-4ca9-aae6-72ba49d7ffec/deploy-status)](https://app.netlify.com/sites/vuejsbr-docs-next/deploys)

O _site_ é automaticamente publicado quando _commits_ chegam em `master`, via [Netlify](https://www.netlify.com/).

O processo é disparado somente por um grupo de contribuidores selecionados para isso, a fim de manter a organização do projeto. Colabore apenas enviando _pull requests_ e, depois de revisados e aceitos, serão mesclados em `master` pela equipe. Ou seja, não se preocupe com a publicação, o _deploy_ é automático.
