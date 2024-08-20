# Iniciando um projeto Node.js com TypeScript

- Abra um navegador do gosto de sua pessoa e pesquise Github.com; 
- Crie uma conta no Github, se já tiver uma conta, perfeito! :o
- Crie um diretório para o projeto e acesse-o pelo vscode;
- Quando entrar no vscode, pressione o Ctrl e clique apenas uma vez no botão de aspas do teclado, isso abrirá o terminal do vscode;
- Após o terminal abrir, digite as seguintes coisas:

npm init -y
npm install express cors sqlite3 sqlite
npm install --save-dev typescript nodemon ts-node @types/express @types/cors
npx tsc --init
mkdir src
touch src/app.ts

- Nessa ordem, exatamente desse jeito, recomendo colocar um por um ao invés de tudo de uma vez, para garantir que não ocorra nenhum erro;

```bash
npm init -y
npm install express cors sqlite3 sqlite
npm install --save-dev typescript nodemon ts-node @types/express @types/cors
npx tsc --init
mkdir src
touch src/app.ts
```

## Configuranado o `tsconfig.json`

- Após todo o primeiro passo, vá até o lugarzinho á sua esquerda escrito tsconfig.json e cliqie nele apenas uma vez;
- logo em seguida você será direcionado a uma pagina, quando chegar nela, vá até a linha 58, onde estará escrito "outDir": "./" e mude para "outDir": "./dist", e adicione a linha "rootDir": "./src";

```json
{
  "compilerOptions": {
    "target": "ES2017",
    "module": "commonjs",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  }
}
```

## Configurando o `package.json`

- Siga em diante até o lugarzinho á sua esquerda escrito package.json e cliqie nele apenas uma vez;
- Logo você chegará a uma pagina, quando chegar nela, voce irá na linha 7 e colocará uma virgulo depois do " && exit 1", e na linha seguinte digitará "dev": "npx nodemon src/app.ts";

```json
"scripts": {
  "dev": "npx nodemon src/app.ts"
}
```

## Criando arquivo inicial do servidor

- Crie um arquivo no src, chamado app.ts e entre nele, após essa ação digite o seguinte código:

```typescript
import express from 'express';
import cors from 'cors';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});
```

## Inicializando o servidor

- Após todos esses passos abra o terminal e digite "npm run dev"; 

```bash
npm run dev
```

Se tudo ocorrer bem, você verá a mensagem `Server running on port 3333` no terminal.

## Testando o servidor

Abra o navegador e acesse `http://localhost:3333`, você verá a mensagem `Hello World`.

## Configurando o banco de dados

- Crie um arquivo no src, chamado database.ts e entre nele, após essa ação digite o seguinte código:
```typescript
import { Database, open } from 'sqlite';
import sqlite3 from 'sqlite3';

let instance: any | null = null;

export async function connect() {
  if (instance) return instance;

  const db = await open({
     filename: './src/database.sqlite',
     driver: sqlite3.Database
   });
  
  await db.exec(`
    CREATE TABLE IF NOT EXISTS users (
      id INTEGER PRIMARY KEY AUTOINCREMENT,
      name TEXT,
      email TEXT
    )
  `);

  instance = db;
  return db;
}
```
## Adicionando o banco de dados ao servidor

- Entre no arquivo app.ts e digite o seguinte código: 
```typescript
import express from 'express';
import cors from 'cors';
import { connect } from './database';

const port = 3333;
const app = express();

app.use(cors());
app.use(express.json());

app.get('/', (req, res) => {
  res.send('Hello World');
});

app.post('/users', async (req, res) => {
  const db = await connect();
  const { name, email } = req.body;

  const result = await db.run('INSERT INTO users (name, email) VALUES (?, ?)', [name, email]);
  const user = await db.get('SELECT * FROM users WHERE id = ?', [result.lastID]);

  res.json(user);
});

app.listen(port, () => {
  console.log(`Server running on port ${port}`);
});

app.get('/users', async (req, res) => {
  const db = await connect();
  const users = await db.all('SELECT * FROM users');

  res.json(users);
});
```
## Listando os Usuários

Adicione esse seguinte código em seu arquivo app.ts.
```typescript
app.listen(port, () => {
  console.log(`Server running on port ${port}`)
})
```
## Editando um Usuário

Adicione esse seguinte código em seu arquivo app.ts.
```typescript
app.put('/users/:id', async (req, res) => {
  const db = await connect()
  const { name, email } = req.body
  const { id } = req.params
  await db.run('UPDATE users SET name = ?, email = ? WHERE id = ?', [name, email, id])
  const user = await db.get('SELECT * FROM users WHERE id = ?', [id])
  res.json(user)
})
```
## Deletando um Usuário

Adicione esse seguinte código em seu arquivo app.ts.
```typescript
app.delete('/users/:id', async (req, res) => {
  const db = await connect()
  const { id } = req.params
  await db.run('DELETE FROM users WHERE id = ?', [id])
  res.json({ message: 'User deleted' })
})
```
## Continuação 

Crie uma pasta chamada public e nela crie um arquivo chamado index.html, nesse arquivo adicione o seguinte código:
```typescript
 <!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
</head>

<body>
  <form>
    <input type="text" name="name" placeholder="Nome">
    <input type="email" name="email" placeholder="Email">
    <button type="submit">Cadastrar</button>
  </form>

  <table>
    <thead>
      <tr>
        <th>Id</th>
        <th>Name</th>
        <th>Email</th>
        <th>Ações</th>
      </tr>
    </thead>
    <tbody>
      <!--  -->
    </tbody>
  </table>

  ''' <script>
    // 
    const form = document.querySelector('form')

    form.addEventListener('submit', async (event) => {
      event.preventDefault()

      const name = form.name.value
      const email = form.email.value

      await fetch('/users', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name, email })
      })

      form.reset()
      fetchData()
    })

    // 
    const tbody = document.querySelector('tbody')

    async function fetchData() {
      const resp = await fetch('/users')
      const data = await resp.json()

      tbody.innerHTML = ''

      data.forEach(user => {
        const tr = document.createElement('tr')
        tr.innerHTML = `
          <td>${user.id}</td>
          <td>${user.name}</td>
          <td>${user.email}</td>
          <td>
            <button class="excluir">excluir</button>
            <button class="editar">editar</button>
          </td>
        '

        const btExcluir = tr.querySelector('button.excluir')
        const btEditar = tr.querySelector('button.editar')

        btExcluir.addEventListener('click', async () => {
          await fetch(`/users/${user.id}`, { method: 'DELETE' })
          tr.remove()
        })

        btEditar.addEventListener('click', async () => {
          const name = prompt('Novo nome:', user.name)
          const email = prompt('Novo email:', user.email)

          await fetch(`/users/${user.id}`, {
            method: 'PUT',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify({ name, email })
          })

          fetchData()
        })

        tbody.appendChild(tr)
      })
    }

    fetchData()
  </script>
</body>

</html>
```
## Testando a inserção de dados

- Vá até as extenções do Visual Code e instale o Rest Client, após instalado, crie um arquivo chamado ts.http e mova para o src e lá mesmo digite esse código:
```typescript
POST http://localhost:3333/users
Content-Type: application/json

{
  "name": "John Doe",
  "email" : "BomDia"
}

PUT http://localhost:3333/users/1 HTTP/1.1
content-type: application/json

{
  "name": "John Doe Updated",
  "email": "johndoe@mail.com"
}

```

DELETE http://localhost:3333/users/1 HTTP/1.1

- Abra o seu navegador, vá na barra de pesquisa e digite http://localhost:3333/users; 

Meus Parabens!

:D
