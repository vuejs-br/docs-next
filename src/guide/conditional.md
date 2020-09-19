# Renderização Condicional

## `v-if`

A diretiva `v-if` é usada para renderizar condicionalmente um bloco. O bloco só será renderizado se a expressão da diretiva retornar um valor verdadeiro.

```html
<h1 v-if="awesome">Vue é incrível!</h1>
```

Também é possível adicionar um "bloco senão" usando `v-else`:

```html
<h1 v-if="awesome">Vue é incrível!</h1>
<h1 v-else>Oh não 😢</h1>
```

### Grupos Condicionais com `v-if` em `<template>`

Como `v-if` é uma diretiva, então deve ser anexado a um único elemento. E se quisermos alternar mais de um elemento? Nesse caso podemos usar `v-if` em um elemento `<template>`, que serve como um invólucro invisível. O resultado final renderizado não incluirá o elemento `<template>`.

```html
<template v-if="ok">
  <h1>Título</h1>
  <p>Parágrafo 1</p>
  <p>Parágrafo 2</p>
</template>
```

### `v-else`

É possível utilizar a diretiva `v-else` para indicar um "bloco _else_" para `v-if`:

```html
<div v-if="Math.random() > 0.5">
  Agora você me vê
</div>
<div v-else>
  Agora você não me vê
</div>
```

Um elemento `v-else` deve vir imediatamente após um elemento `v-if` ou `v-else-if`, caso contrário não será reconhecido.

### `v-else-if`

O `v-else-if`, como o nome sugere, serve como um "bloco _else if_" ao `v-if`. Ele pode, inclusive, ser encadeado várias vezes:

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Não A, B ou C
</div>
```

Similar ao `v-else`, um `v-else-if` deve vir imediatamente após um elemento `v-if` ou um elemento `v-else-if`.

## `v-show`

Outra opção para exibir condicionalmente um elemento é a diretiva `v-show`. A utilização é basicamente a mesma.

```html
<h1 v-show="ok">Olá!</h1>
```

A diferença é que um elemento com `v-show` sempre será renderizado e permanecerá no DOM; `v-show` simplesmente alterna a propriedade CSS `display` do elemento.

`v-show` não suporta o elemento `<template>`, nem funciona com `v-else`.

## `v-if` vs `v-show`

`v-if` é a renderização condicional "real", pois garante que eventos e componentes filhos dentro do bloco condicional sejam devidamente destruídos e recriados durante a alternância.

`v-if` também é **preguiçoso**: se a condição for _false_ na renderização inicial, nada será feito - o bloco condicional não será renderizado até que a condição se torne _true_ pela primeira vez.

Em comparação, `v-show` é mais simples - o elemento sempre será renderizado independentemente da condição inicial, com alternância baseada em CSS.

De modo geral, `v-if` tem custo maior nas alternâncias de renderização, enquanto que `v-show` tem custo maior na renderização inicial. Então prefira `v-show` se precisar alternar a visibilidade de algo com muita frequência, e prefira `v-if` se não é muito provável que a condição se altere durante a execução.

## `v-if` with `v-for`

::: tip Note
Usar `v-if` e `v-for` juntos **não é recomendado**. Veja o [guia de estilos](../style-guide/#avoid-v-if-with-v-for-essential) para mais informações.
:::

Quando usado em conjunto com `v-if`, `v-for` possui maior prioridade do que `v-if`. Veja o [guia renderização de listas](list#v-for-with-v-if) para mais detalhes.
