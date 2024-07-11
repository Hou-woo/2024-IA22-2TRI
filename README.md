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

Meus Parabens!