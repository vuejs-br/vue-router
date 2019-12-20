# Transições

Dado que o `<router-view>` é essencialmente um componente dinâmico, podemos aplicar efeitos de transição à ele da mesma maneira usando o componente `<transition>`:

``` html
<transition>
  <router-view></router-view>
</transition>
```

[Todas as APIs de transição](https://vuejs.org/guide/transitions.html) funcionam da mesma forma aqui.

## Transição por Rota

A utilização acima aplicará a mesma transição para todas as rotas. Se você deseja que cada componente de rota tenha uma transição diferente, você pode usar `<transition>` com diferentes nomes dentro de cada componente de rota:

``` js
const Foo = {
  template: `
    <transition name="slide">
      <div class="foo">...</div>
    </transition>
  `
}

const Bar = {
  template: `
    <transition name="fade">
      <div class="bar">...</div>
    </transition>
  `
}
```

## Transição Dinâmica Baseada em Rota

Também é possível determinar a transição a ser usada dinâmicamente, baseando-se na relação entre a rota alvo e a rota atual:

``` html
<!-- use um nome de transição dinâmico -->
<transition :name="transitionName">
  <router-view></router-view>
</transition>
```

``` js
// então, no componente pai,
// observe `$route` para determinar a transição a ser utilizada
watch: {
  '$route' (to, from) {
    const toDepth = to.path.split('/').length
    const fromDepth = from.path.split('/').length
    this.transitionName = toDepth < fromDepth ? 'slide-right' : 'slide-left'
  }
}
```

Veja o exemplo completo [aqui](https://github.com/vuejs/vue-router/blob/dev/examples/transitions/app.js).
