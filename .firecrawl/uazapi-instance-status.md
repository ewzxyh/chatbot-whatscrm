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

GET`/instance/status`

# Verificar status da instância

Retorna o status atual de uma instância, incluindo:

- Estado da conexão (disconnected, connecting, connected)
- QR code atualizado (se em processo de conexão)
- Código de pareamento (se disponível)
- Informações da última desconexão
- Detalhes completos da instância

Este endpoint é particularmente útil para:

1. Monitorar o progresso da conexão
2. Obter QR codes atualizados durante o processo de conexão
3. Verificar o estado atual da instância
4. Identificar problemas de conexão

Estados possíveis:

- `disconnected`: Desconectado do WhatsApp
- `connecting`: Em processo de conexão (aguardando QR code ou código de pareamento)
- `connected`: Conectado e autenticado com sucesso

### Responses

200Sucesso

401Token inválido/expirado

404Instância não encontrada

500Erro interno

Try ItCode

GET`https://free.uazapi.com/instance/status`

Subdomain

.uazapi.com

token \*

Send API Request

No response yet

Send a request to see the actual response