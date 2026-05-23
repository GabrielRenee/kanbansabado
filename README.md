# 📋 Kanban Board — JWT Auth

> Aplicação de gerenciamento de tarefas no estilo Kanban com autenticação segura via JWT.

---

## 🚀 Sobre o Projeto

**Kanban Board** é uma aplicação web fullstack que permite ao usuário organizar suas tarefas em colunas no estilo Kanban (**A Fazer**, **Fazendo**, **Concluído**). Cada usuário possui sua própria conta e vê apenas as suas tarefas. A comunicação entre frontend e backend é protegida por **JSON Web Tokens (JWT)**.

---

## ✨ Funcionalidades

- ✅ Cadastro e login de usuários
- 🔐 Autenticação com JWT (expira em 8 horas)
- 🚪 Logout com revogação de token
- 📝 Criar, editar e excluir tarefas
- 🖱️ Arrastar e soltar tarefas entre colunas (Drag & Drop)
- 🏷️ Prioridades: **Alta**, **Média** e **Baixa**
- ⚠️ Indicação visual de tarefas em atraso
- ✔️ Marcar tarefa como concluída independentemente da coluna
- 📅 Data de vencimento por tarefa
- 💾 Persistência de dados em arquivos JSON locais

---

## 🛠️ Tecnologias Utilizadas

### Backend
| Tecnologia | Descrição |
|---|---|
| [Node.js](https://nodejs.org/) | Ambiente de execução JavaScript |
| [Express](https://expressjs.com/) | Framework web para Node.js |
| [bcrypt](https://www.npmjs.com/package/bcrypt) | Hash seguro de senhas |
| [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) | Geração e validação de JWT |
| [cors](https://www.npmjs.com/package/cors) | Suporte a Cross-Origin Resource Sharing |

### Frontend
| Tecnologia | Descrição |
|---|---|
| HTML5 | Estrutura da interface |
| CSS3 | Estilização e layout |
| JavaScript (Vanilla) | Lógica e interatividade |

---

## 📁 Estrutura do Projeto

```
kanbansabado/
├── public/
│   └── index.html        # Frontend da aplicação (SPA)
├── server.js             # Servidor Express + API REST
├── package.json          # Dependências e scripts
├── users.json            # Banco de dados de usuários (gerado automaticamente)
├── tasks.json            # Banco de dados de tarefas (gerado automaticamente)
├── revoked_tokens.json   # Tokens JWT revogados (gerado automaticamente)
└── .gitignore
```

---

## ⚙️ Como Rodar o Projeto

### Pré-requisitos
- [Node.js](https://nodejs.org/) instalado (versão 16 ou superior)

### 1. Clone o repositório
```bash
git clone https://github.com/GabrielRenee/kanban.git
cd kanban
```

### 2. Instale as dependências
```bash
npm install
```

### 3. Inicie o servidor

**Modo produção:**
```bash
npm start
```

**Modo desenvolvimento (com auto-reload):**
```bash
npm run dev
```

> ⚠️ Para usar o modo `dev`, instale o nodemon globalmente: `npm install -g nodemon`

### 4. Acesse a aplicação
Abra o navegador e acesse:
```
http://localhost:3000
```

---

## 🔌 Endpoints da API

### Autenticação

| Método | Rota | Descrição |
|--------|------|-----------|
| `POST` | `/api/auth/register` | Cadastrar novo usuário |
| `POST` | `/api/auth/login` | Fazer login e receber token JWT |
| `POST` | `/api/auth/logout` | Revogar token (requer autenticação) |

### Tarefas (🔒 Requerem token JWT no header)

| Método | Rota | Descrição |
|--------|------|-----------|
| `GET` | `/api/tasks` | Listar tarefas do usuário autenticado |
| `POST` | `/api/tasks` | Criar nova tarefa |
| `PUT` | `/api/tasks/:id` | Editar tarefa existente |
| `DELETE` | `/api/tasks/:id` | Excluir tarefa |
| `PATCH` | `/api/tasks/:id/status` | Atualizar apenas o status (Kanban) |
| `PATCH` | `/api/tasks/:id/complete` | Alternar campo `completed` |

### Autenticação nas rotas protegidas

Envie o token no header `Authorization`:
```
Authorization: Bearer <seu_token_jwt>
```

---

## 📦 Modelo de Dados

### Usuário (`users.json`)
```json
{
  "userId": "1779543837934",
  "name": "Nome do Usuário",
  "email": "usuario@email.com",
  "password": "$2b$10$..." 
}
```

### Tarefa (`tasks.json`)
```json
{
  "id": "1779544014447",
  "userId": "1779543837934",
  "title": "Minha tarefa",
  "description": "Descrição detalhada",
  "status": "todo",
  "dueDate": "2026-06-01",
  "priority": "high",
  "completed": false,
  "createdAt": "2026-05-23T13:46:54.447Z",
  "updatedAt": "2026-05-23T13:47:42.343Z"
}
```

**Status possíveis:** `todo` | `doing` | `done`  
**Prioridades possíveis:** `low` | `medium` | `high`

---

## 🔒 Segurança

- Senhas armazenadas com hash **bcrypt** (saltRounds: 10)
- Tokens JWT com expiração de **8 horas**
- Tokens inválidos após logout são armazenados em lista de revogação
- Cada usuário acessa **somente suas próprias tarefas**

---

## 📄 Licença

Este projeto foi desenvolvido para fins educacionais.

---

<p align="center">Feito com ❤️ por <a href="https://github.com/GabrielRenee">GabrielRenee</a></p>
