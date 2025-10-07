# HOW TO - Guia de Deploy Passo a Passo

## 📋 Pré-requisitos

Antes de iniciar, certifique-se de ter:

- ✅ [**Azure CLI** instalado](https://aka.ms/installazurecliwindows)  
- ✅ Conta **GitHub** com acesso ao repositório  
- ✅ Conta **Azure ativa** (Azure for Students é suficiente)  
- ✅ Arquivo `deploy.cmd` (ou `deploy.sh`) salvo na **raiz do projeto**o

---

## 🧩 **Passo 1 — Preparar o ambiente**

1. Abra o **Prompt de Comando (CMD)** ou **PowerShell**  
2. Crie um **fork** deste projeto no GitHub  
3. (Opcional) Clone o repositório forkado localmente:

```bash
git clone https://github.com/<seu-usuario>/EcoAidCheckpoint5.git
cd EcoAidCheckpoint5
```

> ⚠️ Substitua `<seu-usuario>` pelo seu nome de usuário no GitHub.

---

## ⚙️ Passo 2: Personalizar variáveis

No início do arquivo `deploy.cmd` ou `deploy.sh`, edite as seguintes variáveis conforme o seu projeto:

| Variável              | Descrição                                                                                  |
| --------------------- | ------------------------------------------------------------------------------------------ |
| `RESOURCE_GROUP_NAME` | Nome do grupo de recursos                                                                  |
| `WEBAPP_NAME`         | Nome único para o Web App (globalmente único no Azure)                                     |
| `SQL_SERVER_NAME`     | Nome único do SQL Server (também globalmente único)                                        |
| `SQL_ADMIN_USER`      | Usuário administrador do SQL Server                                                        |
| `SQL_ADMIN_PASSWORD`  | Senha forte (mínimo 8 caracteres, letra maiúscula, minúscula, número e caractere especial) |
| `GITHUB_REPO_NAME`    | Repositório GitHub no formato `usuario/repositorio`                                        |

---

## 🧰 **Passo 3 — Executar o script de Deploy**

1. Acesse a pasta `scripts`:

```cmd
cd scripts
```

2. Execute o script conforme seu sistema operacional:

**Windows:**
```cmd
deploy.cmd
```

**Linux / macOS:**
```bash
chmod +x deploy.sh
./deploy.sh
```

O script irá executar automaticamente:

- 🔐 Login no Azure  
- 🏗️ Criação do grupo de recursos  
- 🗃️ Criação do SQL Server e banco de dados  
- 🌐 Configuração de firewall  
- ☁️ Criação do App Service e Application Insights  
- 🤖 Configuração do GitHub Actions para deploy contínuo

---

## 🔐 **Passo 4 — Autenticação**

Durante a execução do script, você precisará:

- **Fazer login na Azure** (uma janela do navegador será aberta)  
- **Autorizar o GitHub** para permitir o deploy contínuo:
  - Um código será exibido no terminal
  - Acesse [https://github.com/login/device](https://github.com/login/device) e insira o código fornecido

---

## ⚡ **Passo 5 — Configurar Credenciais no GitHub**

1. Vá até `.github/workflows/` e abra o arquivo YAML que o Azure gerou.  
2. Logo abaixo do passo de **build**, adicione as variáveis de ambiente:

```yaml
- name: Build with Maven
  run: mvn clean install
  env:
    MSSQL_HOST: ${{ secrets.AZURE_SQL_HOST }}
    MSSQL_PORT: ${{ secrets.AZURE_SQL_PORT }}
    MSSQL_DATABASE: ${{ secrets.AZURE_SQL_DATABASE }}
    MSSQL_USER: ${{ secrets.AZURE_SQL_USERNAME }}
    MSSQL_PASSWORD: ${{ secrets.AZURE_SQL_PASSWORD }}
```

3. No repositório do GitHub, acesse:  
   **Settings** → **Secrets and variables** → **Actions** → **New repository secret**  
   E crie os seguintes segredos:

| Nome                  | Valor (exemplo — personalize com os seus dados)                                                 |
| --------------------- | ------------------------------------------------------------------------------------------------ |
| `AZURE_SQL_DATABASE`  | `db-cp5`                                                                                         |
| `AZURE_SQL_PORT`      | `1433`                                                                                           |
| `AZURE_SQL_PASSWORD`  | `Fiap@2tdsvms` *(exemplo)*                                                                       |
| `AZURE_SQL_HOST`      | `sql-server-cp5-rm558263-eastus2.database.windows.net` *(alterar para o seu host)*              |
| `AZURE_SQL_USERNAME`  | `user-fiaper`                                                                                   |

---

## 🧾 **Passo 6 — Verificar o Deploy**

Após a execução bem-sucedida:

1. Acesse sua aplicação em:  
   ```
   https://<seu-webapp-name>.azurewebsites.net
   ```

2. Monitore os logs em tempo real:
   ```cmd
   az webapp log tail --name <WEBAPP_NAME> --resource-group <RESOURCE_GROUP_NAME>
   ```

3. Acompanhe os workflows no GitHub Actions:  
   ```
   https://github.com/<seu-usuario>/<seu-repo>/actions
   ```

---

## 👤 **Passo 7 — Criar um Usuário na API**

Após o deploy, cadastre um usuário para acessar a API:

```bash
curl -X POST https://<seu-webapp-name>.azurewebsites.net/usuarios/cadastrar \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "João Silva",
    "usuario": "joao@email.com",
    "senha": "Senha@123",
    "foto": "foto123"
  }'
```

---

## 🧱 JSON de Operações da API (GET, POST, PUT, DELETE)

Abaixo estão exemplos baseados nas tabelas `tb_usuario`, `tb_categoria` e `tb_produto`.

### 🔹 Usuários

**POST /usuarios/cadastrar**

```json
{
  "nome": "João Silva",
  "usuario": "joao@email.com",
  "senha": "Senha@123",
  "foto": null
}
```

**GET /usuarios**

```json
[
  {
    "id": 1,
    "nome": "João Silva",
    "usuario": "joao@email.com",
    "foto": null
  }
]
```

**PUT /usuarios/1**

```json
{
  "nome": "João S. Almeida",
  "foto": "https://exemplo.com/joao.jpg"
}
```

---

### 🔹 Categorias

**POST /categorias**

```json
{
  "tipo": "Roupas",
  "descricao": "Peças de vestuário em bom estado"
}
```

**GET /categorias**

```json
[
  {
    "id": 1,
    "tipo": "Roupas",
    "descricao": "Peças de vestuário em bom estado"
  }
]
```

**PUT /categorias/1**

```json
{
  "descricao": "Vestuário usado, porém bem conservado"
}
```

**DELETE /categorias/1**

---

### 🔹 Produtos

**POST /produtos**

```json
{
  "nome": "Camisa Polo",
  "descricao": "Camisa polo azul em ótimo estado",
  "condicao": "Usado",
  "preco": 49.99,
  "foto": "https://exemplo.com/camisa.jpg",
  "usuario_id": 1,
  "categoria_id": 1
}
```

**GET /produtos**

```json
[
  {
    "id": 1,
    "nome": "Camisa Polo",
    "descricao": "Camisa polo azul em ótimo estado",
    "condicao": "Usado",
    "preco": 49.99,
    "foto": "https://exemplo.com/camisa.jpg",
    "usuario_id": 1,
    "categoria_id": 1
  }
]
```

**PUT /produtos/1**

```json
{
  "preco": 39.99,
  "condicao": "Semi-novo"
}
```

**DELETE /produtos/1**

---

## 🧰 Dica final

Caso precise repetir o processo:

* Exclua o **Resource Group** antes de rodar o script novamente:

  ```cmd
  az group delete --name <RESOURCE_GROUP_NAME> --yes --no-wait
  ```

---

📘 **Pronto!**
Seu projeto **EcoAidCheckpoint5** estará rodando no Azure com deploy automatizado via **GitHub Actions**, banco SQL configurado e endpoints da API prontos para uso.

