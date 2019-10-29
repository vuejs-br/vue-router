---
sidebarDepth: 0
---

# Navegação programática

Além de usar <router-link> para criar tags de âncora para navegação declarativa, também podemos fazer isso de forma programática usando os métodos de instância do roteador.

## `router.push(location, onComplete?, onAbort?)`

**Nota: Dentro de uma instância do Vue, você tem acesso à instância do roteador com `$router`. Portanto, você pode chamar `this.$router.push`.**

Para navegar para um URL diferente, use `router.push`. Esse método coloca uma nova entrada no stack histórico, para que quando o usuário clicar no botão voltar do navegador, ele será levado para o URL anterior.

Esse é o método chamado internamente quando você clica em um `<router-link>`, ; portanto, clicar em `<router-link :to="...">` é o equivalente de chamar `router.push(...)`.

| Declarativo               | Programático       |
| ------------------------- | ------------------ |
| `<router-link :to="...">` | `router.push(...)` |

O argumento pode ser uma rota com tipo de string ou um objeto. Exemplos:

```js
// string exata
router.push('home')

// objeto
router.push({ path: 'home' })

// rota nomeada
router.push({ name: 'user', params: { userId: '123' } })

// com consulta, resultando em /register?plan=private
router.push({ path: 'register', query: { plan: 'private' } })
```

**Nota**: `params` são ignorados se `path` for fornecido, o que não é o caso com `query`, conforme mostrado no exemplo acima. Em vez disso, você precisa fornecer o `name` da rota ou especificar manualmente o `path` enteiro com qualquer parâmetro:

```js
const userId = '123'
router.push({ name: 'user', params: { userId } }) // -> /user/123
router.push({ path: `/user/${userId}` }) // -> /user/123
// Isso NÃO vai funcionar
router.push({ path: '/user', params: { userId } }) // -> /user
```

As mesmas regras se aplicam à propriedade `to` do componente `router-link`.

Na versão 2.2.0+, opcionalmente pode fornecer `onComplete` e `onAbort` callbacks para `router.push` ou `router.replace` como o 2º e o 3º argumentos. Esses callbacks serão chamados quando a navegação for concluída com sucesso (após todos os hooks assíncronos terem sido resolvidos) ou abortada (navegou para a mesma rota ou para uma rota diferente antes da navegação atual terminar), respectivamente.
Na versão 3.1.0+, você pode omitir o 2º e o 3º parâmetro e `router.push`/`router.replace` retornará uma promessa se Promessas forem suportadas.

**Nots:** Se o destino for o mesmo da rota atual e apenas os parâmetros estiverem mudando (por exemplo, passando de um perfil para outro `/users/1` -> `/users/2`), é necessário usar [`beforeRouteUpdate`](./dynamic-matching.md#reacting-to-params-changes) para reagir às alterações (por exemplo, para buscar as informações do usuário).

## `router.replace(location, onComplete?, onAbort?)`

Age como `router.push`, a única diferença é que navega sem adicionar uma nova entrada no histórico do navegador, como o nome sugere - substitui a entrada atual.

| Declarativo                       | Programático          |
| --------------------------------- | --------------------- |
| `<router-link :to="..." replace>` | `router.replace(...)` |

## `router.go(n)`

Esse método usa um integer como parâmetro que indica por quantas etapas avançar ou retroceder no stack histórico, similar a `window.history.go(n)`.

Exemplos

```js
// avança por um registro, o mesmo que history.forward()
router.go(1)

// volta por um registro, o mesmo que history.back()
router.go(-1)

// avança 3 registros
router.go(3)

// falha silenciosamente se não h registros suficientes.
router.go(-100)
router.go(100)
```

## Manipulação do Histórico

Você deve ter notado que `router.push`, `router.replace` e `router.go` are são equivalentes a [`window.history.pushState`, `window.history.replaceState` and `window.history.go`](https://developer.mozilla.org/en-US/docs/Web/API/History), e imitam os APIs de `window.history`.

Portanto, se você já conhece [Browser History APIs](https://developer.mozilla.org/en-US/docs/Web/API/History_API), manipular o histórico será super fácil com o Vue Router.

Vale mencionar que os métodos de navegação do Vue Router (`push`, `replace`, `go`) funcionam de forma consistente em todos os modos do roteador (`history`, `hash`, e `abstract`).
