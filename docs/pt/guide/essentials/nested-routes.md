# Rotas Aninhadas

Interfaces de usuário de aplicações reais geralmente são compostas de componentes que estão aninhados profundamente em múltiplos níveis. Também é muito comum que os segmentos de uma URL correspondam a uma certa estrutura de componentes aninhados, por exemplo:

```
/user/foo/profile                     /user/foo/posts
+------------------+                  +-----------------+
| User             |                  | User            |
| +--------------+ |                  | +-------------+ |
| | Profile      | |  +------------>  | | Posts       | |
| |              | |                  | |             | |
| +--------------+ |                  | +-------------+ |
+------------------+                  +-----------------+
```

Com o `vue-router`, é muito simples expressar esse relacionamento usando a configuração de rotas aninhadas.

Dada a aplicação que criamos no último capítulo:

``` html
<div id="app">
  <router-view></router-view>
</div>
```

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

Aqui `<router-view>` é a saída de alto nível. Ela renderiza o componente que corresponde à rota de alto nível. Semelhantemente, um componente renderizado também pode conter sua própria `<router-view>` aninhada. Por exemplo, se adicionarmos uma ao _template_ do componente `User`:

``` js
const User = {
  template: `
    <div class="user">
      <h2>User {{ $route.params.id }}</h2>
      <router-view></router-view>
    </div>
  `
}
```

Para renderizar componentes dentro desta saída anínhada, precisamos usar a opção `children` na configuração do construtor do `VueRouter`:

``` js
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // UserProfile será renderizado dentro da <router-view> do User
          // quando /user/:id/profile for correspondida
          path: 'profile',
          component: UserProfile
        },
        {
          // UserPosts será renderizado dentro da <router-view> do User
          // quando /user/:id/posts for correspondida
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
```

**Observe que caminhos aninhados que começam com `/` serão tratados como um caminho raíz. Isso permite que você aproveite o aninhamento de componentes sem precisar usar um URL aninhado.**

Como você pode ver a opção `children` é apenas outro Array de objetos de configuração de rotas, como o próprio `routes`. Portanto, você pode continuar aninhando _views_ o tanto que precisar.

Neste ponto, com a configuração acima, quando você visitar `/user/foo`, nada será renderizado dentro da saída de `User`, porque nenhuma sub-rota é correspondida. Talvez você queira renderizar algo lá. Nesse caso, você pode fornecer um caminho de sub-rota vazio:

``` js
const router = new VueRouter({
  routes: [
    {
      path: '/user/:id', component: User,
      children: [
        // UserHome será renderizado dentro da <router-view> de User
        // quando /user/:id for correspondida
        { path: '', component: UserHome },

        // ...outras sub-rotas
      ]
    }
  ]
})
```

Um exemplo funcional pode ser encontrado [aqui](https://jsfiddle.net/yyx990803/L7hscd8h/).
