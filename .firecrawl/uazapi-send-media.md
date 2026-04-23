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

POST`/send/media`

# Enviar mídia (imagem, vídeo, áudio ou documento)

Envia diferentes tipos de mídia para um contato, grupo ou canal/newsletter. Suporta URLs ou arquivos base64.

## Tipos de Mídia Suportados

- **`image`**: Imagens (JPG preferencialmente)
- **`video`**: Vídeos (apenas MP4)
- **`videoplay`**: Vídeo com comportamento visual de autoplay/loop no WhatsApp
- **`document`**: Documentos (PDF, DOCX, XLSX, etc)
- **`audio`**: Áudio comum (MP3 ou OGG)
- **`myaudio`**: Mensagem de voz (alternativa ao PTT)
- **`ptt`**: Mensagem de voz (Push-to-Talk)
- **`ptv`**: Mensagem de vídeo (Push-to-Video)
- **`sticker`**: Figurinha/Sticker

## Recursos Específicos

- **Upload por URL ou base64**
- **Caption/legenda** opcional com suporte a placeholders
- **Nome personalizado** para documentos (`docName`)
- **Geração automática de thumbnails**
- **Compressão otimizada** conforme o tipo
- **`viewOnce`** recomendado para mídia compatível

## Envio para Newsletter

Para enviar mídia para um canal, use o mesmo campo `number`, mas informe o JID completo do canal:

- Exemplo: `120363123456789012@newsletter`

```json
{
  "number": "120363123456789012@newsletter",
  "type": "image",
  "file": "https://exemplo.com/foto.jpg",
  "text": "Imagem publicada no canal"
}
```

## Campos Comuns

Este endpoint suporta todos os **campos opcionais comuns** documentados na tag **"Enviar Mensagem"**, incluindo:
`delay`, `readchat`, `readmessages`, `replyid`, `mentions`, `forward`, `track_source`, `track_id`, placeholders e envio para grupos.

## Exemplos Básicos

## Visualização Única (`viewOnce`)

O campo `viewOnce` é recomendado quando quiser mídia de visualização única e hoje produz efeito para os tipos:
`image`, `video`, `videoplay`, `ptv`, `audio`, `myaudio` e `ptt`.

Para `document`, `sticker` e demais endpoints de envio, o campo é ignorado silenciosamente.

### Imagem Simples

```json
{
  "number": "5511999999999",
  "type": "image",
  "file": "https://exemplo.com/foto.jpg"
}
```

### Documento com Nome

```json
{
  "number": "5511999999999",
  "type": "document",
  "file": "https://exemplo.com/contrato.pdf",
  "docName": "Contrato.pdf",
  "text": "Segue o documento solicitado"
}
```

### Request

#### Body

numberstringrequired

ID do chat para o qual a mensagem será enviada. Pode ser um número de telefone em formato internacional, um ID de grupo (`@g.us`), um ID de usuário (com `@s.whatsapp.net` ou `@lid`) ou um ID de canal/newsletter (`@newsletter`).

Example: "5511999999999"

typestringrequired

Tipo de mídia (image, video, videoplay, document, audio, myaudio, ptt, ptv, sticker)

Valores possíveis: image, video, videoplay, document, audio, myaudio, ptt, ptv, sticker

Example: "image"

filestringrequired

URL ou base64 do arquivo

Example: "https://exemplo.com/imagem.jpg"

textstring

Texto descritivo (caption) - aceita placeholders

Example: "Veja esta foto!"

docNamestring

Nome do arquivo (apenas para documents)

Example: "relatorio.pdf"

thumbnailstring

URL ou base64 de thumbnail personalizado para vídeos e documentos

Example: "https://exemplo.com/thumb.jpg"

mimetypestring

MIME type do arquivo (opcional, detectado automaticamente)

Example: "application/pdf"

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

Atraso em milissegundos antes do envio, durante o atraso apacerá 'Digitando...' ou 'Gravando áudio...'

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

viewOnceboolean

Recomendado para mídia com visualização única em tipos compatíveis (`image`, `video`, `videoplay`, `ptv`, `audio`, `myaudio`, `ptt`). Em tipos não compatíveis, o campo é ignorado silenciosamente.

### Responses

200Mídia enviada com sucesso

400Requisição inválida

401Não autorizado

413Arquivo muito grande

415Formato de mídia não suportado

500Erro interno do servidor

Try ItCode

POST`https://free.uazapi.com/send/media`

Subdomain

.uazapi.com

token \*

##### Body

\+ Novo

Imagem

Send API Request

No response yet

Send a request to see the actual response