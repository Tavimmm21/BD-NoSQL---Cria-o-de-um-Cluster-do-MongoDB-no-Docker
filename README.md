# Banco de Dados NoSQL | Comandos para Criação de um Cluster do MongoDB no Docker

## Integrantes 
1. Otávio Sant'Anna - RA: 2307343
2. Vitor Augusto Sant'Anna - RA: 2305610

## 🚀 Etapas do Projeto

### 1. Criação dos containers
```bash
docker run -d --name mongodb1 -p 27017:27017 mongo mongod --replSet rs0
docker run -d --name mongodb2 -p 27018:27017 mongo mongod --replSet rs0
docker run -d --name mongodb3 -p 27019:27017 mongo mongod --replSet rs0
docker run -d --name mongodb4 -p 27020:27017 mongo mongod --replSet rs0
```

### 2. Inicialização do Replica Set
- Acessando o terminal do MongoDB
```bash
docker exec -it mongodb1 mongosh
```
- Inicializando os containers
```js
rs.initiate({
  _id: "rs0",
  members: [
    { _id: 0, host: "host.docker.internal:27017" },
    { _id: 1, host: "host.docker.internal:27018" },
    { _id: 2, host: "host.docker.internal:27019" },
    { _id: 3, host: "host.docker.internal:27020" }
  ]
})
```
- Visualizando a estrutura do cluster 
```js
rs.status()
```

### 3. Inserção de dados no nó primário
- Criando um banco de dados chamado 'faculdade'
```js
use faculdade
```
- Inserindo dados no banco de dados 'faculdade'
```js
db.alunos.insertMany([
  {nome: "João", curso: "Banco de Dados"},
  {nome: "Maria", curso: "Engenharia"},
  {nome: "Pedro", curso: "Ciência da Computação"}
]
```
- Exibindo os dados presentes no DB 'faculdade'
```js
db.alunos.find().pretty()
```

### 4. Verificação da replicação nos nós secundários
- Acessando o nó secundário
```bash
docker exec -it mongodb2 mongosh
```
- Habilitando a leitura do nó secundário
```js
rs.secondaryOk()
```
- Acessando e verificando os dados replicados para o nó secundário
```js
use faculdade 
```
```js
db.alunos.find().pretty()
```
### 5. Queda Nó Secundário 
- Nós ativados antes da queda
```bash
docker ps
```
- Realizando a queda de um nó secundário
```bash
docker stop mongodb3
```
- Acessando o nó primario
```bash
docker exec -it mongodb1 mongosh
```
- Verificando se o nó foi desativado
```js
rs.status()
```
- Inserindo e verificando dados no nó primario
```js
use faculdade 
```
```js 
db.alunos.insertOne({nome: "Carlos", curso: "Rede de Computadores"})
```
```js 
db.alunos.find().pretty()
```

### 6. Queda Nó Primário 
- Nós ativados antes da queda
```bash
docker ps
```
- Realizando a queda de um nó primário 
```bash
docker stop mongodb1
```
- Reestabelecendo um nó apenas
```bash
docker start mongodb3
```
- Verificando a seleção de um novo nó primário
```js
rs.status()
```
- Inserindo dados no novo nó primário
```bash
docker exec -it mongodb2 mongosh
```
```js
use faculdade 
```
```js 
db.alunos.insertOne({nome: "Amanda", curso: "Sistemas de Informação"})
```
```js 
db.alunos.find().pretty()
```




