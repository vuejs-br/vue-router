# Rotas Nomeadas

Em alguns casos, é mais conveniente identificar uma rota com um nome, especialmente ao vincular a uma rota ou ao executar navegações. Você pode dar um nome a uma `routes` nas opções de rotas ao criar a instância do roteador

``` js
const router = new VueRouter({
  routes: [
    {
      path: '/user/:userId',
      name: 'user',
      component: User
    }
  ]
})
```

Para vincular a uma rota nomeada, você pode passar um objeto para o componente `router-link` através da propriedade `to`:

``` html
<router-link :to="{ name: 'user', params: { userId: 123 }}">User</router-link>
```

Esse é exatamente o mesmo objeto usado programaticamente com `router.push()`:

``` js
router.push({ name: 'user', params: { userId: 123 }})
```

Em ambos os casos, o roteador navegará para o caminho `/user/123`.

[Exemplo completo](https://github.com/vuejs/vue-router/blob/dev/examples/named-routes/app.js).
