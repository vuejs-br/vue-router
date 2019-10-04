# Começando

::: tip Nota
Nós usaremos o [ES2015](https://github.com/lukehoban/es6features) nos exemplos de código deste guia.

Além disso, todos os exemplos usarão a versão completa do Vue para possibilitar a compilação dos _templates_ em tempo real. Veja mais detalhes [aqui](https://vuejs.org/v2/guide/installation.html#Runtime-Compiler-vs-Runtime-only).
:::

Criar uma _Single-page Application_ com o Vue + Vue Router é simples. Com Vue.js, nos estamos compondo nossa aplicação com componentes. Quando adicionamos o Vue Router à mistura, tudo o que precisamos fazer é mapear nossos componentes para as rotas e informar ao Vue Router onde renderizá-los. Veja um exemplo básico:

## HTML

```html
<script src="https://unpkg.com/vue/dist/vue.js"></script>
<script src="https://unpkg.com/vue-router/dist/vue-router.js"></script>

<div id="app">
  <h1>Hello App!</h1>
  <p>
    <!-- use o componente router-link para navegação. -->
    <!-- especifique o link passando a prop `to`. -->
    <!-- o `<router-link>` será renderizado como uma tag `<a>` por padrão -->
    <router-link to="/foo">Vá para Foo</router-link>
    <router-link to="/bar">Vá para Bar</router-link>
  </p>
  <!-- saída da rota -->
  <!-- o componente correspondente à rota será renderizado aqui -->
  <router-view></router-view>
</div>
```

## JavaScript

```js
// 0. Caso use um sistema de módulos (e.g. via vue-cli), importe o Vue e o VueRouter
// e então chame `Vue.use(VueRouter)`.

// 1. Defina os compomentes das rotas.
// Estes podem ser importados de outros arquivos
const Foo = { template: '<div>foo</div>' }
const Bar = { template: '<div>bar</div>' }

// 2. Defina algumas rotas
// Cada rota deve ser mapeada para um componente. O "componente" pode
// ser um construtor de componente real, criado via
// `Vue.extend ()`, ou apenas um objeto de opções de componente.
// Falaremos sobre rotas aninhadas mais tarde.
const routes = [
  { path: '/foo', component: Foo },
  { path: '/bar', component: Bar }
]

// 3. Crie a instância do router e passe a opção `routes`
// Você pode passar algumas opções adicionais aqui, mas vamos
// manter isso simples por enquanto.
const router = new VueRouter({
  routes // atalho para `routes: routes`
})

// 4. Crie e monte a instância raiz.
// Certifique-se de injetar o router na opção router para tornar
// todo o app compatível com ele.
const app = new Vue({
  router
}).$mount('#app')

// Agora a palicação foi iniciada!

```

Com a injeção do _router_, nós ganhamos acesso à ele através de `this.$router`, assim como à rota atual com `this.$route` dentro de qualquer componente:

```js
// Home.vue
export default {
  computed: {
    username() {
      // Veremos o que `params` é em breve
      return this.$route.params.username
    }
  },
  methods: {
    goBack() {
      window.history.length > 1 ? this.$router.go(-1) : this.$router.push('/')
    }
  }
}
```

Ao longo da documentação, as vezes utilizaremos a instância do `router`. Lembre-se que usar `this.$router` é exatamente o mesmo que usar o `router`. A razão pela qual usamos `this.$router` é porque nós não queremos importar o _router_ em cada componente que precise manipular o roteamento.

Você pode conferir esse exemplo [ao vivo](https://jsfiddle.net/yyx990803/xgrjzsup/).

Veja que um `<router-link>` recebe a classe `.router-link-active` automaticamente quando a rota alvo é correspondida. Você pode aprender mais sobre isso na  [referência da API](../api/#router-link) dele.
