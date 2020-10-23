---
badges:
  - breaking
---

# Atributo `key` <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

- **NOVO:** `key`s não são mais necessárias em _branches_ `v-if`/`v-else`/`v-else-if`, já que o Vue agora gera automaticamente `key`s únicas.
  - **BREAKING:** Se você informar `key`s manualmente, então cada _branch_ deve ter uma `key` única. Você não pode mais usar intencionalmente a mesma `key` para forçar reuso de _branch_.
- **BREAKING:** `key`s de `<template v-for>` devem ser colocadas na tag `<template>` (ao invés de em seus filhos).

## Background

O atributo especial `key` é usado como uma dica para o algoritmo de DOM virtual do Vue rastrear a identidade de um nó. Dessa forma, o Vue sabe quando pode reutilizar e corrigir nós existentes e quando precisa reordená-los ou recriá-los. Para mais informações, olhe as seções a seguir:

- [Renderização de Listas: Manutenção de Estado](/guide/list.html#manutencao-de-estado)
- [Referência da API: Atributo Especial `key`](/api/special-attributes.html#key)

## Em _Branches_ Condicionais

No Vue 2.x, era recomendado o uso de `key`s nos _branches_ `v-if`/`v-else`/`v-else-if`.

```html
<!-- Vue 2.x -->
<div v-if="condition" key="yes">Sim</div>
<div v-else key="no">Não</div>
```

O exemplo acima ainda funciona no Vue 3.x. Porém, não é mais recomendado o uso do atributo `key` em _branches_ `v-if`/`v-else`/`v-else-if`, já que `key`s únicas são geradas automaticamente em _branches_ condicionais caso você não as informe.

```html
<!-- Vue 3.x -->
<div v-if="condition">Sim</div>
<div v-else>Não</div>
```

A grande mudança (breaking change) é que se você informar `key`s manualmente, cada branch deverá usar uma `key` única. Na maioria dos casos, você pode remover essas `key`s.

```html
<!-- Vue 2.x -->
<div v-if="condition" key="a">Sim</div>
<div v-else key="a">Não</div>

<!-- Vue 3.x (solução recomendada: remover as keys) -->
<div v-if="condition">Sim</div>
<div v-else>Não</div>

<!-- Vue 3.x (solução alternativa: garantir que as keys sejam sempre únicas) -->
<div v-if="condition" key="a">Sim</div>
<div v-else key="b">Não</div>
```

## Com `<template v-for>`

No Vue 2.x, uma tag `<template>` não poderia ter uma `key`. Em vez disso, você poderia colocar as `key`s em cada uma de suas tags filhas.

```html
<!-- Vue 2.x -->
<template v-for="item in list">
  <div :key="item.id">...</div>
  <span :key="item.id">...</span>
</template>
```

No Vue 3.x, a `key` deve ser colocada na tag `<template>`.

```html
<!-- Vue 3.x -->
<template v-for="item in list" :key="item.id">
  <div>...</div>
  <span>...</span>
</template>
```

Similarmente, quando houver uma tag `<template v-for>` com uma tag filha que use `v-if`, a `key` deve ser movida para a tag `<template>`.

```html
<!-- Vue 2.x -->
<template v-for="item in list">
  <div v-if="item.isVisible" :key="item.id">...</div>
  <span v-else :key="item.id">...</span>
</template>

<!-- Vue 3.x -->
<template v-for="item in list" :key="item.id">
  <div v-if="item.isVisible">...</div>
  <span v-else>...</span>
</template>
```
