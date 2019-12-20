# Passando _props_ para Componentes de Rota

Usar `$route` em seus componentes cria um acoplamento firme com a rota, o que limita a flexibilidade do componente, pois só poderá ser usado em certas URLs.

Para desacoplar esse componente do _router_ use a opção `props`:

**Em vez de acoplar ao `$route`:**

``` js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User }
  ]
})
```

**Desacople usando `props`**

``` js
const User = {
  props: ['id'],
  template: '<div>User {{ id }}</div>'
}
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User, props: true },

    // para rotas com views nomeadas, você precisa definir a opção `props` para cada view nomeada:
    {
      path: '/user/:id',
      components: { default: User, sidebar: Sidebar },
      props: { default: true, sidebar: false }
    }
  ]
})
```

Isso permite você usar o componente em qualquer lugar, tornando-o mais fácil de reusar e testar.

## Modo Boolean

Quando o valor `true` é atribuíto à `props`, os `route.params` serão atribuídos como _props_ do componente.

## Modo Object

Quando `props` é um objeto, isso será atribuído às _props_ do componente. Útil para quando as props são estáticas.

``` js
const router = new VueRouter({
  routes: [
    { path: '/promotion/from-newsletter', component: Promotion, props: { newsletterPopup: false } }
  ]
})
```

## Modo Function

Você pode criar uma função que retorna _props_. Isso permite converter parâmetros em outros tipos, combinar valores estáticos com valores baseados nas rotas e etc.

``` js
const router = new VueRouter({
  routes: [
    { path: '/search', component: SearchUser, props: (route) => ({ query: route.query.q }) }
  ]
})
```

A URL `/search?q=vue` irá passar `{query: 'vue'}` como _props_ para o componente `SearchUser`.

Tente manter a função `props` sem estado, pois ela é avaliada apenas em alterações de rota. User um componente _wrapper_ se você precisa do estado para definir as _props_, para que assim o vue possa reagir às alterações de estado.

Para uso avançado, veja o [exemplo](https://github.com/vuejs/vue-router/blob/dev/examples/route-props/app.js).
