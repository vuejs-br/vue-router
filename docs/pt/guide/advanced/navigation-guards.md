# Protetores de Navegação

Como o nome sugere, os protetores de navegação fornecidos pelo `vue-router` são usados primariamente para proteger navegações, redirecionando-as ou cancelando-as. Existem várias maneiras de se conectar ao processo de navegação de rotas: globalmente, _per-route_, ou _in-component_.

Lembre-se que **mudanças de parâmetros ou _querys_ não acionam os guardas de navegação de entrada/saída**. Como alternativa, você pode [observar o objeto `route`](../essentials/dynamic-matching.md#reacting-to-params-changes) para reagir à essas mudanças, ou usar o protetor _in-component_ `beforeRouteUpdate`.

## Protetores _Before_ Globais

Você pode registrar um protetor _berfore_ global usando `router.beforeEach`:

``` js
const router = new VueRouter({ ... })

router.beforeEach((to, from, next) => {
  // ...
})
```

Protetores _before_ globais são chamados por ordem de criação sempre que uma navegação é acionada. Os protetores podem ser resolvidos assincronamente, e a navegação fica **pendente** enquanto todos os _hooks_ são resolvidos.

Toda função de proteção recebe três argumentos:

- **`to: Route`**: o alvo [Objeto Route](../../api/#the-route-object) para onde será navegado.

- **`from: Route`**: a rota atual de onde se está saindo.

- **`next: Function`**: essa função precisa ser chamada para **resolver** o _hook_. A ação depende dos argumentos fornecidos para `next`:

  - **`next()`**: vá para o próximo _hook_ no _pipeline_. Se não há _hooks_ restantes, a navegação é **confirmada**.

  - **`next(false)`**: aborte a navegação atual. Se a URL do navegador foi modificada (seja manualmente pelo usuário ou via botão voltar), ele será reestabelecido para a rota presente em `from`.

  - **`next('/')` ou `next({ path: '/' })`**: redireciona para um local diferente. A navegação atual será abortada e uma nova será iniciada. Você pode passar qualquer objeto de localização para a `next`, o que permite você especificar opções como `replace: true`, `name: 'home'` e qualquer opção usada na [propriedade `to` do `router-link`](../../api/#to) ou [`router.push`](../../api/#router-push).

  - **`next(error)`**: (2.4.0+) se o argumento passado para `next` é uma instância de `Error`, a navegação será abortada e o erro será passado para as funções de _callback_ registradas via [`router.onError()`](../../api/#router-onerror).

**Certifique-se de sempre chamar a função `next`, caso contrário o _hook_ nunca será resolvido.**

## Protetores _Resolve_ Globais

Você pode registrar um protetor global com `router.beforeResolve`. Isso é similar ao `router.beforeEach`, com a diferença de que os protetores _resolve_ serão chamados logo antes da navegação ser confirmada, **após todos os protetores _in-component_ e todos os componentes de rota assíncronos serem resolvidos**.

## _Hooks After_ Globais

Você também pode registrar _hooks after_ globais, entretanto, diferentemente dos protetores, esse _hooks_ não recebem a função `next` e não podem afetar a navegação:

``` js
router.afterEach((to, from) => {
  // ...
})
```

## Protetores _Per-Route_

Você pode definir protetors `beforeEnter` diretamente em um objeto de configuração de rota:

``` js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```

Esses protetores tem exatamente a mesma assinatura de protetores _before_ globais.

## In-Component Guards

Finalmente, você pode definir diretamente protetores de navegação dentro de componentes de rotas (aqueles passados para a configuração de rota) com as seguintes opções:

- `beforeRouteEnter`
- `beforeRouteUpdate`
- `beforeRouteLeave`

``` js
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // chamado antes que a rota que renderiza o componente é confirmada.
    // NÃO possui acesso à instância `this` do componente.
    // porque ela ainda não foi criada quango o protetor é chamado!
  },
  beforeRouteUpdate (to, from, next) {
    // chamado quando a rota que renderiza esse componente muda,
    // mas esse componente é reutilizado na nova rota.
    // Por exemplo, para uma rota com parâmetro dinâmicos `/foo/:id`, quando
    // navegamos entre `/foo/1` e `/foo/2`, a mesma instância do componente `Foo`
    // será reutilizada, e esse hook será chamado.
    // possui acesso à instância `this` do componente.
  },
  beforeRouteLeave (to, from, next) {
    // chamado quando a rota que renderiza esse componente está prestes
    // a ser abandonada.
    // possui acesso à instância `this` do componente.
  }
}
```

O protetor `beforeRouteEnter` **NÃO** possui acesso ao `this`, porque o protetor é chamado antes da navegação ser confirmada, então o novo componente não foi sequer criado.

Entretanto, você pode acessar a instância passando uma função _callback_ para `next`. A _callback_ será chamada quando a navegação for confirmada, e a instância do componente será passada para a _callback_ como argumento:

``` js
beforeRouteEnter (to, from, next) {
  next(vm => {
    // acessa a instância do componente via `vm`
  })
}
```

Observe que o protetor `beforeRouteEnter` é o único que suporta a passagem de uma _callback_ para `next`. Para `beforeRouteUpdate` e `beforeRouteLeave`, o `this` já está disponível, então passar uma _callback_ é desnecessário e portanto *não suportado*:

```js
beforeRouteUpdate (to, from, next) {
  // apenas use `this`
  this.name = to.params.name
  next()
}
```

O **protetor _leave_** é geralmente utilizado para previnir o usuário de acidentalmente deixar a rota com edições não salvas. A navegação pode ser cancelada chamando `next(false)`.

```js
beforeRouteLeave (to, from, next) {
  const answer = window.confirm('Você realmente quer sair? Há mudanças não salvas!')
  if (answer) {
    next()
  } else {
    next(false)
  }
}
```

## O Fluxo Completo de Resolução de Navegação

1. Navegação acionada.
2. Chama os protetores `beforeRouteLeave` nos componentes desativados.
3. Chama os protetores globais `beforeEach`.
4. Chama os protetores `beforeRouteUpdate` em componentes reutilizados.
5. Chama `beforeEnter` nas configurações de rota.
6. Resolve os componentes de rota assíncronos.
7. Chama `beforeRouteEnter` nos componentes ativados.
8. Chama os protetores globais `beforeResolve`.
9. Confirma a navegação.
10. Chama os _hooks_ globais `afterEach`.
11. Aciona as atualizações do DOM.
12. Chama as _callbacks_ passadas para `next` nos protetores `beforeRouteEnter` com instâncias instanciadas.
