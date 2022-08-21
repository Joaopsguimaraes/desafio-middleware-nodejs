# 💻 Sobre o desafio

Nesse desafio você irá trabalhar mais a fundo com middlewares no Express. Dessa forma você será capaz de fixar mais ainda os conhecimentos obtidos até agora. 

Para facilitar um pouco mais do conhecimento da regra de negócio, você irá trabalhar com a mesma aplicação do desafio anterior: uma aplicação para gerenciar tarefas (ou *todos*) mas com algumas mudanças.

Será permitida a criação de um usuário com `name` e `username`, bem como fazer o CRUD de *todos*:

- Criar um novo *todo*;
- Listar todos os *todos*;
- Alterar o `title` e `deadline` de um *todo* existente;
- Marcar um *todo* como feito;
- Excluir um *todo*;

Tudo isso para cada usuário em específico. Além disso, dessa vez teremos um plano grátis onde o usuário só pode criar até dez *todos* e um plano Pro que irá permitir criar *todos* ilimitados, isso tudo usando middlewares para fazer as validações necessárias.

## Middlewares da aplicação

**checksExistsUserAccount** 
```ts

function checksExistsUserAccount(request, response, next) {
  const { username } = request.headers;

  const userAlReadyExists = users.find((user) => user.username === username);

  if (!userAlReadyExists) {
    return response.status(404).json({ error: "User not found" });
  }

  request.user = userAlReadyExists;
  next();
}

```

**checksCreateTodosUserAvailability**
```ts
function checksCreateTodosUserAvailability(request, response, next) {
  const { user } = request;

  if (user.pro) {
    return next();
  } else if (!user.pro && user.todos.length + 1 <= 10) {
    return next();
  }

  return response.status(403).json({
    message: "You already have ten todos created. Please, change to pro Plan!",
  });
}
```
**checksTodoExists**
```ts
function checksTodoExists(request, response, next) {
  const { username } = request.headers;
  const { id: todoId } = request.params;

  const foundUserByUsername = users.find((user) => user.username === username);

  if (!foundUserByUsername) {
    return response.status(404).json({ error: "user not found" });
  }

  if (!validate(todoId)) {
    return response.status(400).json({ error: "Todo ID is not uuid validate" });
  }

  const foundUserByTodoId = foundUserByUsername.todos.find(
    (todo) => todo.id === todoId
  );

  if (!foundUserByTodoId) {
    return response.status(404).json({ error: "Todo don't found" });
  }

  request.todo = foundUserByTodoId;
  request.user = foundUserByUsername;

  return next();
}
```

**findUserById**
```ts
function findUserById(request, response, next) {
  const { id } = request.params;

  const userFoundById = users.find((user) => user.id === id);

  if (!userFoundById) {
    return response.status(404).json({ error: "User not found" });
  }

  request.user = userFoundById;
  next();
}
```

### Iniciando servidor

Para iniciar o servidor e ter acesso as rotas, execute no terminal:
```
npm run dev
yarn dev

```