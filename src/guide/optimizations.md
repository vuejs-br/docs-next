# Mecanismos de Renderização e Otimização

> Essa página não é uma leitura necessária para aprender como usar o Vue de forma correta, mas fornece mais informações, caso você fique curioso em como o trabalho de renderização funciona por debaixo do capô.

## DOM Virtual

Agora que nós sabemos como os observadores atualizam os componentes, você deve se perguntar como essas mudanças eventualmente alteram o DOM! Possivelmente você já ouviu falar sobre o DOM Virtual anteriormente, muitos *frameworks* inclusive o Vue usam esse paradigma para ter certeza que suas interfaces refletem as alterações que nós atualizamos no JavaScript efetivamente.

<div class="reactivecontent">
  <iframe height="500" style="width: 100%;" scrolling="no" title="Como o DOM Virtual funciona?" src="https://codepen.io/sdras/embed/RwwQapa?height=500&theme-id=light&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
    Veja a caneta <a href='https://codepen.io/sdras/pen/RwwQapa'>Como o DOM Virtual funciona?</a> por Sarah Drasner
    (<a href='https://codepen.io/sdras'>@sdras</a>) em <a href='https://codepen.io'>CodePen</a>.
  </iframe>
</div>

Nós fazemos uma cópia do DOM no JavaScript chamada DOM Virtual, nós fazemos isso porque alterar o DOM com JavaScript é computacionalmente caro. Enquanto efetuar atualizações no JavaScript é barato, encontrando os nós necessários do DOM e os atualizando com JS é caro. Então nós colocamos as chamadas em lote, e fazemos a mudança no DOM todas de uma única vez.

O DOM Virtual é um leve objeto em JavaScript, criado pela função de renderização. Ele recebe três argumentos: o elemento, o objeto com a informação, propriedades, atributos e mais, e uma Array. A Array é onde nós passamos o filho, no qual tem todos esses argumentos também, e então eles podem ter filhos e assim por diante, até que nós criamos uma árvore cheia de elementos.

Se nós precisarmos atualizar a lista de itens, nós também fazemos no JavaScript, utilizando a reatividade que mencionamos anteriormente. Nós fazemos todas as alterações na cópia do JavaScript, o DOM Virtual, e executamos a diferença entre esse e o DOM atual. Apenas então, fazemos nossas atualizações apenas no que foi alterado. O DOM Virtual nos permite fazer atualizações performáticas em nossas interfaces de usuário (UIs)!.
