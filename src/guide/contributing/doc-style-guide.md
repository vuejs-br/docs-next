# Guia de Estilização da Documentação

Este guia fornece uma visão geral dos diferentes elementos de _design_ que estão disponíveis para uso durante a criação de documentação.

## Alertas

VuePress oferece um _plug-in_ de contêiner personalizado para blocos de alerta. Existem quatro tipos:

- **Informações**: Para exibir informações neutras
- **Dica**: Para exibir informações positivas e incentivadas
- **Aviso**: Para exibir informações que os usuários devem estar cientes, pois há um baixo a moderado risco
- **Perigo**: Para exibir informações negativas e/ou de alto risco para o usuário

**Markdown de Exemplo**

```
::: info
Você pode encontrar mais informações neste _site_.
:::

::: tip
Esta é uma ótima dica para se lembrar!
:::

::: warning
Isso é algo para ser cauteloso.
:::

::: danger DANGER
Isso é algo que não recomendamos. Use por sua conta e risco.
:::
```

**Resultado Renderizado**

::: info
Você pode encontrar mais informações neste _site_.
:::

::: tip
Esta é uma ótima dica para se lembrar!
:::

::: warning
Isso é algo para ser cauteloso.
:::

::: danger DANGER
Isso é algo que não recomendamos. Use por sua conta e risco.
:::

## Blocos de Código

VuePress usa o Prism para fornecer destaques de sintaxe de código-fonte (_syntax highlighting_), ao acrescentar a linguagem desejada após os três acentos graves de um bloco de código:

**Markdown de Exemplo**

````
```js
export default {
  name: 'MyComponent'
}
```
````

**Resultado Renderizado**

```js
export default {
  name: 'MyComponent'
}
```

### Destacando Linhas

Para adicionar destaques de linha aos blocos de código, você precisa acrescentar o número da linha entre chaves.

#### Linha Única

**Markdown de Exemplo**

````
```js{2}
export default {
  name: 'MyComponent',
  props: {
    type: String,
    item: Object
  }
}
```
````

**Resultado Renderizado**

```js{2}
export default {
  name: 'MyComponent',
  props: {
    type: String,
    item: Object
  }
}
```

#### Grupos de Linhas

**Markdown de Exemplo**

````
```js{4-5}
export default {
  name: 'MyComponent',
  props: {
    type: String,
    item: Object
  }
}
```
````

**Resultado Renderizado**

```js{4-5}
export default {
  name: 'MyComponent',
  props: {
    type: String,
    item: Object
  }
}
```

#### Múltiplas Seções

**Markdown de Exemplo**

````
```js{2,4-5}
export default {
  name: 'MyComponent',
  props: {
    type: String,
    item: Object
  }
}
```
````

**Resultado Renderizado**

```js{2,4-5}
export default {
  name: 'MyComponent',
  props: {
    type: String,
    item: Object
  }
}
```
