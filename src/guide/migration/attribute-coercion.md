---
badges:
  - Urgente
---

# Comportamento de Coerção de Atributo <MigrationBadges :badges="$frontmatter.badges" />

::: Informação:
Esta é uma alteração de baixo nível da API interna e não afeta a maioria dos desenvolvedores.
:::

## Visão Geral

Aqui está um resumo de alto nível das mudanças:

- Abandone o conceito interno de atributos enumerados e trate esses atributos da mesma forma que atributos normais não booleanos
- **URGENTE**: Não remova mais o atributo se o valor for booleano `false`. Em vez disso, é definido como attr="false". Para remover o atributo, use `null` ou `undefined`.

Para mais informações, continue lendo!

## Sintaxe 2.x

No 2.x, tínhamos as seguintes estratégias para forçar os valores `v-bind`:

- Para alguns pares de atributo/elemento, Vue está sempre usando o atributo IDL correspondente (propriedade): [como `valores` de `<input>`, `<select>`, `<progress>`, etc](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/util/attrs.js#L11-L18).

- Para "[atributos booleanos](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/util/attrs.js#L33-L40)" e [xlinks](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/util/attrs.js#L44-L46), o Vue os remove se forem "falsos" ([`undefined`, ` null` ou `false`](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/util/attrs.js#L52-L54)) e os adiciona de outra forma (veja [aqui](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/runtime/modules/attrs.js#L66-L77) e [aqui](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/runtime/modules/attrs.js#L81-L85)).

- Para "[atributos enumerados](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/util/attrs.js#L20)" (atualmente `contenteditable`, `draggable` e `spellcheck`), Vue tenta [coagir](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/util/attrs.js#L24-L31) eles para string (com tratamento especial para `contenteditable` por enquanto, para corrigir o problema [vuejs/vue#9397](https://github.com/vuejs/vue/issues/9397)).

- Para outros atributos, removemos valores "falsos" (`undefined`, `null` ou `false`) e definimos outros valores como estão (veja [aqui](https://github.com/vuejs/vue/blob/bad3c326a3f8b8e0d3bcf07917dc0adf97c32351/src/platforms/web/runtime/modules/attrs.js#L92-L113)).

A tabela a seguir descreve como o Vue coage os "atributos enumerados" de maneira diferente com atributos normais não booleanos:

| Vinculando a expressão  | `foo` <sup>normal</sup> | `draggable` <sup>enumerado</sup> |
| ------------------- | ----------------------- | --------------------------------- |
| `:attr="null"`      | /                       | `draggable="false"`               |
| `:attr="undefined"` | /                       | /                                 |
| `:attr="true"`      | `foo="true"`            | `draggable="true"`                |
| `:attr="false"`     | /                       | `draggable="false"`               |
| `:attr="0"`         | `foo="0"`               | `draggable="true"`                |
| `attr=""`           | `foo=""`                | `draggable="true"`                |
| `attr="foo"`        | `foo="foo"`             | `draggable="true"`                |
| `attr`              | `foo=""`                | `draggable="true"`                |

Podemos ver na tabela acima, a implementação atual força `true` para `'true'`, mas remove o atributo se for `false`. Isso também levou à inconsistência e obrigou os usuários a forçar manualmente os valores booleanos para string em casos de uso muito comuns, como atributos `aria-*` como `aria-selected`,` aria-hidden`, etc.

## Sintaxe 3.x

Pretendemos abandonar esse conceito interno de "atributos enumerados" e tratá-los como atributos HTML não booleanos normais.

- Isso resolve a inconsistência entre os atributos normais não booleanos e "atributos enumerados"
- Também torna possível usar valores diferentes de `'true'` e `' false'`, ou mesmo palavras-chave ainda por vir, para atributos como `contenteditable`

Para atributos não booleanos, o Vue irá parar de removê-los se eles forem `falsos` e forçá-los a serem `'falsos'`.

- Isso resolve a inconsistência entre `true` e `false`, e torna a saída dos atributos `aria-*` mais fácil

A tabela a seguir descreve o novo comportamento:

| Vinculando a expressão  | `foo` <sup>normal</sup>    | `draggable` <sup>enumerado</sup> |
| ------------------- | -------------------------- | --------------------------------- |
| `:attr="null"`      | /                          | / <sup>†</sup>                    |
| `:attr="undefined"` | /                          | /                                 |
| `:attr="true"`      | `foo="true"`               | `draggable="true"`                |
| `:attr="false"`     | `foo="false"` <sup>†</sup> | `draggable="false"`               |
| `:attr="0"`         | `foo="0"`                  | `draggable="0"` <sup>†</sup>      |
| `attr=""`           | `foo=""`                   | `draggable=""` <sup>†</sup>       |
| `attr="foo"`        | `foo="foo"`                | `draggable="foo"` <sup>†</sup>    |
| `attr`              | `foo=""`                   | `draggable=""` <sup>†</sup>       |

<small>†: modificado</small>

A coerção para atributos booleanos não foi alterada.

## Estratégia de Migração

### Atributos enumerados

A ausência de um atributo enumerado e `attr="false"` pode produzir diferentes valores de atributo IDL (que refletirão o estado real), descritos a seguir:

| Atributo enumerado ausente | Atributo IDL & valor                     |
| ---------------------- | ------------------------------------ |
| `contenteditable`      | `contentEditable` &rarr; `'inherit'` |
| `draggable`            | `draggable` &rarr; `false`           |
| `spellcheck`           | `spellcheck` &rarr; `true`           |

Para manter o comportamento antigo funcionando, e como estaremos coagindo `false` para `'false'`, no 3.x, os desenvolvedores Vue precisam fazer com que a expressão `v-bind` seja resolvida para `false` ou `'false'` para `contenteditable` e `spellcheck`.

No 2.x, os valores inválidos foram forçados a `'true'` para atributos enumerados. Isso geralmente não era intencional e improvável de ser invocado em grande escala. Em 3.x `true` ou `'true'` deve ser explicitamente especificado.

### Coagir `false` para` 'false'` em vez de remover o atributo

No 3.x, `null` ou `undefined` devem ser usados explicitamente para remover um atributo.

### Comparação entre o comportamento 2.x e 3.x

<table>
  <thead>
    <tr>
      <th>Atributo</th>
      <th> valor <code>v-bind</code> <sup>2.x</sup></th>
      <th> valor <code>v-bind</code> <sup>3.x</sup></th>
      <th>Saída HTML</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3">2.x “Atributos enumerados”<br><small>ou seja: <code>contenteditable</code>, <code>draggable</code> e <code>spellcheck</code>.</small></td>
      <td><code>undefined</code>, <code>false</code></td>
      <td><code>undefined</code>, <code>null</code></td>
      <td><i>removido</i></td>
    </tr>
    <tr>
      <td>
        <code>true</code>, <code>'true'</code>, <code>''</code>, <code>1</code>,
        <code>'foo'</code>
      </td>
      <td><code>true</code>, <code>'true'</code></td>
      <td><code>"true"</code></td>
    </tr>
    <tr>
      <td><code>null</code>, <code>'false'</code></td>
      <td><code>false</code>, <code>'false'</code></td>
      <td><code>"false"</code></td>
    </tr>
    <tr>
      <td rowspan="2">Outros atributos não booleanos<br><small>por exemplo: <code>aria-checked</code>, <code>tabindex</code>, <code>alt</code>, etc.</small></td>
      <td><code>undefined</code>, <code>null</code>, <code>false</code></td>
      <td><code>undefined</code>, <code>null</code></td>
      <td><i>removido</i></td>
    </tr>
    <tr>
      <td><code>'false'</code></td>
      <td><code>false</code>, <code>'false'</code></td>
      <td><code>"false"</code></td>
    </tr>
  </tbody>
</table>
