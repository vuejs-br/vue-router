# Redirecionamento e Alias

## Redirecionamento

O redirecionamento é também feito na configuração `routes`. Para redirecionar de `/a` para `/b`:

``` js
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: '/b' }
  ]
})
```

O redirecionamento também pode ter como alvo uma rota nomeada:

``` js
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: { name: 'foo' }}
  ]
})
```

Ou até mesmo usar uma função para redirecionar dinâmicamente:

``` js
const router = new VueRouter({
  routes: [
    { path: '/a', redirect: to => {
      // a função recebe a rota alvo como argumento
      // e retorna o caminho/local aqui.
    }}
  ]
})
```

Observe que os [Protetores de Navegação](../advanced/navigation-guards.md) não são aplicados na rota que redireciona, somente na rota alvo. No exemplo abaixo, adicionar o protetor `beforeEnter` à rota `/a` não terá nenhum efeito.

Para outros usos avançados, cofira o [exemplo](https://github.com/vuejs/vue-router/blob/dev/examples/redirect/app.js).

## Alias

Um redirecionamento significa que quando um usuário visita `/a`, a URL será trocada por `/b`, e então correspondida como `/b`. Mas o que é um alias?

**Um alias de `/a` como `/b` significa que quando o usuário visitar `/b`, a URL se mantém `/b`, mas irá ser correspondida como se o usuário estivesse visitando `/a`.**

O que foi dito acima pode ser expressado na configuração de rota como:

``` js
const router = new VueRouter({
  routes: [
    { path: '/a', component: A, alias: '/b' }
  ]
})
```

Um alias oferece a liberdade de mapear uma estrutura de interface do usuário para uma URL arbitrária, em vez de ser restringida pela estrutura de aninhamento da configuração.

Para uso avançado, confira o [exemplo](https://github.com/vuejs/vue-router/blob/dev/examples/route-alias/app.js).
