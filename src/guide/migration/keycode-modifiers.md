---
badges:
  - breaking
---

# Modificadores KeyCode <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

Aqui está um resumo rápido do que mudou:

- **BREAKING**: Usando números, i.e. keyCodes, como em modificadores `v-on` não é mais suportado
- **BREAKING**: `config.keyCodes` não é mais suportado

## Sintaxe v2.x

No Vue 2, `keyCodes` eram suportados como uma maneira de modificar um método `v-on`.

```html
<!-- versão usando keyCode -->
<input v-on:keyup.13="submit" />

<!-- versão usando apelido -->
<input v-on:keyup.enter="submit" />
```

Adicionalmente, você podia definir seus próprios apelidos através da configuração global `config.keyCodes`.

```js
Vue.config.keyCodes = {
  f1: 112
}
```

```html
<!-- versão usando keyCode -->
<input v-on:keyup.112="showHelpText" />

<!-- versão usando apelido customizado -->
<input v-on:keyup.f1="showHelpText" />
```

## Sintaxe v3.x

Uma vez que [`KeyboardEvent.keyCode` virou obsoleto](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/keyCode), não faria mais sentido para o Vue 3 continuar à suportá-lo também. Como resultado, agora é recomendado utilizar o nome em kebab-case para qualquer tecla que você utilizar como um modificador.

```html
<!-- Modificador de tecla do Vue 3 no v-on -->
<input v-on:keyup.delete="confirmDelete" />
```

Como um resultado, isso significa que `config.keyCodes` também está obsoleto e não será mais suportado.

## Estratégia de Migração

Para aqueles que utilizam `keyCode` em sua base de código, nós recomendamos convertê-los para os nomes kebab-cased equivalentes.
