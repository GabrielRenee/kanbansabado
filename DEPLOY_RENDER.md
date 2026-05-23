# 🚀 Dossiê de Deploy — Render (Kanban System JWT)

Este guia prático documenta a revisão e padronização feitas no projeto **Kanban System JWT** para viabilizar seu deploy na plataforma **Render**, além de detalhar as etapas de configuração necessárias.

---

## 🛠️ O que foi revisado e alterado?

Para preparar o projeto para a nuvem, as seguintes modificações foram implementadas no código-fonte:

1. **Porta Dinâmica no Servidor (`server.js`)**:
   - **Antes**: O servidor escutava apenas na porta fixa `3000`.
   - **Depois**: Ajustado para `const PORT = process.env.PORT || 3000;`. O Render define dinamicamente a porta onde a aplicação deve rodar por meio da variável de ambiente `PORT`.

2. **Chave Secreta JWT Protegida (`server.js`)**:
   - **Antes**: A chave secreta JWT estava exposta diretamente no código (`'super-secret-key-for-jwt-do-not-use-in-prod'`).
   - **Depois**: Ajustado para carregar de `process.env.JWT_SECRET` com fallback seguro para ambiente local. Em produção, essa chave será injetada de forma segura nas configurações do Render.

3. **URL Dinâmica no Frontend (`public/index.html`)**:
   - **Antes**: A constante `API_URL` apontava rigidamente para `http://localhost:3000/api`.
   - **Depois**: Alterada para `/api` (caminho relativo). Isso faz com que as requisições apontem automaticamente para o mesmo domínio em que o frontend está hospedado (seja no `localhost` ou no domínio de produção do Render), eliminando erros de CORS e URLs quebradas.

---

## ⚠️ Atenção Especial: Persistência de Dados (Arquivos JSON)

Este projeto utiliza arquivos JSON locais (`users.json`, `tasks.json` e `revoked_tokens.json`) para armazenar dados. No **Render**, os contêineres de hospedagem possuem um **sistema de arquivos efêmero** (ephemeral file system) no plano gratuito. 

> **O que isso significa?** Toda vez que o servidor reiniciar (o que acontece pelo menos uma vez por dia ou a cada novo deploy), os usuários cadastrados e tarefas criadas **serão apagados**, retornando o banco ao estado inicial (vazio).

### Como solucionar isso?

* **Opção 1: Render Persistent Disks (Recomendado se quiser manter arquivos JSON)**
  Se você estiver usando um plano pago do Render (a partir do plano *Starter*), você pode anexar um **Persistent Disk** (Disco Persistente) de 1GB ao seu Web Service. Ele cria um diretório permanente no servidor que sobrevive a reinicializações.
  
* **Opção 2: Banco de Dados Externo (Melhor prática de mercado)**
  Em projetos de produção reais, substitui-se a gravação em arquivos JSON por bancos de dados gerenciados, como o **MongoDB Atlas** (NoSQL gratuito) ou o **Render PostgreSQL** (grátis por 90 dias).

*(As instruções abaixo explicam como fazer o deploy padrão e como configurar o disco persistente caso utilize um plano pago).*

---

## 📋 Passo a Passo para Deploy no Render

Siga as etapas abaixo para publicar o projeto:

### Passo 1: Preparar seu repositório no GitHub
1. Faça o commit e o push das últimas alterações efetuadas no seu repositório do GitHub (`GabrielRenee/kanbansabado`).
2. Garanta que o repositório esteja público ou que sua conta do Render tenha permissão para lê-lo.

### Passo 2: Criar o Web Service no Render
1. Acesse o painel do [Render](https://dashboard.render.com/) e faça login.
2. Clique no botão **"New +"** no canto superior direito e selecione **"Web Service"**.
3. Conecte sua conta do GitHub (se ainda não o fez) e selecione o repositório `kanbansabado`.

### Passo 3: Configurar os Detalhes do Web Service
Preencha os campos conforme as especificações abaixo:

* **Name**: `kanban-jwt-system` (ou o nome que preferir)
* **Region**: Escolha a mais próxima de você (ex: `Ohio (us-east-2)` ou `Frankfurt (eu-central-1)`)
* **Branch**: `main` (ou a branch principal que deseja publicar)
* **Root Directory**: Deixe em branco (raíz do projeto)
* **Runtime**: `Node`
* **Build Command**: `npm install`
* **Start Command**: `npm start`
* **Instance Type**: Selecione **Free** (Grátis) ou **Starter** (Pago, necessário se for usar Disco Persistente)

### Passo 4: Configurar as Variáveis de Ambiente
Antes de clicar em criar, role a página até a seção **Advanced** ou clique na aba **Environment**:

1. Clique em **"Add Environment Variable"**.
2. Adicione a seguinte chave:
   - **Key**: `JWT_SECRET`
   - **Value**: Insira uma senha forte e aleatória (ex: um hash gerado ou uma frase secreta única).
3. *(Opcional)* Você não precisa definir `PORT`, o Render cuidará disso de forma automatizada.

### Passo 5: Configurar Disco Persistente (Caso utilize Plano Pago ⚠️)
Se você estiver utilizando uma instância paga para não perder os dados salvos nos arquivos JSON:
1. Em **Advanced**, clique em **"Add Disk"**.
2. Configure:
   - **Name**: `kanban-data`
   - **Mount Path**: `/data`
   - **Size**: `1 GiB` (mínimo necessário)
3. No código de `server.js`, as linhas que definem os caminhos dos arquivos JSON deverão apontar para a pasta `/data` (ex: `/data/users.json`). *Nota: Se for seguir por esse caminho, me avise para ajustarmos o código para você!*

### Passo 6: Finalizar e Acompanhar logs
1. Clique em **"Create Web Service"**.
2. O Render iniciará o processo de deploy: baixando o código, executando `npm install` e subindo o servidor.
3. Acompanhe os logs no painel. Quando o build terminar, você verá a mensagem: 
   `Server running on http://localhost:XXXX` e o status mudará para **Live** (com um sinalizador verde).
4. O link público do seu Kanban estará disponível no topo esquerdo do painel do Render (ex: `https://kanban-jwt-system.onrender.com`).

---

**Dica extra**: Por estar na camada gratuita (plano Free), se a sua aplicação ficar inativa (sem acessos) por mais de 15 minutos, o Render colocará o contêiner para "dormir". O próximo acesso após esse período pode demorar cerca de 50 segundos para carregar novamente (spin up). Isso é normal para instâncias gratuitas da plataforma.
