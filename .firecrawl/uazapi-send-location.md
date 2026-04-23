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

Enviar mensagem de texto

POST

Enviar mídia (imagem, vídeo, áudio ou documento)

POST

Enviar cartão de contato (vCard)

POST

Enviar localização geográfica

POST

Enviar atualização de presença

POST

Enviar Stories (Status)

POST

Enviar menu interativo (botões, carrosel, lista ou enquete)

POST

Enviar carrossel de mídia com botões

POST

Solicitar localização do usuário

POST

Solicitar pagamento

POST

Enviar botão PIX

POST

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

POST`/send/location`

# Enviar localização geográfica

Envia uma localização geográfica para um contato ou grupo.

## Recursos Específicos

- **Coordenadas precisas** (latitude e longitude obrigatórias)
- **Nome do local** para identificação
- **Endereço completo** para exibição detalhada
- **Mapa interativo** no WhatsApp para navegação
- **Pin personalizado** com nome do local

## Campos Comuns

Este endpoint suporta todos os **campos opcionais comuns** documentados na tag **"Enviar Mensagem"**, incluindo:
`delay`, `readchat`, `readmessages`, `replyid`, `mentions`, `forward`, `track_source`, `track_id`, placeholders e envio para grupos.

## Exemplo Básico

```json
{
  "number": "5511999999999",
  "name": "Maracanã",
  "address": "Av. Pres. Castelo Branco - Maracanã, Rio de Janeiro - RJ",
  "latitude": -22.912982815767986,
  "longitude": -43.23028153499254
}
```

### Request

#### Body

numberstringrequired

ID do chat para o qual a mensagem será enviada. Pode ser um número de telefone em formato internacional, um ID de grupo (`@g.us`), um ID de usuário (com `@s.whatsapp.net` ou `@lid`).

Example: "5511999999999"

namestring

Nome do local

Example: "MASP"

addressstring

Endereço do local

Example: "Av. Paulista, 1578 - Bela Vista, São Paulo - SP"

latitudenumberrequired

Latitude (-90 a 90)

Example: -23.5616

longitudenumberrequired

Longitude (-180 a 180)

Example: -46.6562

replyidstring

ID da mensagem para responder

Example: "3EB0538DA65A59F6D8A251"

mentionsstring

Números para mencionar (separados por vírgula)

Example: "5511999999999,5511888888888"

readchatboolean

Marca conversa como lida após envio

Example: true

readmessagesboolean

Marca últimas mensagens recebidas como lidas

Example: true

delayinteger

Atraso em milissegundos antes do envio, durante o atraso apacerá 'Digitando...'

Example: 1000

forwardboolean

Marca a mensagem como encaminhada no WhatsApp

Example: true

track\_sourcestring

Origem do rastreamento da mensagem

Example: "chatwoot"

track\_idstring

ID para rastreamento da mensagem (aceita valores duplicados)

Example: "msg\_123456789"

asyncboolean

Se true, envia a mensagem de forma assíncrona via fila interna

### Responses

200Localização enviada com sucesso

400Requisição inválida

401Não autorizado

429Limite de requisições excedido

500Erro interno do servidor

Try ItCode

POST`https://free.uazapi.com/send/location`

Subdomain

.uazapi.com

token \*

##### Body

\+ Novo

Padrão

Send API Request

No response yet

Send a request to see the actual response