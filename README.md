# Oi!
### Seja bem-vindo a mais um tutorial!
### Hoje vamos fazer algumas atividades, que explicarei de forma mais detalhada no final de cada arquivo. :)

![Tutorial](https://github.com/Edson-Edu/IA22-Cadastro-Login-Autenticacao/blob/main/img/logo.gif?raw=true)

## Autenticação de Usuários (Single Server)

Autenticação é o processo de verificar se alguém é quem diz ser.

### Autenticação vs Autorização

- **Autenticação:** "Você é quem você diz que é?"
- **Autorização:** "O que você pode fazer?"
# 

# Iniciando um Repositório no CodeSpace

### Crie um repositório com o nome "2024-IA24-Login-Autenticacao".
#### Lembre-se: o repositório deve ser público e com o arquivo README incluso.
![Imagem da criação do repositório](https://raw.githubusercontent.com/Edson-Edu/2024-IA22-2TRI/main/img/criar_repositorio.PNG)

### Agora, nesta tela, você vai clicar no botão `Code` > `Codespace on main` e depois no símbolo `+`.
![Imagem de seleção do Codespace](https://raw.githubusercontent.com/Edson-Edu/2024-IA22-2TRI/main/img/codespace.PNG)

### Espere a página carregar para continuar os próximos passos. Sua tela deverá estar assim:
![Tela inicial do CodeSpace](https://raw.githubusercontent.com/Edson-Edu/2024-IA22-2TRI/main/img/tela_inicial.PNG)


# Configuração do Projeto de Autenticação

Este guia irá ajudá-lo a configurar o ambiente de desenvolvimento para um projeto de autenticação com Node.js, Express, e TypeScript.

## Passos de Configuração

Siga os passos abaixo, executando os comandos no terminal um de cada vez:

1. **Inicialize o projeto Node.js:**
    ```bash
    npm init -y
    ```

2. **Instale as dependências principais:**
    ```bash
    npm install express cors sqlite3 sqlite
    ```

3. **Instale as dependências de desenvolvimento:**
    ```bash
    npm install --save-dev typescript nodemon ts-node @types/express @types/cors
    ```

4. **Inicialize o TypeScript no projeto:**
    ```bash
    npx tsc --init
    ```

5. **Crie as pastas necessárias para a organização do projeto:**
    ```bash
    mkdir src public src/services src/utils
    ```

6. **Crie os arquivos principais para o projeto:**
    ```bash
    touch src/app.ts src/database.ts public/index.html public/index2.css public/index.css public/greys.html public/main.js initial-users.json src/services/user.services.ts src/utils/addAliasDots.ts src/utils/index.ts
    ```
### Agora voce ja tem todos os arquivos necessarios para continuarmos nosso servidor de autenticação.

# Index.html
### Dentro da pasta ```public```, voce encontrara o arquivo ``index.html`` e adicione:

````html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Token Management</title>
  <link rel="stylesheet" href="index2.css">
  <script defer src=main.js></script>
</head>

<body>
  <h1>Token Management</h1>
  <p>Abra o console do navegador para ver os logs</p>
  <form>
    <div>
      <label for="username">Username:</label>
      <input type="text" id="username" name="username">
    </div>
    <div>
      <label for="password">Password:</label>
      <input type="password" id="password" name="password">
    </div>
    <button>Login</button>
  </form>

  <button class="get-token">Get Token User</button>
  <button class="get-users">Get Users</button>
  <button class="logout">Logout</button>

  <!-- pre é um elemento para textos fixos -->
  <pre> 
    ...
  </pre>


</body>

</html>
````

### Este arquivo esta criando a estrutura básica da página de autenticação.

# Main.js
### Dentro da pasta ```public```, voce encontrara o arquivo ``main.js`` e adicione:

````javascript 
const pre = document.querySelector('pre')

// LOGIN
const form = document.querySelector('form')
form.addEventListener('submit', async (event) => {
  event.preventDefault();
  const username = form.username.value
  const password = form.password.value
  const response = await fetch('/token', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ username, password })
  })
  const data = await response.json()
  pre.textContent = response.status + "\n" + JSON.stringify(data, null, 2)
  if (!response.ok) {
    console.error('Login failed', data)
    if (data.token)
      localStorage.setItem('token', data.token)
    alert('Login failed')
    return
  }

  console.log('Login success', data)
  window.location.href="greys.html";
 
  localStorage.setItem('token', data.token)

})

document.querySelector('button.get-token').addEventListener('click', async () => {
  const token = localStorage.getItem('token')
  const response = await fetch(`/token/${token}`)
  const data = await response.json()
  pre.textContent = response.status + "\n" + JSON.stringify(data, null, 2)
  if (!response.ok) {
    console.error('Token failed', data)
    alert('Token failed')
    return
  }
  console.log('Token success', data)
})

document.querySelector('button.get-users').addEventListener('click', async () => {
  const token = localStorage.getItem('token')
  const response = await fetch(`/users/${token}`)
  const data = await response.json()
  pre.textContent = response.status + "\n" + JSON.stringify(data, null, 2)
  if (!response.ok) {
    console.error('Get users failed', data)
    alert('Get users failed')
    return
  }
  console.log('Get users success', data)
})  

document.querySelector('button.logout').addEventListener('click', async () => {
  const token = localStorage.getItem('token')
  const response = await fetch(`/token/${token}`, { method: 'DELETE' })
  pre.textContent = response.status
  if (!response.ok) {
    console.error('Logout failed')
    alert('Logout failed')
    return
  }
  console.log('Logout success')
  localStorage.removeItem('token')
})
````
### Este arquivo está criando a funcionalidade principal para interagir com o servidor. Vou detalhar o que cada parte faz:

- **Selecionando Elementos**:
  - O elemento `<pre>` é selecionado para exibir os logs e respostas do servidor.

- **Login**:
  - Quando o formulário é enviado, a função `submit` é acionada. Ela coleta o nome de usuário e a senha, e envia uma solicitação `POST` para `/token` com essas informações.
  - Se o login for bem-sucedido, o token recebido é armazenado no `localStorage` e o usuário é redirecionado para `greys.html`.
  - Se o login falhar, uma mensagem de erro é exibida e o token (se presente) é removido.

- **Obter Token**:
  - O botão "Get Token User" envia uma solicitação `GET` para `/token/${token}`, onde `${token}` é o token armazenado no `localStorage`.
  - A resposta do servidor é exibida no elemento `<pre>`. Se a solicitação falhar, uma mensagem de erro é exibida.

- **Obter Usuários**:
  - O botão "Get Users" envia uma solicitação `GET` para `/users/${token}`, onde `${token}` é o token armazenado.
  - A lista de usuários é exibida no elemento `<pre>`. Se a solicitação falhar, uma mensagem de erro é exibida.

- **Logout**:
  - O botão "Logout" envia uma solicitação `DELETE` para `/token/${token}`, removendo o token do servidor.
  - O status da resposta é exibido no elemento `<pre>`. Se o logout falhar, uma mensagem de erro é exibida e o token é removido do `localStorage`.



# Greys.html
### Dentro da pasta ```public```, voce encontrara o arquivo ``greys.html`` e adicione:

````html
<!DOCTYPE html>
<html lang="pt-BR">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <link rel="stylesheet" href="index.css">
  <title>Grey's Anatomy</title>
</head>

<body>
  <div class="container">
    <h1>Grey's Anatomy</h1>
    <h3>Petição contra o fim da série</h3>


    <table>
      <thead>
        <tr>
          <th>Id</th>
          <th>Nome</th>
          <th>Email</th>
          <th>Ações</th>
        </tr>
      </thead>
      <tbody>
        <!--  -->
      </tbody>
    </table>
  </div>

  <script>
    const form = document.querySelector('form')
    const tbody = document.querySelector('tbody')

    async function fetchData() {
      const resp = await fetch('/users/' + localStorage.getItem('token'))
      const data = await resp.json()

      tbody.innerHTML = ''

      data.forEach(user => {
        const tr = document.createElement('tr')
        tr.innerHTML = `
          <td>${user.id}</td>
          <td>${user.name}</td>
          <td>${user.email}</td>
          <td>
            <button class="excluir">Excluir</button>
            <button class="editar">Editar</button>
          </td>
        `

        const btExcluir = tr.querySelector('button.excluir')
        const btEditar = tr.querySelector('button.editar')
        const token = localStorage.getItem('token');
        console.log(token);
        btExcluir.addEventListener('click', async () => {
          const resp = await fetch(`/users/${user.id}/${token}`, { method: 'DELETE' })
          if(resp.status == 401){
            alert("Voce nao tem permissão");
            return
          }
          tr.remove()
        })

        btEditar.addEventListener('click', async () => {
          const name = prompt('Novo nome:', user.name)
          const email = prompt('Novo email:', user.email)

          const resp =  await fetch(`/users/${user.id}/${token}`, {
            method: 'PUT',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ name, email })
          })

          if (resp.status == 401) {
            alert("Voce nao tem permissão")
            return 
          }

          fetchData()
        })

        tbody.appendChild(tr)
      })
    }

    fetchData()
  </script>
</body>

</html>
````

### Este arquivo está criando a interface para gerenciar os usuários após o login.

# Agora vamos estilizar as duas paginas

### Dentro da pasta ```public```, voce encontrara o arquivo ``index.css`` e adicione:

````css
html, body {
  height: 100%;
  margin: 0;
  padding: 0;
}

body {
  background-image: url(https://raw.githubusercontent.com/Edson-Edu/2024-IA22-2TRI/main/public/seattle.jpg);
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  color: #f5f5f5; 
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  text-align: center;
  line-height: 1.6;
  transition: background-color 0.3s ease;
}

.container {
  background: rgba(0, 0, 0, 0.75);
  border-radius: 15px; 
  padding: 30px;
  max-width: 800px;
  margin: 50px auto;
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.6);
  transition: background 0.3s ease, transform 0.3s ease, box-shadow 0.3s ease;
}

.container:hover {
  transform: scale(1.03); 
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.7); 
}

h1 {
  font-size: 2.8em;
  margin-bottom: 15px;
  color: #e0e0e0; 
  transition: color 0.3s ease;
}

h3 {
  font-size: 1.7em;
  margin-bottom: 25px;
  color: #b0b0b0; 
  transition: color 0.3s ease;
}

form {
  margin-bottom: 20px;
}

form input[type="text"],
form input[type="email"],
form input[type="password"] {
  padding: 12px;
  border: 1px solid #666;
  border-radius: 8px;
  margin: 8px;
  width: calc(50% - 24px);
  background-color: rgba(255, 255, 255, 0.15);
  color: #f5f5f5;
  transition: background-color 0.3s ease, border-color 0.3s ease, box-shadow 0.3s ease;
}

form input[type="text"]:focus,
form input[type="email"]:focus {
  background-color: rgba(255, 255, 255, 0.25); 
  border-color: #007BFF; 
  box-shadow: 0 0 8px rgba(0, 123, 255, 0.5); 
}

form button {
  padding: 12px 25px;
  border: none;
  border-radius: 8px;
  background-color: #007BFF;
  color: #fff;
  cursor: pointer;
  font-size: 1.1em;
  transition: background-color 0.3s ease, transform 0.3s ease, box-shadow 0.3s ease;
}

form button:hover {
  background-color: #0056b3;
  transform: scale(1.05); 
  box-shadow: 0 4px 12px rgba(0, 123, 255, 0.3);
}

table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 30px;
}

table th, table td {
  padding: 12px;
  border: 1px solid #555; 
  transition: background-color 0.3s ease, color 0.3s ease;
}

table th {
  background-color: rgba(0, 0, 0, 0.65);
  color: #e0e0e0;
}

table td {
  background-color: rgba(0, 0, 0, 0.55);
}

table button {
  padding: 6px 12px;
  border: none;
  border-radius: 8px;
  color: #fff;
  cursor: pointer;
  font-size: 0.95em;
  margin: 3px;
  transition: background-color 0.3s ease, transform 0.3s ease;
}

table button.excluir {
  background-color: #dc3545;
}

table button.excluir:hover {
  background-color: #c82333;
  transform: scale(1.05);
}

table button.editar {
  background-color: #28a745; 
}

table button.editar:hover {
  background-color: #218838;
  transform: scale(1.05); 
}

````

### Dentro da pasta ```public```, voce encontrara o arquivo ``index2.css`` e adicione:

````css
/* Global Styles */

* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

body {
    background-color: #f7f7f7;
    color: #333;
    line-height: 1.6;
}

h1 {
    text-align: center;
    margin-bottom: 30px;
    color: #333;
    font-size: 2.5em;
}

p {
    margin-bottom: 15px;
    color: #555;
    text-align: center;
}

/* Form Styles */

form {
    max-width: 350px;
    margin: 50px auto;
    padding: 25px;
    background-color: #ffffff;
    border: 1px solid #ddd;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

form div {
    margin-bottom: 25px;
}

label {
    display: block;
    margin-bottom: 8px;
    font-weight: bold;
    color: #333;
}

input[type="text"], input[type="password"] {
    width: 100%;
    height: 45px;
    padding: 10px;
    border: 1px solid #ccc;
    border-radius: 5px;
    font-size: 1em;
    color: #333;
}

input[type="text"]:focus, input[type="password"]:focus {
    border-color: #4CAF50;
    outline: none;
    box-shadow: 0 0 5px rgba(76, 175, 80, 0.2);
}

button[type="submit"] {
    background-color: #4CAF50;
    color: #fff;
    padding: 12px 24px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 1em;
}

button[type="submit"]:hover {
    background-color: #45a049;
}

/* Button Styles */

.get-token, .get-users, .logout {
    display: inline-block;
    margin-top: 15px;
    margin-right: 10px;
    padding: 12px 24px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 1em;
    color: #fff;
}

.get-token {
    background-color: #007bff;
}

.get-token:hover {
    background-color: #0056b3;
}

.get-users {
    background-color: #28a745;
}

.get-users:hover {
    background-color: #218838;
}

.logout {
    background-color: #dc3545;
}

.logout:hover {
    background-color: #c82333;
}

/* Pre Styles */

pre {
    background-color: #ffffff;
    padding: 15px;
    border: 1px solid #ddd;
    border-radius: 5px;
    overflow: auto;
    font-family: 'Courier New', Courier, monospace;
}

````

# Services

### Dentro da pasta ```src```, voce encontrara outra pasta chamada ``services``, dentro dela tera o arquivo ``user.services.ts`` e nele adicione:

````ts
import { database  } from "../database"

export const findUserByLoginPassword = async (username: string, password: string) => {
  const db = await database()
  return db.get(`SELECT * FROM users WHERE username = ? AND password = ? LIMIT 1`, [username, password])
}

export const getAllUsers = async () => {
  const db = await database()
  return db.all(`SELECT id, name, email, username FROM users`)
}
````
### Este arquivo está implementando funções para interagir com o banco de dados de usuários. Vou detalhar o que cada função faz:

- **`findUserByLoginPassword`**:
  - Esta função recebe o nome de usuário e a senha como parâmetros.
  - Conecta-se ao banco de dados e executa uma consulta SQL para encontrar um usuário que corresponda ao nome de usuário e senha fornecidos.
  - Retorna o usuário encontrado, ou `null` se nenhum usuário corresponder aos critérios.

- **`getAllUsers`**:
  - Esta função se conecta ao banco de dados e executa uma consulta SQL para obter todos os usuários.
  - Retorna uma lista de usuários com seus IDs, nomes, e-mails e nomes de usuário.

# Utils
### Dentro da pasta ```src```, voce encontrara outra pasta chamada ``utils``, dentro dela tera o arquivo ``addAliasDots.ts`` e nele adicione:

````ts
export default function addAliasDots(obj: any) {
    let newObj: any = {}
    for (let key in obj) {
      newObj[`:${key}`] = obj[key]
    }
    return newObj
  }
````

### Dentro da pasta ```src```, voce encontrara outra pasta chamada ``utils``, dentro dela tera o arquivo ``index.ts`` e nele adicione:

````ts
export { default as addAliasDots } from "./addAliasDots"
````

# App.ts
### Dentro da pasta ```src```, voce encontrara o arquivo ``app.ts`` e adicione:

````ts
import express, { RequestHandler } from 'express'
import * as userServices from './services/user.services'
import { randomUUID } from 'crypto'
import { database } from './database';

const port = 4400
const app = express()

app.use(express.json())
app.use(express.static(__dirname + '/../public'))

type user = { id: number, name: string, email: string, username: string, password: string }
const logged: { [token: string]: user } = {}


/**
 * Esta função verifica por username se já existem tokem criado para o usuário
 * @param username 
 * @returns 
 */
const isAlreadyLogged = (username: string) => {
  for (const token in logged)
    if (logged[token].username === username)
      return token
  return false
}

// Check if user is logged middleware
const middlewareLogged: RequestHandler = (req, res, next) => {
  const token = req.params.token
  if (!token)
    return res.status(404).json({ error: "Token não informado" })
  if (!logged[token])
    return res.status(401).json({ error: "Token inválido" })
  next()
}

const middlewareSouDono: RequestHandler = (req, res, next) => {
  const { id, token } = req.params;
  if (!logged[token]) {
    return res.status(401).json({ error: "Token inválido" });
  }
  if (logged[token].id+"" !== id) {
    return res.status(401).json({ error: "Você não tem permissão para atualizar este usuário" });
  }
  next()
}

// TOKEN CREATE :: LOGIN
app.post("/token", async (req, res) => {
  const { username, password } = req.body
  const tokenAlread = isAlreadyLogged(username)
  if (tokenAlread)
    return res.status(401).json({
      error: "Usuário já está logado", 
      token: tokenAlread
    })
  const user = await userServices.findUserByLoginPassword(username, password)
  if (!user)
    return res.status(401).json({ error: "Usuário ou senha inválidos" })
  const token = randomUUID()
  logged[token] = user
  return res.json({ token })
})

app.put('/users/:id/:token', middlewareSouDono, async (req, res) => {
  const db = await database();
  const { id, token } = req.params;
  const { name, email } = req.body;
  const userId = parseInt(id, 10);    
  await db.run('UPDATE users SET name = ?, email = ? WHERE id = ?', [name, email, userId]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [userId]);

  res.json(user);
});


app.delete('/users/:id/:token', middlewareSouDono, async (req, res) => {
  const db = await database();
  const { id } = req.params;
  await db.run('DELETE FROM users WHERE id = ?', [id]);
  res.json({ message: 'User deleted' });
});

app.get("/users/:token", middlewareLogged, async (req, res) => {
  const users = await userServices.getAllUsers()
  return res.json(users)
})





// TOKEN CHECK :: VALIDATE
app.get("/token/:token", (req, res) => {
  const token = req.params.token
  if (!token)
    return res.status(401).json({ error: "Token não informado" })
  if (!logged[token])
    return res.status(401).json({ error: "Token inválido" })
  return res.json({ ...logged[token], password: undefined })
})

// TOKEN DELETE :: LOGOUT
app.delete("/token/:token", (req, res) => {
  const token = req.params.token
  if (!token)
    return res.status(401).json({ error: "Token não informado" })
  if (!logged[token])
    return res.status(401).json({ error: "Token inválido" })
  delete logged[token]
  return res.status(204).send()
})

// LISTAR USUÁRIOS SOMENTE SE ESTIVER LOGADO
app.get("/users/:token", middlewareLogged, async (req, res) => {
  const users = await userServices.getAllUsers()
  return res.json(users)
})

app.listen(port, () => console.log(`⚡ Server is running on port ${port}`))
````
### Este arquivo está implementando o servidor Express para autenticação e gerenciamento de usuários. Vou detalhar o que cada parte faz:

- **Importações e Configuração**:
  - Importa pacotes necessários como `express`, `userServices`, `crypto` e `database`.
  - Define a porta do servidor como `4400`.
  - Cria uma instância do servidor Express e configura middlewares para manipulação de JSON e arquivos estáticos.

- **Funções e Middlewares**:
  - **`isAlreadyLogged`**:
    - Verifica se um usuário já está logado, retornando o token se o usuário estiver logado.
  - **`middlewareLogged`**:
    - Middleware que verifica se um token de autenticação é válido.
  - **`middlewareSouDono`**:
    - Middleware que garante que o usuário tem permissão para modificar ou excluir um recurso.

- **Rotas**:
  - **`POST /token`**:
    - Endpoint para autenticação. Recebe `username` e `password`, e retorna um token se as credenciais forem válidas.
  - **`GET /token/:token`**:
    - Endpoint para validar um token. Retorna as informações do usuário associado ao token.
  - **`DELETE /token/:token`**:
    - Endpoint para logout. Remove o token da lista de tokens válidos.
  - **`GET /users/:token`**:
    - Endpoint para listar todos os usuários. Requer um token válido.
  - **`PUT /users/:id/:token`**:
    - Endpoint para atualizar as informações de um usuário. Requer que o token corresponda ao ID do usuário.
  - **`DELETE /users/:id/:token`**:
    - Endpoint para excluir um usuário. Requer que o token corresponda ao ID do usuário.


# database.ts
### Dentro da pasta ```src```, voce encontrara o arquivo ``database.ts`` e adicione:

````ts
import { open, Database } from "sqlite"
import { Database as driver } from "sqlite3"
import { addAliasDots as dots } from "./utils"

let instance: Database | null = null

const filename = "./database.sqlite"

export const database = async () => {
  if (instance)
    return instance

  const db =
    await open({ filename, driver })

  await db.exec(`
    CREATE TABLE IF NOT EXISTS users (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      name TEXT NOT NULL,
      email TEXT NOT NULL,
      username TEXT NOT NULL,
      password TEXT NOT NULL
    )
  `)

  const users: any[] = require("../initial-users.json")
  users.forEach(async user => await db.run(`
      INSERT INTO users (name, email, username, password) 
      SELECT :name, :email, :username, :password
      WHERE NOT EXISTS (SELECT 1 FROM users WHERE username = :username)
    `, dots(user)
  ))

  return instance = db
}

database()
````
### Este arquivo está configurando o banco de dados para o projeto.

# Usuarios
### No arquivo ``initial-users.json`` adicione: 
````json
[
    {
      "name": "Joao Silva",
      "email": "joao@gmail.com",
      "username": "Joao",
      "password": "123"
    },
    
    {
      "name": "Maria Santos",
      "email": "maria@gmail.com",
      "username": "Maria",
      "password": "123"
    }
  ]
````
### Este arquivo está criando um conjunto inicial de usuários para o banco de dados.

# Package.json

### No arquivo ``package.json`` substitua tudo por:
````json
{
  "name": "2024-ia22-2tri",
  "version": "1.0.0",
  "description": "Programação II",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "npx nodemon src/app.ts"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "cors": "^2.8.5",
    "express": "^4.19.2",
    "sqlite": "^5.1.1",
    "sqlite3": "^5.1.7"
  },
  "devDependencies": {
    "@types/cors": "^2.8.17",
    "@types/express": "^4.17.21",
    "nodemon": "^3.1.4",
    "ts-node": "^10.9.2",
    "typescript": "^5.5.4"
  }
}

````


# Executando

### No terminal, digite `npm run dev` e pressione Enter.

### Acesse no seu navegador. Para isso, vá até a opção ``Portas`` e clique no seguinte icone.

![Imagem do Local de Acesso](https://github.com/Edson-Edu/IA22-Cadastro-Login-Autenticacao/blob/main/img/botao.png?raw=true) 

### Na tela de login, insira o nome de usuário `Joao` e a senha `123`, e clique no botão **Login**.

![Imagem da Tela de Login](https://github.com/Edson-Edu/IA22-Cadastro-Login-Autenticacao/blob/main/img/Login.png?raw=true) 

### Após o login, você será direcionado para a página principal, onde poderá ver todos os usuários cadastrados. 

### Note que você só poderá editar e excluir o seu próprio usuário.

# Parabéns!
 Você chegou ao fim do tutorial! 🎉
