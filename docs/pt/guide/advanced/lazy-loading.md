# Rotas de Carregamento Preguiçoso

Quando construímos aplicações com um empacotador, o pacote JavaScript pode ficar um pouco grande, e por tanto, afetar o tempo de carregamento da página. Seria mais eficiente se pudéssemos dividir cada componente de cada rota em um pedaço separado, e só carregá-los quando a rota for visitada.

Combinando a [funcionalidade componentes assíncronos](https://br.vuejs.org/v2/guide/components-dynamic-async.html#Componentes-Assincronos) do Vue e a [funcionalidade _code splitting_](https://webpack.js.org/guides/code-splitting-async/) do webpack, é trivial carregar preguiçosamente os componentes de rotas.

Primeiro, um componente assíncrono pode ser definido como uma função de fábrica que retorne uma _Promise_ (que deve se resolver para o próprio componente):

``` js
const Foo = () => Promise.resolve({ /* definição do componente */ })
```

Em seguida, em webpack 2, podemos utilizar a sintaxe de [importação dinâmica](https://github.com/tc39/proposal-dynamic-import) para indicar um ponto de divisão de código:

``` js
import('./Foo.vue') // retorna uma Promise
```

::: tip Nota
se você estiver utilizando Babel, você precisará adicionar o plugin [syntax-dynamic-import](https://babeljs.io/docs/plugins/syntax-dynamic-import/) para que o Babel possa analizar a sintaxe.
:::

Combinando esses dois passos, é assim que definimos um componente assíncrono que será automaticamente dividido pelo webpack:

``` js
const Foo = () => import('./Foo.vue')
```

Nada precisa ser modificado na configuração da rota, apenas utilize `Foo` normalmente:

``` js
const router = new VueRouter({
  routes: [
    { path: '/foo', component: Foo }
  ]
})
```

## Agrupando Componentes no Mesmo Pedaço

As vezes, queremos agrupar todos os componentes aninhados sob a mesma rota no mesmo pedaço assíncrono. Para tal, precisamos utilizar [pedaços nomeados](https://webpack.js.org/guides/code-splitting-async/#chunk-names) providenciando um nome a um pedaço usando uma sintaxe espececial de comentários (requer webpack > 2.4):

``` js
const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')
const Bar = () => import(/* webpackChunkName: "group-foo" */ './Bar.vue')
const Baz = () => import(/* webpackChunkName: "group-foo" */ './Baz.vue')
```

O webpack vai agrupar qualquer módulo assíncrono com o mesmo nomde de pedaço no mesmo pedaço assíncrono.
