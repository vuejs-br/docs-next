# Lidando com Casos Extremos

<<<<<<< HEAD
> Esta página assume que você já leu o [Básico sobre Componentes](component-basics.md). Leia lá primeiro se o desenvolvimento de componentes for novidade para você.
=======
> This page assumes you've already read the [Components Basics](component-basics.md). Read that first if you are new to components.
>>>>>>> 84d3484c8edff3aff5e832d69ace073ea0e182a0

::: tip NOTA
Todos os recursos dessa página documentam o tratamento de casos extremos, ou seja, situações incomuns que às vezes exigem que as regras do Vue sejam levemente contornadas. Observe, entretanto, que todos eles têm desvantagens ou situações onde podem ser perigosos. Estes detalhes são expostos em cada caso, portanto, lembre-se ao decidir usar cada recurso.
:::

## Controlando Atualizações

Graças ao sistema de reatividade do Vue, ele sempre sabe quando atualizar a interface (se você usá-lo corretamente). Porém, há casos incomuns em que possa querer forçar uma atualização, mesmo que nenhum dado reativo tenha mudado. Também há casos em que possa querer prevenir atualizações desnecessárias.

### Forçando uma Atualização

Se você precisar forçar uma atualização no Vue, em 99,99% dos casos você cometeu um erro em algum lugar. Por exemplo, você pode estar contando com um estado que não é monitorado pelo sistema de reatividade do Vue, por exemplo, contando com uma propriedade `data` adicionada após a criação do componente.

No entanto, se você descartou a opção acima e se encontra nessa situação extremamente rara de ter que forçar manualmente uma atualização, você pode fazer isso com [`$forceUpdate`](../api/instance-methods.html#forceupdate).

### Componentes Estáticos Econômicos com `v-once`

Renderizar elementos HTML puros é algo bem rápido no Vue mas, algumas vezes, você pode ter um componente que possui **uma grande quantidade** de conteúdo estático. Nesses casos, você pode garantir que ele seja avaliado somente uma vez e então armazenado em cache, adicionando a diretiva `v-once` ao elemento raiz, como mostrado abaixo:

```js
app.component('terms-of-service', {
  template: `
    <div v-once>
      <h1>Termos de serviço</h1>
      ... muito conteúdo estático ...
    </div>
  `,
})
```

::: tip DICA
Como de costume, tente não abusar deste padrão. Embora seja conveniente nos raros casos em que você precisar renderizar muito conteúdo estático, isso simplesmente não é necessário a menos que você realmente observe uma renderização lenta - além disso, pode causar muita confusão posteriormente. Por exemplo, imagine outro desenvolvedor que não está familiarizado com o `v-once` ou simplesmente não o notou no _template_. Ele pode vir a perder horas tentando entender o porquê de o _template_ não estar sendo atualizado.
:::
