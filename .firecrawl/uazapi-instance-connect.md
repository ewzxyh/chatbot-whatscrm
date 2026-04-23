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

Conectar instância ao WhatsApp

POST

Desconectar instância

POST

Reiniciar runtime da instância

POST

Verificar status da instância

GET

Consultar limites atuais de novas conversas no WhatsApp

GET

Atualizar nome da instância

POST

Deletar instância

DELETE

Buscar configurações de privacidade

GET

Alterar configurações de privacidade

POST

Atualizar status de presença da instância

POST

Delay na fila de mensagens

POST

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

POST`/instance/connect`

# Conectar instância ao WhatsApp

Inicia o processo de conexão de uma instância ao WhatsApp. Este endpoint:

1. Requer o token de autenticação da instância
2. Recebe o número de telefone associado à conta WhatsApp
3. Gera um QR code caso não passe o campo `phone`
4. Ou Gera código de pareamento se passar o o campo `phone`
5. Atualiza o status da instância para "connecting"

O processo de conexão permanece pendente até que:

- O QR code seja escaneado no WhatsApp do celular, ou
- O código de pareamento seja usado no WhatsApp
- Timeout de 2 minutos para QRCode seja atingido ou 5 minutos para o código de pareamento

Use o endpoint /instance/status para monitorar o progresso da conexão.

Estados possíveis da instância:

- `disconnected`: Desconectado do WhatsApp
- `connecting`: Em processo de conexão
- `connected`: Conectado e autenticado

Sincronização e armazenamento de mensagens:

- Todas as mensagens recebidas da Meta durante a sincronização da conexão (leitura do QR code) são enviadas no evento `history` do webhook.
- As mensagens dos últimos 7 dias são armazenadas no banco de dados e ficam acessíveis pelos endpoints: `POST /message/find` e `POST /chat/find`.
- Depois que a instância conecta, todas as mensagens enviadas ou recebidas são armazenadas no banco de dados.
- Mensagens mais antigas do que 7 dias são excluídas durante a madrugada.

Exemplo de requisição:

```json
{
  "phone": "5511999999999"
}
```

### Request

#### Body

phonestring

Número de telefone no formato internacional (ex: 5511999999999). Se informado, gera código de pareamento. Se omitido, gera QR code.

Example: "5511999999999"

### Responses

200Sucesso

401Token inválido/expirado

404Instância não encontrada

429Limite de conexões simultâneas atingido

500Erro interno

Try ItCode

POST`https://free.uazapi.com/instance/connect`

Subdomain

.uazapi.com

token \*

##### Body

\+ Novo

Padrão

Send API Request

No response yet

Send a request to see the actual response