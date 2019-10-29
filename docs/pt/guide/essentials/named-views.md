# _Views_ Nomeadas

Às vezes, você precisa exibir várias _views_ ao mesmo tempo, em vez de aninhá-las, e.g. criar um _layout_ com uma _view_ `sidebar` e uma _view_ `main`. É aqui que as _views_ nomeadas são úteis. Em vez de ter uma saída única em sua visualização, você pode ter múltiplas e dar a cada uma delas um nome. Um `router-view` sem nome terá o nome `default`.

``` html
<router-view class="view one"></router-view>
<router-view class="view two" name="a"></router-view>
<router-view class="view three" name="b"></router-view>
```

Uma _view_ é renderizada usando um componente, portanto, múltiplas _views_ requerem múltiplos componentes para a mesma rota. Certifique-se de usar a opção `components` (com um s).

``` js
const router = new VueRouter({
  routes: [
    {
      path: '/',
      components: {
        default: Foo,
        a: Bar,
        b: Baz
      }
    }
  ]
})
```

Um exemplo funcional disto pode ser visto [aqui](https://jsfiddle.net/posva/6du90epg/).

## _Views_ Nomeadas Aninhadas

É possível criar _layouts_ complexos usando _views_ nomeadas com _views_ aninhadas. Ao fazer isso, você precisará nomear também os componentes aninhados do `router-view`. Vejamos um exemplo de painel de Configurações:

```
/settings/emails                                       /settings/profile
+-----------------------------------+                  +------------------------------+
| UserSettings                      |                  | UserSettings                 |
| +-----+-------------------------+ |                  | +-----+--------------------+ |
| | Nav | UserEmailsSubscriptions | |  +------------>  | | Nav | UserProfile        | |
| |     +-------------------------+ |                  | |     +--------------------+ |
| |     |                         | |                  | |     | UserProfilePreview | |
| +-----+-------------------------+ |                  | +-----+--------------------+ |
+-----------------------------------+                  +------------------------------+
```

- `Nav` é só um componente regular
- `UserSettings` é o component da _view_
- `UserEmailsSubscriptions`, `UserProfile`, `UserProfilePreview` são componentes de _view_ aninhados

**Nota**: _Vamos esquecer como o HTML/CSS deve parecer para representar esse layout e focar nos componentes utilizados._

A seção `<template>` do componente `UserSettings` no _layout_ acima poderia parecer com algo assim:

```html
<!-- UserSettings.vue -->
<div>
  <h1>User Settings</h1>
  <NavBar/>
  <router-view/>
  <router-view name="helper"/>
</div>
```

_Os componetes de view aninhados estão omitidos, mas você pode encontrar o código-fonte completo para o exempo acima [aqui](https://jsfiddle.net/posva/22wgksa3/)._

Você pode alcançar o _layout_ acima com a seguinte configuração de rota:

```js
{
  path: '/settings',
  // Você também pode ter views nomeadas na parte superior
  component: UserSettings,
  children: [{
    path: 'emails',
    component: UserEmailsSubscriptions
  }, {
    path: 'profile',
    components: {
      default: UserProfile,
      helper: UserProfilePreview
    }
  }]
}
```

Uma demonstração funcional desse exemplo pode ser vista [aqui](https://jsfiddle.net/posva/22wgksa3/).
