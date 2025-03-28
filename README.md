# Banco de Dados NoSQL | Comandos para Criação de um Cluster do MongoDB no Docker

## 🚀 Etapas do Projeto

### 1. Criação dos containers
```bash
docker run -d --name mongodb1 -p 27017:27017 mongo mongod --replSet rs0
docker run -d --name mongodb2 -p 27018:27017 mongo mongod --replSet rs0
docker run -d --name mongodb3 -p 27019:27017 mongo mongod --replSet rs0
docker run -d --name mongodb4 -p 27020:27017 mongo mongod --replSet rs0
```

### 2. Inicialização do Replica Set
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

### 3. Inserção de dados no nó primário
```js
use faculdade
db.alunos.insertOne({ nome: "Maria", curso: "ADS" })
db.alunos.find()
```

### 4. Verificação da replicação nos nós secundários
```js
rs.secondaryOk()
db.alunos.find()
```

### 5. Simulação de falhas
- Queda de 1 ou 2 nós secundários: `docker stop mongodb3`
- Queda do nó primário: `docker stop mongodb1`
- Reativação para restabelecer maioria: `docker start mongodb3`

### 6. Eleição de novo primário
```js
rs.status()
```




