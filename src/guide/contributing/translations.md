# Traduzindo

Vue se espalhou pelo globo, com a equipe principal em pelo menos meia dúzia de fusos horários diferentes. O [fórum oficial](https://forum.vuejs.org/) inclui 7 idiomas e contando. Além disso, muitas de nossas documentações têm [traduções mantidas ativamente](https://github.com/vuejs?utf8=%E2%9C%93&q=vuejs.org). Temos muito orgulho do alcance internacional do Vue, mas podemos fazer ainda melhor.

## Podemos mesmo traduzir a documentação do Vue 3?

Sim! Estamos prontos para a tradução!

## Como posso me envolver com traduções?

Digamos que você fale Esperanto e quer saber quais iniciativas de tradução já existem e como se envolver em uma delas, confira [esta _issue_ afixada](https://github.com/vuejs/docs-next/issues/478) que contém discussões ativas sobre as iniciativas de tradução da nova documentação pela comunidade.

### Como colaborar com a versão em português?

Mas se você está aqui, deve ser por falar português! Neste caso, para nos ajudar diretamente, interaja com a equipe em [nosso repositório de tradução](https://github.com/vuejs-br/docs-next).

::: tip
Problemas pontuais em traduções já realizadas, como erros de grafia pequenos ou frases confusas, podem ser reportados apenas [criando uma _issue_](https://github.com/vuejs-br/docs-next/issues/new). Deixe claro sobre qual página se refere o problema e, preferencialmente, ofereça uma sugestão para a correção desejada.
:::

Mas se você se sente à vontade para traduzir/revisar conteúdos inteiros por conta, os passos são:

- Verifique o que já está em [aberto nas _issues_](https://github.com/vuejs-br/docs-next/issues),
  - Se necessário, [crie uma _issue_](https://github.com/vuejs-br/docs-next/issues/new) cujo título contenha claramente o nome do arquivo alvo da tradução. Por exemplo: `Traduzir "guide/installation.md"`;
  - Na _issue_ alvo de seu trabalho, deixe um comentário avisando que está trabalhando nisso, para evitar trabalho duplicado por outros colaboradores.
- Faça um _fork_ do _branch_ `master` deste _repo_ para sua própria conta,
  - Faça as traduções/revisões no(s) arquivo(s) que se propôs, usando o editor que quiser;
  - Recomendamos fortemente que faça uma revisão geral antes de considerar concluído, de preferência usando um corretor gramatical acoplado ao seu editor.
- Ao finalizar, faça um _pull request_ para este _repo_ contendo o(s) arquivo(s) modificado(s) e **a descrição do _commit_ em inglês** (recomendação do _core team_),
  - Alguém da equipe de mantenedores do Vue.js Brasil vai avaliar seu _pull request_, eventualmente solicitar novos _commits_ se ainda precisar de algum ajuste e, depois de tudo pronto, liberar sua contribuição para o mundo.
- **Gostou e quer mais?** Não se esqueça de fazer um _pull_ das alterações mais recentes antes de recomeçar o processo, já que a documentação original em inglês sempre sofre alterações e frequentemente fazemos a sincronização com o [_upstream_](https://github.com/vuejs/docs-next) para manter tudo em dia.

::: warning
Se passar alguns dias desde que avisou sobre o trabalho que estava fazendo e não conseguir terminar, volte na _issue_ e avise a equipe: outras pessoas podem querer dar continuidade em seu trabalho! Neste caso, você poderá enviar um _pull request_ com a parte feita e deixar que outros façam novos _commits_ ali mesmo, até que o trabalho seja finalizado. Por favor, não abandone do nada o trabalho em um arquivo sem avisar ninguém, isso dificulta demais a organização do projeto!
:::

## Recomendações para qualidade da tradução

Para uma documentação padronizada e de qualidade, seguimos alguns princípios e recomendações gerais sobre **o que fazer**:

- Traduzir os comentários dos códigos-fonte, sejam eles _inline_ `//` ou de múltiplas-linhas `/* */`;
- Traduzir textos informativos dos códigos-fonte, por exemplo: `<div id="level-1">Nível 1</div>`;
- Seguir maiúsculas e minúsculas conforme o original em inglês sempre que possível, exceto quando existir claramente uma regra de notação diferente na língua portuguesa;
- Usar itálico em expressões sem tradução (por exemplo, _view layer_), exceto em nomes próprios grafados em maiúsculas (por exemplo, Internet);
- Utilizar alguma extensão de ortografia e gramática vinculada ao seu editor de código, para evitar que erros deste tipo sejam submetidos;
- Quando for submeter seu trabalho, sempre escreva a descrição do _commit_ em inglês (recomendação do _core team_).

E algumas recomendações sobre **o que não fazer**:

- Não traduzir nomes de variáveis, métodos, dados, _ids_, classes etc. nos códigos-fonte, ou seja, nada que afetaria a estrutura e a execução do código-fonte;
- Não grafar em itálico tipos de dados ou valores primitivos (como true, false, String, Object);
- Não modificar a estrutura dos arquivos `.md` traduzidos, ou seja, não mudar o local das quebras de linha, dos títulos, dos parágrafos, não remover quebras de linhha entre títulos e parágrafos, etc. Qualquer mudança neste sentido dificulta muito _merges_ futuros na hora de manter a documentação atualizada em relação à documentação em inglês.
- Não fazer _pull_ diretamente do [_upstream_](https://github.com/vuejs/docs-next), este repositório da tradução é que deve ser alvo do seu _fork_ pessoal;
- Não fazer _pull request_ diretamente para o [_upstream_](https://github.com/vuejs/docs-next), a tradução sempre acontece apenas neste repositório.

### Termos padronizados

Alguns termos recorrentes causam dúvidas aos novos colaboradores da tradução. Alguns são sempre traduzidos de certa forma, outros nunca são traduzidos. Veja como ficar dentro do padrão:

**Sempre traduzimos**

Devem ser traduzidos sempre da mesma maneira (a menos que sua tradução atrapalhe demais o contexto da frase, o que deve ser discutido na _issue_ correspondente):

- _Bundle_ = Pacote
- _Debug_ = Depuração
- _Handling_ = Manipulação
- _Event Listening_ = Escuta de Eventos
- _Render Function_ = Função de Renderização
- _Computed Properties_ = Dados Computados
- _Single-File Components_ = Componentes Single-File
- _Custom Elements_ = Elementos Personalizados
- _Performance_ = Desempenho
- _Watchers_ = Observadores
- _Under the hood_ = Em seu interior
- _Server-Side Rendering_ = Renderização no Lado do Servidor
- _Props_ = Propriedades
- _Hooks_ = Gatilhos
- _Bind_ = Vínculo ou Interligação

**Nunca traduzimos**

Atualmente, recomenda-se que os termos a seguir sejam mantidos em inglês, inclusive com a grafia em itálico ou sem itálico, conforme apresentado a seguir:

- _getter_
- _setter_
- _standalone_
- _runtime_
- _store_
- _scaffolding_
- _loader_
- _loop_
- _template_
- _wrapper_
- _hot-reload_
- _open-source_
- _framework_
- _issue_
- true
- false
- Number
- String
- Boolean
- Array

**Traduzir mas manter o original**

Em alguns casos, para facilitar a compreensão, tanto por desenvolvedores menos experientes quanto pelos mais experientes, alguns termos podem ser traduzidos mas com o original em inglês entre parênteses, por exemplo:

> A biblioteca principal é focada exclusivamente na camada visual (_view layer_) das aplicações.

Na dúvida, discuta na _issue_ correspondente ao arquivo que estiver trabalhando, para trocar opiniões com os mantenedores.

## Como escrever melhor?

O _core team_ criou um material vasto e interessante no [Guia de Escrita da Documentação](./writing-guide.md) para melhorar a escrita de conteúdos de documentação, o que também é muito relevante para que traduzir conteúdos. Passe um tempo por lá se inteirando das recomendações, certamente serão úteis para isto e para várias outras situações.

## Como discutir sobre a tradução?

Como indivíduos, estamos espalhados por aí entre as várias [comunidades Vue.js Brasil](https://github.com/vuejs-br/comunidades) existentes em várias plataformas/localidades. Pode procurar por qualquer mantenedor listado neste GitHub e vai acabar encontrando em algum (ou vários) destes locais.

Mas, para fins de organização da tradução, **não conduzimos discussões externas a este repositório**. Portanto, vale reforçar: se precisar conversar sobre a tradução, [crie uma _issue_](https://github.com/vuejs-br/docs-next/issues/new).
