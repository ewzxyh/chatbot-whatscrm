![uazapiGO](https://docs.uazapi.com/uazapi-logo.png)

# uazapiGO V2

API Documentation

![uazapiGO](https://docs.uazapi.com/uazapi-logo.png)

uazapiGO V2

Docs & API

Overview

Monitor de Eventos

ENDPOINTS

134

Admininstração

7

Instancia

11

Proxy

3

Perfil

2

Business

8

Chamadas

2

Webhooks e SSE

4

Enviar Mensagem

11

Mensagem Async

2

Ações na mensagem e Buscar

8

Chats

9

Contatos

6

Retorna lista de contatos do WhatsApp

GET

Listar todos os contatos com paginacao

POST

Adiciona um contato à agenda

POST

Remove um contato da agenda

POST

Obter Detalhes Completos

POST

Verificar Números no WhatsApp

POST

Bloqueios

2

Etiquetas

4

Grupos e Comunidades

16

Newsletters e Canais

26

Respostas Rápidas

2

CRM

2

Mensagem em massa

7

Integração Chatwoot

2

SCHEMAS

11

POST`/chat/check`

# Verificar Números no WhatsApp

Verifica se números fornecidos estão registrados no WhatsApp e retorna informações detalhadas.

### Funcionalidades:

- Verifica múltiplos números simultaneamente
- Suporta números individuais e IDs de grupo
- Retorna nome verificado quando disponível
- Identifica grupos e comunidades
- Verifica subgrupos de comunidades

**Comportamento específico**:

- Para números individuais:
  - Verifica registro no WhatsApp
  - Retorna nome verificado se disponível
  - Normaliza formato do número
- Para grupos:
  - Verifica existência
  - Retorna nome do grupo
  - Retorna id do grupo de anúncios se buscado por id de comunidade

### Request

#### Body

numbersarray

Lista de números ou IDs de grupo para verificar

Example: \["5511999999999","redacted@example.invalid"\]

### Responses

200Resultado da verificação

400Payload inválido ou sem números

401Sem sessão ativa

500Erro interno do servidor

Try ItCode

POST`https://free.uazapi.com/chat/check`

Subdomain

.uazapi.com

token \*

##### Body

\+ Novo

Padrão

Send API Request

No response yet

Send a request to see the actual response