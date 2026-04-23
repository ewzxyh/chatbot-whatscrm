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

Baixar arquivo de uma mensagem

POST

Buscar mensagens em um chat

POST

Solicitar histórico sob demanda de um chat

POST

Marcar mensagens como lidas

POST

Enviar reação a uma mensagem

POST

Apagar Mensagem Para Todos

POST

Edita uma mensagem enviada

POST

Fixa ou desafixa uma mensagem

POST

Chats

9

Contatos

6

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

POST`/message/markread`

# Marcar mensagens como lidas

Marca uma ou mais mensagens como lidas. Este endpoint permite:

1. Marcar múltiplas mensagens como lidas de uma vez
2. Atualizar o status de leitura no WhatsApp
3. Sincronizar o status de leitura entre dispositivos

Exemplo de requisição básica:

```json
{
  "id": [\
    "62AD1AD844E518180227BF68DA7ED710",\
    "ECB9DE48EB41F77BFA8491BFA8D6EF9B"\
  ]
}
```

Exemplo de resposta:

```json
{
  "success": true,
  "message": "Messages marked as read",
  "markedMessages": [\
    {\
      "id": "62AD1AD844E518180227BF68DA7ED710",\
      "timestamp": 1672531200000\
    },\
    {\
      "id": "ECB9DE48EB41F77BFA8491BFA8D6EF9B",\
      "timestamp": 1672531300000\
    }\
  ]
}
```

Parâmetros disponíveis:

- id: Lista de IDs das mensagens a serem marcadas como lidas

Erros comuns:

- 401: Token inválido ou expirado
- 400: Lista de IDs vazia ou inválida
- 404: Uma ou mais mensagens não encontradas
- 500: Erro ao marcar mensagens como lidas

### Request

#### Body

idarrayrequired

Lista de IDs das mensagens a serem marcadas como lidas

Example: \["62AD1AD844E518180227BF68DA7ED710","ECB9DE48EB41F77BFA8491BFA8D6EF9B"\]

### Responses

200Messages successfully marked as read

400Invalid request payload or missing required fields

401Unauthorized - invalid or missing token

500Server error while processing the request

Try ItCode

POST`https://free.uazapi.com/message/markread`

Subdomain

.uazapi.com

token \*

##### Body

\+ Novo

Padrão

Send API Request

No response yet

Send a request to see the actual response