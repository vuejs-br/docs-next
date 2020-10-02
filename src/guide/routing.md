# Roteamento

## Roteador oficial 

Para a maioria das aplicações de página única _(Single Page Application)_, é recomendado utilizar a [biblioteca vue-router](https://github.com/vuejs/vue-router) oficial. Para mais detalhes, veja a [documentação](https://router.vuejs.org/) do _vue-router_.

## Roteamento básico do zero

Se você só precisa de um roteamento simples e não tem interesse em utilizar uma biblioteca robusta de roteamento, você pode o fazer dinamicamente, renderizando um componente no nível da página _(page-level component)_, desse jeito: 

```js
const NotFoundComponent = { template: '<p>Page not found</p>' }
const HomeComponent = { template: '<p>Home page</p>' }
const AboutComponent = { template: '<p>About page</p>' }

const routes = {
  '/': HomeComponent,
  '/about': AboutComponent
}

const SimpleRouter = {
  data: () => ({
    currentRoute: window.location.pathname
  }),

  computed: {
    CurrentComponent() {
      return routes[this.currentRoute] || NotFoundComponent
    }
  },

  render() {
    return Vue.h(this.CurrentComponent)
  }
}

Vue.createApp(SimpleRouter).mount('#app')
```

Combinado com a [History API](https://developer.mozilla.org/en-US/docs/Web/API/History_API/Working_with_the_History_API), você poderá construir um roteador básico (porém poderoso) no lado do cliente (client-side router). Para ver isso na prática, acesse [esse app de exemplo](https://github.com/phanan/vue-3.0-simple-routing-example).

## Integrando com roteadores externos

Se você preferir utilizar um roteador de terceiros _(3rd-party router)_, como por exemplo [Page.js](https://github.com/visionmedia/page.js) ou [Director](https://github.com/flatiron/director), a integração é [igualmente simples](https://github.com/phanan/vue-3.0-simple-routing-example/compare/master...pagejs). Segue aqui [um exemplo completo](https://github.com/phanan/vue-3.0-simple-routing-example/tree/pagejs) utilizando `Page.js`.
