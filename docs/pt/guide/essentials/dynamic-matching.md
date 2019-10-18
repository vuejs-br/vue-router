# Correspondência Dinâmica de Rotas

Muitas vezes, precisaremos mapear rotas com determinado padrão para o mesmo componente. Por exemplo, podemos ter um componente `User` que deve ser renderizado para todos os usuários, porém com _IDs_ de usuário diferentes. No `vue-router` podemos usar um segmento dinâmico no caminho para atingir isso:

```js
const User = {
  template: '<div>User</div>'
}

const router = new VueRouter({
  routes: [
    // segmentos dinâmicos iniciam com dois-pontos
    { path: '/user/:id', component: User }
  ]
})
```

Agora URL's como `/user/foo` e `/user/bar` irão ambas mapear para a mesma rota.

Um segmento dinâmico é denotado por dois-pontos `:`. Quando uma rota é correspondida, os valores dos segmentos dinâmicos serão expostos como `this.$route.params` em cada componente.
Portanto, podemos renderizar o ID do usuário atual atualizando o _template_ de `User`'s para isso:

```js
const User = {
  template: '<div>User {{ $route.params.id }}</div>'
}
```

Você pode ver o exemplo ao vivo [aqui](https://jsfiddle.net/yyx990803/4xfa2f19/).

Você pode ter múltiplos segmentos dinâmicos na mesma rota, e eles serão mapeados para os campos correspondentes em `$route.params`. Exemplos:

| padrão                        | caminho correspondido | \$route.params                         |
| ----------------------------- | --------------------- | -------------------------------------- |
| /user/:username               | /user/evan            | `{ username: 'evan' }`                 |
| /user/:username/post/:post_id | /user/evan/post/123   | `{ username: 'evan', post_id: '123' }` |

Além do `$route.params`, o objeto `$route` também expõe outras informações úteis como `$route.query` (se existir uma consulta na URL), `$route.hash` e etc. Você pode verificar todos os detalhes na [Referência da API](../../api/#the-route-object).

## Reagindo à Mudança de Parâmetros

Uma coisa a se notar ao usar rotas com parâmetros é que quanto o usuário navega de `/user/foo` para `/user/bar`, **a mesma instância do componente será reutilizada**.
Como as duas rotas renderizam o mesmo componente, isso é mais eficiente do que destruir a instância antiga e criar uma nova. **Entretanto, isso também significa que os _lifecycle hooks_ do componente não serão chamados**.

Para reagir à mudança de parâmetro no mesmo componente, você pode simplesmente monitorar o objeto `$route`:

```js
const User = {
  template: '...',
  watch: {
    $route(to, from) {
      // reage a mudanças de rota...
    }
  }
}
```

Ou, usar o _[navigation guard](../advanced/navigation-guards.html)_ `beforeRouteUpdate` introduzido em 2.2:

```js
const User = {
  template: '...',
  beforeRouteUpdate (to, from, next) {
    // reage a mudanças de rota...
    // não se esqueca de chamar next()
  }
}
```

## Pegar tudo / 404 Rota não encontrada

Parâmetros regulares só corresponderão a caracteres entre os fragmentos da URL separados por `/`. Se quisermos identificar **qualquer coisa**, podemos usar o asterisco (`*`):

```js
{
  // irá corresponder à qualquer coisa
  path: '*'
}
{
  // irá corresponder à qualquer coisa que inicie com `/user-`
  path: '/user-*'
}
```

Ao usar rotas _asterisco_, certifique-se de ordená-las corretamente, de forma que as que contenham _asteriscos_ fiquem no fim.
A rota `{ path: '*' }` é comumente usada para o erro 404 no lado do cliente. Se estiver usando o _History mode_, tenha certeza de [configurar seu servidor corretamente](./history-mode.md) também.

Ao usar um _asterisco_, um parâmetro chamado `pathMatch` é adicionado automaticamente ao `$route.params`. Ele contém o resto da url correspondida pelo _asterisco_:

```js
// Dada um rota { path: '/user-*' }
this.$router.push('/user-admin')
this.$route.params.pathMatch // 'admin'

// Dada uma rota { path: '*' }
this.$router.push('/non-existing')
this.$route.params.pathMatch // '/non-existing'
```

## Padrões de Correspondência Avançados

O `vue-router` usa o [path-to-regexp](https://github.com/pillarjs/path-to-regexp/tree/v1.7.0) como mecanismo de correspondência de caminho, portanto suporta muitos padrões de correspondência avançados, como segmentos dinâmicos opcionais, zero ou mais / um ou mais requisitos, e até mesmo padrões de regex customizados. Veja a [documentação](https://github.com/pillarjs/path-to-regexp/tree/v1.7.0#parameters) deles para esses padrões avançados, e [esse examplo](https://github.com/vuejs/vue-router/blob/dev/examples/route-matching/app.js) de como usá-los no `vue-router`.

## Prioridade de Correspondência

Algumas vezes, a mesma URL pode ser correspondida por diversas rotas. Neste caso, a prioridade da correspondência é determinada pela ordem da definição da rota: quanto antes uma rota é definida, maior é a sua prioridade.
