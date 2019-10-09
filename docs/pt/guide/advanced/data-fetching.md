# Buscando Dados

Em alguns casos, você precisa buscar dados do servidor quando uma rota é ativada. Por exemplo, antes de renderizar um perfil de usuário, você precisar buscar os dados daquele usuário no servidor. Podemos conseguir isso de duas maneiras diferentes:

- **Buscando Após a Navegação**: realiza a navegação primeiro, e busca os dados no próximo gatilho de ciclo de vida do componente. Mostrar um estado de carregamento enquanto os dados estiverem sendo buscados.

- **Buscando Antes da Navegação**:  Busca os dados antes da navegação no protetor de navegação, e realiza a navegação após os dados terem sido buscados.

Technically, both are valid choices - it ultimately depends on the user experience you are aiming for.

## Fetching After Navigation

When using this approach, we navigate and render the incoming component immediately, and fetch data in the component's `created` hook. It gives us the opportunity to display a loading state while the data is being fetched over the network, and we can also handle loading differently for each view.

Let's assume we have a `Post` component that needs to fetch the data for a post based on `$route.params.id`:

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
    // fetch the data when the view is created and the data is
    // already being observed
    this.fetchData()
  },
  watch: {
    // call again the method if the route changes
    '$route': 'fetchData'
  },
  methods: {
    fetchData () {
      this.error = this.post = null
      this.loading = true
      // replace `getPost` with your data fetching util / API wrapper
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

## Fetching Before Navigation

With this approach we fetch the data before actually navigating to the new
route. We can perform the data fetching in the `beforeRouteEnter` guard in the incoming component, and only call `next` when the fetch is complete:

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
  // when route changes and this component is already rendered,
  // the logic will be slightly different.
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

The user will stay on the previous view while the resource is being fetched for the incoming view. It is therefore recommended to display a progress bar or some kind of indicator while the data is being fetched. If the data fetch fails, it's also necessary to display some kind of global warning message.
