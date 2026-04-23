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

POST`/send/text`

# Enviar mensagem de texto

Envia uma mensagem de texto para um contato, grupo ou canal/newsletter.

## Recursos Específicos

- **Preview de links** com suporte a personalização automática ou customizada
- **Formatação básica** do texto
- **Substituição automática de placeholders** dinâmicos

## Envio para Newsletter

Para enviar texto para um canal, use o mesmo campo `number`, mas informe o JID completo do canal:

- Exemplo: `120363123456789012@newsletter`

```json
{
  "number": "120363123456789012@newsletter",
  "text": "Post publicado no canal"
}
```

## Campos Comuns

Este endpoint suporta todos os **campos opcionais comuns** documentados na tag **"Enviar Mensagem"**, incluindo:
`delay`, `readchat`, `readmessages`, `replyid`, `mentions`, `forward`, `track_source`, `track_id`, placeholders e envio para grupos.

## Preview de Links

### Preview Automático

```json
{
  "number": "5511999999999",
  "text": "Confira: https://exemplo.com",
  "linkPreview": true
}
```

### Preview Personalizado

```json
{
  "number": "5511999999999",
  "text": "Confira nosso site! https://exemplo.com",
  "linkPreview": true,
  "linkPreviewTitle": "Título Personalizado",
  "linkPreviewDescription": "Uma descrição personalizada do link",
  "linkPreviewImage": "https://exemplo.com/imagem.jpg",
  "linkPreviewLarge": true
}
```

### Request

#### Body

numberstringrequired

ID do chat para o qual a mensagem será enviada. Pode ser um número de telefone em formato internacional, um ID de grupo (`@g.us`), um ID de usuário (com `@s.whatsapp.net` ou `@lid`) ou um ID de canal/newsletter (`@newsletter`).

Example: "5511999999999"

textstringrequired

Texto da mensagem (aceita placeholders)

Example: "Olá {{name}}! Como posso ajudar?"

linkPreviewboolean

Ativa/desativa preview de links. Se true, procura automaticamente um link no texto para gerar preview.

Comportamento:

- Se apenas linkPreview=true: gera preview automático do primeiro link encontrado no texto
- Se fornecidos campos personalizados (title, description, image): usa os valores fornecidos
- Se campos personalizados parciais: combina com dados automáticos do link como fallback

Example: true

linkPreviewTitlestring

Define um título personalizado para o preview do link

Example: "Título Personalizado"

linkPreviewDescriptionstring

Define uma descrição personalizada para o preview do link

Example: "Descrição personalizada do link"

linkPreviewImagestring

URL ou Base64 da imagem para usar no preview do link

Example: "https://exemplo.com/imagem.jpg"

linkPreviewLargeboolean

Se true, gera um preview grande com upload da imagem. Se false, gera um preview pequeno sem upload

Example: true

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

Se true, envia a mensagem de forma assíncrona via fila interna. Útil para alto volume de mensagens.

### Responses

200Mensagem enviada com sucesso

400Requisição inválida

401Não autorizado

429Limite de requisições excedido

500Erro interno do servidor

Try ItCode

POST`https://free.uazapi.com/send/text`

Subdomain

.uazapi.com

token \*

##### Body

\+ Novo

Mensagem básica

Send API Request

No response yet

Send a request to see the actual response