# Atributos Especiais

## key

- **Esperado:** `number | string`

  O atributo especial `key` é usado principalmente como uma dica para o algoritmo DOM virtual do Vue para identificar VNodes ao comparar a nova lista de nós com a lista antiga. Sem as chaves, o Vue usa um algoritmo que minimiza o movimento do elemento e tenta corrigir/reutilizar elementos do mesmo tipo no local, tanto quanto possível. Com as chaves, ele reordenará os elementos com base na alteração da ordem das chaves, e os elementos com chaves que não estão mais presentes sempre serão removidos/destruídos.

  Filhos do mesmo pai comum devem ter **chaves únicas**. Chaves duplicadas causarão erros de renderização.

  O caso de uso mais comum é combinado com `v-for`:

  ```html
  <ul>
    <li v-for="item in items" :key="item.id">...</li>
  </ul>
  ```

  Também pode ser usado para forçar a substituição de um elemento/componente em vez de reutilizá-lo. Isso pode ser útil quando você deseja:

   - Acionar corretamente ganchos de ciclo de vida de um componente
   - Transições de gatilho

  Por exemplo:

  ```html
  <transition>
    <span :key="text">{{ text }}</span>
  </transition>
  ```

  Quando o `texto` muda, o `<span>` sempre será substituído ao invés de corrigido, então uma transição será disparada.

## ref

- **Esperado:** `string | Function`

  `ref` é usado para registrar uma referência a um elemento ou componente filho. A referência será registrada no objeto `$refs` do componente pai. Se usado em um elemento DOM simples, a referência será esse elemento; se usado em um componente filho, a referência será a instância do componente:

  ```html
  <!-- vm.$refs.p será o nó DOM -->
  <p ref="p">Olá</p>

  <!-- vm.$refs.child será a instância do componente filho -->
  <child-component ref="child"></child-component>

  <!-- Quando vinculado dinamicamente, podemos definir "ref" como uma função de "callback"(retorno de chamada), passando o elemento ou instância do componente explicitamente -->
  <child-component :ref="(el) => child = el"></child-component>
  ```

  Uma observação importante sobre o tempo de registro de uma "ref": como as próprias refs são criadas como resultado da função de renderização, você não pode acessá-los na renderização inicial - eles ainda não existem! `$refs` também não é reativo, portanto, você não deve tentar usá-lo em modelos para vinculação de dados.

- **Veja também:** [Refs de Componente Filho](../guide/component-template-refs.html)

## is

- **Esperado:** `string | Object (objeto de opções do componente)`

Usado para [componentes dinâmicos](../guide/component-dynamic-async.html).

Por exemplo:

```html
<!-- componente muda quando currentView muda -->
<component :is="currentView"></component>
```

- **Veja também:**
  - [Componentes Dinâmicos](../guide/component-dynamic-async.html)
  - [Advertências de análise de modelo DOM](../guide/component-basics.html#dom-template-parsing-caveats)
