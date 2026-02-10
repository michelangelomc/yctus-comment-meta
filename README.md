# yctus-comment-meta
centralizador

Implemente o sistema: AlgaComments
Você deve desenvolver um sistema composto por dois microsserviços que se comunicam de forma síncrona via HTTP/REST usando Spring RestClient. O sistema será responsável por receber comentários de usuários, validá-los contra palavras proibidas e armazenar apenas os aprovados.

O sistema deve permitir:

Criar um novo comentário e enviar ele para moderação
Consultar os detalhes de um comentário aprovado
Listar comentários aprovados com paginação
1. Microsserviço: CommentService
Expor uma API REST para criação e consulta de comentários
Enviar novos comentários para moderação via POST síncrono usando RestClient
Armazenar apenas comentários aprovados
Endpoints
POST /api/comments
GET /api/comments/{id}
GET /api/comments
Controller CommentController
POST /api/comments:
Cria um novo comentário
Recebe: CommentInput
Retorna:
201 Created com CommentOutput se aprovado
422 Unprocessable Entity com motivo se rejeitado
GET /api/comments/{id}:
Retorna 200 OK com CommentOutput se existir
Retorna 404 Not Found se não existir
GET /api/comments:
Retorna lista paginada de comentários aprovados
Parâmetros: page, size
Estrutura de resposta:
{
  "page": 0,
  "size": 10,
  "totalElements": 45,
  "totalPages": 5,
  "content": [ /* lista de CommentOutput */ ]
}
DTOs
CommentInput - Modelo para criação:
{
  "text": "string",
  "author": "string"
}
CommentOutput - Modelo de exibição:
{
  "id": "string", //UUID
  "text": "string",
  "author": "string",
  "createdAt": "2023-11-15T10:00:00Z"
}
Integração com o ModerationService
O CommentService faz uma chamada POST para /api/moderate do ModerationService com o corpo:

{
  "text": "string",
  "commentId": "string" //UUID
}
Resposta esperada:

{
  "approved": true,
  "reason": "string"
}
Regras de Validação
id deve ser UUID
Comentários rejeitados não são armazenados
Requisitos Técnicos
Use H2 para persistência
Configure timeout de 5 segundos para chamadas ao ModerationService
Use RestClient para comunicação síncrona
Trate adequadamente:
Erros gerais na integração (retorne 502)
Erros de timeout na integração (retorne 504)
Comentário não encontrado (404)
2. Microsserviço: ModerationService
Expor um endpoint REST para validação de comentários
Validar se o texto contém palavras proibidas (lista fixa)
Endpoint
POST /api/moderate
Controller ModerationController
POST /api/moderate:
Verifica se o texto enviado possui palavras proibidas
Recebe: ModerationInput
Retorna:
200 OK com ModerationOutput se aprovado ou reprovado
DTOs
ModerationInput - Modelo para solicitação:
{
  "text": "string",
  "commentId": "string" //UUID
}
ModerationOutput - Modelo de resultado:
{
  "approved": true,
  "reason": "string"
}
Regras de Validação
Lista fixa de palavras proibidas no ModerationService: ["ódio", "xingamento"]
Requisitos Técnicos
Mantenha a lista de palavras proibidas em memória
3. Tarefas do Desafio
Implemente o CommentService com:
Endpoints REST
Integração síncrona com ModerationService
Persistência de comentários aprovados
Implemente o ModerationService com:
Endpoint POST /api/moderate
Validação de palavras proibidas
Configure o RestClient:
Read timeout de 5 segundos
Tratamento de erros
Garanta que:
Respostas HTTP seguem os códigos adequados
Teste os cenários:
Comentário válido
Comentário com palavras proibidas
Timeout na moderação
Consulta de comentário inexistente
Dicas
Registre logs nas operações importantes
Opte pela abordagem de implementação de RestClient que desejar
