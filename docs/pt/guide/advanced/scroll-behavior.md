# Comportamento de Rolagem

Ao usar o roteamento do lado do cliente, pode ser conveniente rolar para o topo da página ao navegar para uma nova rota ou preservar a posição de rolagem das entradas do hitórico, assim como o recarregamento real de páginas. O `vue-router` permite isso e até mais, permite você personalizar completamente o comportamento de rolagem na navegação de rota.

**Nota: essa _feature_ só funciona se o navegador suportar o `history.pushState`.**

Ao criar a intância do _router_, você pode prover a função `scrollBehavior`:

``` js
const router = new VueRouter({
  routes: [...],
  scrollBehavior (to, from, savedPosition) {
    // retorna a posição desejada
  }
})
```

A função `scrollBehavior` recebe os objetos de rota `to` e `from`. O terceiro argumento, `savedPosition` só fica disponível se for uma navegação `popstate` (acionada pelos botões voltar/avançar do navegador).

A função pode retornar um objeto de posição de rolagem. O objeto pode ser da seguinte forma:

- `{ x: number, y: number }`
- `{ selector: string, offset? : { x: number, y: number }}` (_offset_ suportado somente em 2.6.0+)


Se um valor falso ou um objeto vazio é retornado, nenhuma rolagem ocorrerá.

Por exemplo:

``` js
scrollBehavior (to, from, savedPosition) {
  return { x: 0, y: 0 }
}
```

Isso irá simplesmente levar a rolagem para o topo em todas as navegações de rota.

Retornar `savedPosition` resultará em um comportamento semelhante ao nativo do botões voltar/avançar:

``` js
scrollBehavior (to, from, savedPosition) {
  if (savedPosition) {
    return savedPosition
  } else {
    return { x: 0, y: 0 }
  }
}
```

Se você quiser simular o comportamento "rolar para âncora":

``` js
scrollBehavior (to, from, savedPosition) {
  if (to.hash) {
    return {
      selector: to.hash
      // , offset: { x: 0, y: 10 }
    }
  }
}
```

Podemos também usar os [meta campos de rota](meta.md) para implementar o controle de comportamento de rolagem refinada. Veja o exemplo completo [aqui](https://github.com/vuejs/vue-router/blob/dev/examples/scroll-behavior/app.js).

## Rolagem Assíncrona

> Novo na 2.8.0

Você também pode retornar uma Promise que resolve para o descritor da posição desejada:

``` js
scrollBehavior (to, from, savedPosition) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({ x: 0, y: 0 })
    }, 500)
  })
}
```

É possível associar isso a eventos de um componente de transição no nível da página, para fazer com que o comportamento da rolagem seja reproduzido juntamente com as transições da página, mas devido à possíveis variações e complexidades nos casos de uso, simplesmente fornecemos essa primitiva para permitir implementações específicas de cada usuário.
