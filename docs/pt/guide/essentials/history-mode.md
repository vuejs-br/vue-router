# Modo History do HTML5

O `vue-router` é configurado com o _modo hash_ como padrão - ele usa a hash da URL para simular uma URL completa, assim a página não é recarregada quando a URL muda.

Para remover a hash nós podemos usar o **modo history**, que aproveita a API `history.pushState` para fazer a navegação via URL sem recarregar a página:

``` js
const router = new VueRouter({
  mode: 'history',
  routes: [...]
})
```

Ao usar o modo history a URL parecerá "normal", e.g. `http://nossosite.com/usuario/id`. Lindo!

Mas existe um problema: como nossa aplicação é uma SPA (single page application), sem uma configuração de servidor apropriada os usuários receberão um erro 404 se eles acessarem `http://nossosite.com/usuario/id` diretamente em seus navegadores. E isso não é legal.

Não se preocupe: para resolver esse problema tudo que você precisa fazer é adicionar uma simples rota de fallback. Se a URL não bater com nenhum recurso estático, ela deve servir o mesmo `index.html` que abriga a sua aplicação. Lindo de novo!

## Exemplos de configuração de servidor

#### Apache

```apache
<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteBase /
  RewriteRule ^index\.html$ - [L]
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule . /index.html [L]
</IfModule>
```

No lugar de `mod_rewrite`, você pode usar [`FallbackResource`](https://httpd.apache.org/docs/2.2/mod/mod_dir.html#fallbackresource).

#### nginx

```nginx
location / {
  try_files $uri $uri/ /index.html;
}
```

#### Node.js nativo

```js
const http = require('http')
const fs = require('fs')
const httpPort = 80

http.createServer((req, res) => {
  fs.readFile('index.htm', 'utf-8', (err, content) => {
    if (err) {
      console.log('Não foi possível abrir o arquivo "index.htm".')
    }

    res.writeHead(200, {
      'Content-Type': 'text/html; charset=utf-8'
    })

    res.end(content)
  })
}).listen(httpPort, () => {
  console.log('Servidor rodando em: http://localhost:%s', httpPort)
})
```

#### Express com Node.js

Com Node.js/Express, considere usar o middleware [connect-history-api-fallback](https://github.com/bripkens/connect-history-api-fallback).

#### Internet Information Services (IIS)

1. Instale o [IIS UrlRewrite](https://www.iis.net/downloads/microsoft/url-rewrite)
2. Crie um arquivo `web.config` no diretório raíz da sua aplicação com o seguinte código:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Handle History Mode and custom 404/500" stopProcessing="true">
          <match url="(.*)" />
          <conditions logicalGrouping="MatchAll">
            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
            <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
          </conditions>
          <action type="Rewrite" url="/" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

#### Caddy

```
rewrite {
    regexp .*
    to {path} /
}
```

#### Firebase hosting

Adicione isto ao seu `firebase.json`:

```
{
  "hosting": {
    "public": "dist",
    "rewrites": [
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  }
}
```

## Limitação

Existe uma limitação nesse processo: seu servidor não vai mais reportar erros 404, pois todas as rotas não encontradas agora redirecionam para o seu arquivo `index.html`. Para resolver esse problema você deve implementar uma rota na sua aplicação Vue para mostrar a página de erro 404:

``` js
const router = new VueRouter({
  mode: 'history',
  routes: [
    { path: '*', component: NotFoundComponent }
  ]
})
```

Se estiver usando um servidor Node.js, você pode implementar uma função que use o roteador no lado do servidor para combinar a URL enviada e responder com 404 se nenhuma rota for encontrada. Leia a documentação do [Vue server side rendering](https://ssr.vuejs.org/en/)
