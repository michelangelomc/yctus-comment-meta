# Yctus Comment Meta

## 🌟 Overview

Yctus Comment Meta is a customizable comment system designed for modern web applications.

## 📘 Features

- **Customizable:** Tailor the look and feel to match your brand.
- **Icon Support:** Add icons to enhance user interaction.
- **Easy Integration:** Simple setup for various platforms.

## 🛠 Installation

To install Yctus Comment Meta, follow these steps:

1. Clone the repository:
   ```bash
   git clone https://github.com/michelangelomc/yctus-comment-meta.git
   ```

2. Navigate to the directory:
   ```bash
   cd yctus-comment-meta
   ```

3. Install dependencies:
   ```bash
   npm install
   ```

## 🚀 Usage

To use Yctus Comment Meta:
1. Include the library in your project.
2. Initialize it with your configuration settings.

# 🎯 yctus-comment-meta - AlgaComments System

Centralizador de Comentários com Moderação

## 📋 Visão Geral

Implemente o sistema **AlgaComments**: um conjunto de dois microsserviços que se comunicam de forma **síncrona via HTTP/REST** usando Spring RestClient. O sistema é responsável por receber comentários e enviá-los para moderação.

### ✅ Funcionalidades Principais

- ✨ Criar um novo comentário e enviar para moderação
- 🔍 Consultar detalhes de um comentário aprovado
- 📄 Listar comentários aprovados com paginação

---

## 🏗️ Microsserviço 1: CommentService

### 📡 Responsabilidades

- Expor uma API REST para criação e consulta de comentários
- Enviar novos comentários para moderação via POST síncrono (RestClient)
- Armazenar apenas comentários **aprovados**

### 🔌 Endpoints

```http
POST   /api/comments      # Criar novo comentário
GET    /api/comments/{id} # Obter comentário específico
GET    /api/comments      # Listar comentários (paginado)
```

### 🎮 Controller: CommentController

#### `POST /api/comments`
**Cria um novo comentário**

**Entrada (CommentInput):**
```json
{
  "text": "string",
  "author": "string"
}
```

**Respostas:**
- `201 Created` - CommentOutput (se aprovado)
- `422 Unprocessable Entity` - com motivo (se rejeitado)

#### `GET /api/comments/{id}`
**Retorna detalhes de um comentário**

**Respostas:**
- `200 OK` - CommentOutput
- `404 Not Found` - se não existir

#### `GET /api/comments`
**Lista comentários aprovados com paginação**

**Parâmetros:**
- `page` - número da página
- `size` - quantidade por página

**Resposta:**
```json
{
  "page": 0,
  "size": 10,
  "totalElements": 45,
  "totalPages": 5,
  "content": [
    {
      "id": "UUID",
      "text": "string",
      "author": "string",
      "createdAt": "2023-11-15T10:00:00Z"
    }
  ]
}
```

### 📦 DTOs

#### CommentInput
```json
{
  "text": "string",
  "author": "string"
}
```

#### CommentOutput
```json
{
  "id": "UUID",
  "text": "string",
  "author": "string",
  "createdAt": "2023-11-15T10:00:00Z"
}
```

### 🔗 Integração com ModerationService

O CommentService faz uma chamada **POST** para `/api/moderate` do ModerationService.

**Requisição:**
```json
{
  "text": "string",
  "commentId": "UUID"
}
```

**Resposta Esperada:**
```json
{
  "approved": true,
  "reason": "string"
}
```

### ✔️ Regras de Validação

- ⚠️ ID deve ser UUID
- 🚫 Comentários rejeitados **não são armazenados**

### ⚙️ Requisitos Técnicos

- 🗄️ **Banco de dados:** H2
- ⏱️ **Timeout:** 5 segundos para chamadas ao ModerationService
- 🔌 **Cliente HTTP:** RestClient (síncrono)
- 🛡️ **Tratamento de Erros:**
  - Erros gerais na integração → `502 Bad Gateway`
  - Timeout na integração → `504 Gateway Timeout`
  - Comentário não encontrado → `404 Not Found`

---

## 🏗️ Microsserviço 2: ModerationService

### 📡 Responsabilidades

- Expor endpoint REST para validação de comentários
- Validar se o texto contém palavras proibidas
- Manter lista de palavras proibidas em memória

### 🔌 Endpoints

```http
POST /api/moderate  # Validar comentário
```

### 🎮 Controller: ModerationController

#### `POST /api/moderate`
**Verifica se o texto possui palavras proibidas**

**Entrada (ModerationInput):**
```json
{
  "text": "string",
  "commentId": "UUID"
}
```

**Saída (ModerationOutput):**
```json
{
  "approved": true,
  "reason": "string"
}
```

**Resposta:**
- `200 OK` - aprovado ou reprovado

### 📦 DTOs

#### ModerationInput
```json
{
  "text": "string",
  "commentId": "UUID"
}
```

#### ModerationOutput
```json
{
  "approved": true,
  "reason": "string"
}
```

### ✔️ Regras de Validação

- 🚫 **Palavras Proibidas:** `["ódio", "xingamento"]`

### ⚙️ Requisitos Técnicos

- 💾 **Armazenamento:** Palavras proibidas em memória (lista fixa)

---

## 📋 Checklist de Tarefas

### CommentService
- [ ] Implementar endpoints REST
- [ ] Integração síncrona com ModerationService
- [ ] Persistência de comentários aprovados
- [ ] Configurar H2 como banco de dados
- [ ] Implementar paginação

### ModerationService
- [ ] Implementar endpoint POST /api/moderate
- [ ] Validação de palavras proibidas
- [ ] Manter lista em memória

### Configuração RestClient
- [ ] ⏱️ Read timeout de 5 segundos
- [ ] 🛡️ Tratamento de erros de integração
- [ ] 🛡️ Tratamento de timeout

### ✅ Garantias

- [ ] Códigos HTTP corretos em todas as respostas
- [ ] Testes de cenários:
  - ✨ Comentário válido e aprovado
  - 🚫 Comentário com palavras proibidas
  - ⏱️ Timeout na moderação
  - 404 Consulta de comentário inexistente

---

## 💡 Dicas de Implementação

- 📝 Registre logs nas operações importantes
- 🔌 Escolha a abordagem de implementação de RestClient que preferir
- 🧪 Teste cada microsserviço isoladamente antes de integrar
- 📊 Considere adicionar métricas de sucesso/falha nas chamadas
- 🔐 Valide entrada em ambos os microsserviços

---

## 🎯 Cenários de Teste

| Cenário | Entrada | Comportamento Esperado |
|---------|---------|------------------------|
| ✨ Comentário válido | texto sem palavras proibidas | 201 Created |
| 🚫 Palavras proibidas | contém "ódio" ou "xingamento" | 422 Unprocessable Entity |
| ⏱️ Timeout | demora > 5s no ModerationService | 504 Gateway Timeout |
| 🔍 Inexistente | GET com ID que não existe | 404 Not Found |
| 📄 Listagem | GET /api/comments com paginação | 200 OK com dados paginados |

## 🤝 Contributing

We welcome contributions! Please read our [contributing guidelines](CONTRIBUTING.md) before making changes.

## 📞 Contact

For questions or support, reach out to [support@yctus.com](mailto:support@yctus.com).
