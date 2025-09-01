# Cadastro de Pessoas API

![Versão](https://img.shields.io/badge/version-1.0.0-blue)

API REST para gerenciamento de um cadastro de clientes (Pessoas e Cidades), desenvolvida com Jakarta EE e Payara Micro.

---

## Instruções para Versionamento da API

Este projeto adota o [Semantic Versioning 2.0.0](http://semver.org/). O versionamento segue o formato `X.Y.Z` (ou `MAJOR.MINOR.PATCH`), onde cada parte é incrementada de acordo com as seguintes regras:

-   **MAJOR (X)**: Para mudanças incompatíveis na API (breaking changes).
    -   Ex: Remoção de endpoints REST.
    -   Ex: Alteração de esquema de banco de dados que quebra compatibilidade.

-   **MINOR (Y)**: Para adição de novas funcionalidades de forma retrocompatível.
    -   Ex: Adição de novos endpoints REST.
    -   Ex: Novas funcionalidades em classes existentes sem quebrar o uso atual.

-   **PATCH (Z)**: Para correções de bugs de forma retrocompatível (bug fixes).
    -   Ex: Correções de segurança.
    -   Ex: Correção de bugs que não alteram a assinatura de métodos da API.

#### Exemplo de Versionamento para este Projeto
```
1.0.0 - Versão inicial com CRUD de Pessoa e Cidade
1.1.0 - Adição de endpoints de relatórios
1.1.1 - Correção de bug na validação de CPF
2.0.0 - Migração para Jakarta EE 10 (breaking change)
```

---

## Como Contribuir

Para garantir a qualidade e a consistência do código, seguimos algumas diretrizes:

1.  **Branching Model:** Utilizamos o **Git Flow**. Todo desenvolvimento de novas funcionalidades deve ser feito em um branch `feature/*` a partir do `develop`.
2.  **Commits:** Adotamos o padrão **Conventional Commits** para mensagens de commit claras e significativas.
3.  **Code Review:** Todo código deve ser submetido via **Pull Request** para o branch `develop` e precisa ser revisado e aprovado por pelo menos um membro da equipe antes do merge.

---

## Tabela de Conteúdos

- [Funcionalidades](#funcionalidades)
- [Tecnologias Utilizadas](#tecnologias-utilizadas)
- [Instruções para Versionamento da API](#instruções-para-versionamento-da-api)
- [Pré-requisitos](#pré-requisitos)
- [Como Executar o Projeto (Desenvolvimento)](#como-executar-o-projeto-desenvolvimento)
  - [1. Clone o Repositório](#1-clone-o-repositório)
  - [2. Configure o Banco de Dados (PostgreSQL com Docker)](#2-configure-o-banco-de-dados-postgresql-com-docker)
  - [3. Execute a Aplicação](#3-execute-a-aplicação)
- [Executando os Testes](#executando-os-testes)
- [Como Executar o Projeto (Produção)](#como-executar-o-projeto-produção)
- [Como Contribuir](#como-contribuir)

---

## Funcionalidades

-   [x] CRUD completo de Pessoas
-   [x] CRUD completo de Cidades
-   [ ] Geração de relatórios (em desenvolvimento)

---

## Tecnologias Utilizadas

-   **Backend:** Java 20, Jakarta EE 10
-   **Servidor de Aplicação:** Payara Micro
-   **Banco de Dados:** PostgreSQL
-   **Build e Dependências:** Apache Maven
-   **Containerização:** Docker

---

## Pré-requisitos

Antes de começar, você vai precisar ter instalado em sua máquina:
-   [JDK 20](https://www.oracle.com/java/technologies/downloads/) ou superior
-   [Apache Maven](https://maven.apache.org/download.cgi)
-   [Docker](https://www.docker.com/products/docker-desktop/)
-   [Git](https://git-scm.com/downloads)

---

## Como Executar o Projeto (Desenvolvimento)

### 1. Clone o Repositório

```bash
git clone [https://github.com/tech-solutions/cadastro-pessoas.git](https://github.com/tech-solutions/cadastro-pessoas.git) # Substitua pela URL do seu repositório
cd cadastro-pessoas
```

### 2. Configure o Banco de Dados (PostgreSQL com Docker)

Para um ambiente de desenvolvimento limpo, recomendamos usar o Docker para rodar o banco de dados. O comando abaixo irá criar um container PostgreSQL pronto para uso.

```bash
docker run -it --rm \
  --name devPostgresDb \
  -p 5432:5432 \
  -e POSTGRES_USER=postgres \
  -e POSTGRES_PASSWORD=postgres \
  -e POSTGRES_DB=localdb \
  -d postgres
```

### 3. Execute a Aplicação

Primeiro, torne o script do Maven Wrapper executável (necessário apenas uma vez).

```bash
# Apenas para Linux/macOS
chmod +x mvnw
```

Agora, compile o projeto e inicie o servidor Payara Micro.

**No Linux/macOS:**
```bash
./mvnw clean package payara-micro:start
```

**No Windows:**
```bash
mvnw.cmd clean package payara-micro:start
```

Após a inicialização, a API estará disponível em `http://localhost:8080`.

---

## Executando os Testes

Para rodar a suíte de testes automatizados, execute o comando:

```bash
# Linux/macOS
./mvnw clean test

# Windows
mvnw.cmd clean test
```

Caso deseje compilar o projeto sem rodar os testes, utilize a flag `-DskipTests=true`.

---

## Como Executar o Projeto (Produção)

### Configurando Variáveis de Ambiente

Em produção, as credenciais do banco de dados devem ser fornecidas via variáveis de ambiente:

-   `DATABASE_URL`: A URL de conexão JDBC. Ex: `jdbc:postgresql://127.0.0.1:5432/customerdb`
-   `DATABASE_USERNAME`: O nome de usuário do banco. Ex: `postgres`
-   `DATABASE_PASSWORD`: A senha do banco. Ex: `sudodb`

### Opção 1: Executando o artefato .war

Compile o projeto para gerar o arquivo `.war` na pasta `target/` e execute-o com o Payara Micro.

```bash
# 1. Compile o projeto
./mvnw clean package -DskipTests=true

# 2. Execute o .war (substitua <payara-micro.jar> pelo caminho do seu executável)
java -jar <payara-micro.jar> target/Cadastro-Pessoas.war
```

### Opção 2: Executando com Docker

Esta é a abordagem recomendada para produção.

**1. Construa a imagem Docker:**
O `Dockerfile` na raiz do projeto já está configurado. Execute o comando:

```bash
# Compila o projeto e constrói a imagem Docker
./mvnw clean package
docker build -t cadpessoas:v1 .
```

**2. Rode o container da aplicação:**
Execute o comando abaixo, substituindo os valores pelas suas configurações de produção.

```bash
docker run -it --rm \
  -e DATABASE_URL="jdbc:postgresql://<url_do_banco_de_dados>" \
  -e DATABASE_USERNAME="<nome_do_usuario>" \
  -e DATABASE_PASSWORD="<senha_do_usuario>" \
  -p 8080:8080 \
  --name cadpessoas-app \
  cadpessoas:v1
```
A aplicação estará disponível em `http://localhost:8080/Cadastro-Pessoas`.
