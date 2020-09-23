# Tratamento de casos extremos

> Essa página pressupõe que você leu os [Componentes Básicos](component-basics.md). Leia isso primeiro se você for novo em componentes.

:::tip Nota
Todos os recursos dessa página documentam o tratamento de casos extremos, ou seja, situações incomuns que às vezes exigem que as regras do Vue sejam um pouco distorcidas. Observe, entretanto, que todos eles têm desvantagens ou situações onde podem ser perigosos. Eles são notados em cada caso, então lembre-se deles ao decidir usar cada recurso.
:::

## Controle de Atualizações

Graças ao sistema de reatividade do Vue, ele sempre sabe quando atualizar (se você usá-lo corretamente). No entanto, há casos extremos onde você talvez queira forçar uma atualização, apesar do fato de que nenhum dado reativo foi alterado. Também há casos onde você talvez queira prevenir atualizações desnecessárias.

### Forçando uma Atualização

Se você precisar forçar uma atualização no Vue, em 99.99% dos casos, você cometeu um erro em algum lugar. Por exemplo, você pode estar contando com um estado que não é monitorado pelo sistema de reatividade do Vue, por exemplo, com a propriedade `data` adicionada após a criação do componente.

No entanto, se você descartou as opções acima e se encontra nessa situação extremamente rara de ter que forçar manualmente uma atualização, você pode fazer isso com [`$forceUpdate`](../api/instance-methods.html#forceupdate).

### Componentes estáticos baratos com `v-once`

Renderizar elementos HTML simples é muito rápido no Vue, mas às vezes você pode ter um componente que contém **muito** conteúdo estático. Nesses casos, você pode garantir que ele seja avaliado somente uma vez e então armazená-lo em cache adicionando a diretiva `v-once` ao elemento raiz, como este:

```js
app.component('terms-of-service', {
  template: `
    <div v-once>
      <h1>Terms of Service</h1>
      ... muito conteúdo estático ...
    </div>
  `,
})
```

:::tip
Mais uma vez, tente não abusar desse padrão. Embora seja conveniente nos raros casos em que você precisa renderizar muito conteúdo estático, isso simplesmente não é necessário, a menos que você realmente observe uma renderização lenta - além disso, pode causar muita confusão posteriormente. Por exemplo, imagine outro desenvolvedor que não está familiarizado com o `v-once` ou simplesmente não o veja no template. Eles podem passar horas tentando descobrir porque o template não está sendo atualizado corretamente.
:::
