# Buscando Dados

Em alguns casos, você precisa buscar dados do servidor quando uma rota é ativada. Por exemplo, antes de renderizar um perfil de usuário, você precisar buscar os dados daquele usuário no servidor. Podemos conseguir isso de duas maneiras diferentes:

- **Buscando Após a Navegação**: realiza a navegação primeiro, e busca os dados no próximo gatilho de ciclo de vida do componente. Mostrar um estado de carregamento enquanto os dados estiverem sendo buscados.

- **Buscando Antes da Navegação**:  Busca os dados antes da navegação no protetor de navegação, e realiza a navegação após os dados terem sido buscados.

Tecnicamente, ambas as escolhas são válidas - fundamentalmente, depende da experiência de usuário que você está almejando.

## Buscando Após da Navegação

Quando utilizamos esta abordagem, navegamos e renderizamos o componente recebido imediatamente, e buscamos os dados no gatilhos `created` do componente. Isso nos dá a oportunidade de mostrar um estado de carregamento enquanto os dados estão sendo buscados através da rede, e podemos também, lidar com o carregamento de forma diferente para cada camada visual.

Vamos supor que temos um componente `Post`, que precisa buscar os dados de uma postagem baseado em `$route.params.id`:

``` html
<template>
  <div class="post">
    <div v-if="loading" class="loading">
      Loading...
    </div>

    <div v-if="error" class="error">
      {{ error }}
    </div>

    <div v-if="post" class="content">
      <h2>{{ post.title }}</h2>
      <p>{{ post.body }}</p>
    </div>
  </div>
</template>
```

``` js
export default {
  data () {
    return {
      loading: false,
      post: null,
      error: null
    }
  },
  created () {
    // busca os dados quando a camada de visualização é criada
    // e os dados já estão sendo observados
    this.fetchData()
  },
  watch: {
    // chama novamente o método se a rota mudar
    '$route': 'fetchData'
  },
  methods: {
    fetchData () {
      this.error = this.post = null
      this.loading = true
      // substitui `getPost` pelo seu utilitário de busca de dados
      // / encapsulamento de API
      getPost(this.$route.params.id, (err, post) => {
        this.loading = false
        if (err) {
          this.error = err.toString()
        } else {
          this.post = post
        }
      })
    }
  }
}
```

## Buscando Antes da Navegação

Com esta abordagem, buscamos os dados antes da navegação para a nova rota. Podemos realizar a busca dos dados no protetor `beforeRouteEnter` do componente recebido, e só chamar `next` quando a busca estiver completa:

``` js
export default {
  data () {
    return {
      post: null,
      error: null
    }
  },
  beforeRouteEnter (to, from, next) {
    getPost(to.params.id, (err, post) => {
      next(vm => vm.setData(err, post))
    })
  },
  // quando a rota mudar e este componente já estiver renderizado,
  // a lógica será ligeiramente diferente.
  beforeRouteUpdate (to, from, next) {
    this.post = null
    getPost(to.params.id, (err, post) => {
      this.setData(err, post)
      next()
    })
  },
  methods: {
    setData (err, post) {
      if (err) {
        this.error = err.toString()
      } else {
        this.post = post
      }
    }
  }
}
```

O usuário permanecerá na camada de visualização anterior enquanto os recursos estão sendo buscados pela próxima camada de visualização. Logo, é recomendado mostrar uma barra de progresso ou algum tido de indicador enquanto os dados estão sendo buscados. Se a busca dos dados falhar, também é necessário mostrar algum tido de mensagem de alerta global.
