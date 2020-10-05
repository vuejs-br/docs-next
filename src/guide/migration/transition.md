---
title: Mudança das Classes de Transição
badges:
  - breaking
---

# {{ $frontmatter.title }} <MigrationBadges :badges="$frontmatter.badges" />

## Visão Geral

A classe de transição `v-enter` foi renomeada para `v-enter-from` e a classe de transição `v-leave` foi renomeada para `v-leave-from`.

## Sintaxe 2.x

Antes da v2.1.8, nós tinhamos duas classes de transição para cada direção da transição: estados inicial e ativo.

Na v2.1.8, nós introduzimos a `v-enter-to` para endereçar o intervalo de tempo entre as transições enter/leave. Entretanto, para compatibilidade com versões anteriores, o nome `v-enter` se manteve inalterado:

```css
.v-enter,
.v-leave-to {
  opacity: 0;
}

.v-leave,
.v-enter-to {
  opacity: 1;
}
```

Isso se tornou confuso, como _enter_ e _leave_ eram amplos e não utilizavam a mesma convenção de nome que suas classes de ligação com homólogas.

## Atualização 3.x

De forma a deixar mais explícito e legível, nós agora renomeamos essas classes de estado inicial:

```css
.v-enter-from,
.v-leave-to {
  opacity: 0;
}

.v-leave-from,
.v-enter-to {
  opacity: 1;
}
```

Agora é muito mais claro a diferença entre esses estados.

![Diagrama de Transição](/images/transitions.svg)

Os nomes de propriedades do componente `<transition>` relacionadas também foram alterados:

- `leave-class` é renomeada para `leave-from-class` (pode ser escrita como `leaveFromClass` em funções de renderização ou JSX)
- `enter-class` é renomeada para `enter-from-class` (pode ser escrita como `enterFromClass` em funções de renderização ou JSX)

## Estratégia de Migração

1. Substitua instâncias de `.v-enter` por `.v-enter-from`
2. Substitua instâncias de `.v-leave` por `.v-leave-from`
3. Substitua instâncias de nomes de propriedades relacionadas, como acima.