# Instalação

## Download Direto / CDN

[https://unpkg.com/vue-router/dist/vue-router.js](https://unpkg.com/vue-router/dist/vue-router.js)

<!--email_off-->
[Unpkg.com](https://unpkg.com) fornece links de CDN baseados em npm. O link acima irá sempre apontar para o último lançamento no npm. Você também pode usar uma versão/_tag_ específica via URLs como `https://unpkg.com/vue-router@2.0.0/dist/vue-router.js`.
<!--/email_off-->

Inclua o `vue-router` depois do Vue e ele se instalará automaticamente:

``` html
<script src="/path/to/vue.js"></script>
<script src="/path/to/vue-router.js"></script>
```

## npm

``` bash
npm install vue-router
```

Quando usado com um sistema de módulos, você precisa explicitamente instalar o _router_ via `Vue.use()`:

``` js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)
```

Você não precisa fazer isso quando estiver usando _tags_ de _script_ globais.

## Dev Build

Você precisará clonar diretamente do GitHub e compilar o `vue-router` por conta própria se
quiser usar a última versão de desenvolvimento.

``` bash
git clone https://github.com/vuejs/vue-router.git node_modules/vue-router
cd node_modules/vue-router
npm install
npm run build
```
