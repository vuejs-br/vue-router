# Meta Campos de Rota

Você pode incluir um campo `meta` ao definir uma rota:

``` js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      children: [
        {
          path: 'bar',
          component: Bar,
          // um campo meta
          meta: { requiresAuth: true }
        }
      ]
    }
  ]
})
```

Então como eu acesso esse campo `meta`?

Primeiro, cada objeto de rota na configurção `routes` é chamado de **registro de rota**. Registros de rota podem ser aninhados. Portanto, quando uma rota é correspondida, pode potencialmente corresponder a mais de um registro de rota.

Por exemplo, com a configuração de rota acima, a URL `/foo/bar` corresponderá a ambos, o registro de rota pai e o registro de rota filho.

Todos os registros de rotas correspondidos por uma rota são expostos no objeto `$route` (e também nos objetos de rota nos guardas de navegação) como o Array `$route.matched`. Portanto, iremos precisar iterar sobre `$route.matched` para checar pelos meta campos nos registros de rotas.

Veja um caso de uso de exemplo que checa por um meta campo no guarda de navegação global:

``` js
router.beforeEach((to, from, next) => {
  if (to.matched.some(record => record.meta.requiresAuth)) {
    // essa rota requer autenticação, verifique se está logado
    // se não, redirecione para a a rota de login.
    if (!auth.loggedIn()) {
      next({
        path: '/login',
        query: { redirect: to.fullPath }
      })
    } else {
      next()
    }
  } else {
    next() // certifique-se de sempre chamar o next()!
  }
})
```
