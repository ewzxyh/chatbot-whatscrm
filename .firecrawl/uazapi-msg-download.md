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

POST`/message/download`

# Baixar arquivo de uma mensagem

Baixa o arquivo associado a uma mensagem de mídia (imagem, vídeo, áudio, documento ou sticker).

## Parâmetros

- **id** (string, obrigatório): ID da mensagem
- **return\_base64** (boolean, default: false): Retorna arquivo em base64
- **generate\_mp3**(boolean, default: true): Para áudios, define formato de retorno

  - `true`: Retorna MP3
  - `false`: Retorna OGG
- **return\_link** (boolean, default: true): Retorna URL pública do arquivo
- **transcribe** (boolean, default: false): Transcreve áudios para texto
- **openai\_apikey**(string, opcional): Chave OpenAI para transcrição

  - Se não informada, usa a chave salva na instância
  - Se informada, atualiza e salva na instância para próximas chamadas
- **download\_quoted**(boolean, default: false): Baixa mídia da mensagem citada

  - Útil para baixar conteúdo original de status do WhatsApp
  - Quando uma mensagem é resposta a um status, permite baixar a mídia do status original
  - **Contextualização**: Ao baixar a mídia citada, você identifica o contexto da conversa

    - Exemplo: Se alguém responde a uma promoção, baixando a mídia você saberá que a pergunta é sobre aquela promoção específica

## Exemplos

### Baixar áudio como MP3:

```json
{
  "id": "7EB0F01D7244B421048F0706368376E0",
  "generate_mp3": true
}
```

### Transcrever áudio:

```json
{
  "id": "7EB0F01D7244B421048F0706368376E0",
  "transcribe": true
}
```

### Apenas base64 (sem salvar):

```json
{
  "id": "7EB0F01D7244B421048F0706368376E0",
  "return_base64": true,
  "return_link": false
}
```

### Baixar mídia de status (mensagem citada):

```json
{
  "id": "7EB0F01D7244B421048F0706368376E0",
  "download_quoted": true
}
```

_Útil quando o cliente responde a uma promoção/status - você baixa a mídia original para entender sobre qual produto/oferta ele está perguntando._

## Resposta

```json
{
  "fileURL": "https://api.exemplo.com/files/arquivo.mp3",
  "mimetype": "audio/mpeg",
  "base64Data": "UklGRkj...",
  "transcription": "Texto transcrito"
}
```

**Nota**:

- Por padrão, se não definido o contrário:
1. áudios são retornados como MP3.
2. E todos os pedidos de download são retornados com URL pública.
- Transcrição requer chave OpenAI válida. A chave pode ser configurada uma vez na instância e será reutilizada automaticamente.
- Retenção de mídia: mantemos as mídias no nosso storage por 2 dias. Após 2 dias, elas são removidas na limpeza automática e o link retornado deixa de ficar disponível. Para voltar a disponibilizar a mídia, é necessário refazer o download pelo endpoint. Se o cliente solicitar novamente, a mídia será baixada do CDN da Meta, o que pode aumentar o tempo de resposta. Enquanto estiver no nosso storage, a resposta tende a ser mais rápida.

### Request

#### Body

idstringrequired

ID da mensagem contendo o arquivo

Example: "7EB0F01D7244B421048F0706368376E0"

return\_base64boolean

Se verdadeiro, retorna o conteúdo em base64

generate\_mp3boolean

Para áudios, define formato de retorno (true=MP3, false=OGG)

return\_linkboolean

Salva e retorna URL pública do arquivo

transcribeboolean

Se verdadeiro, transcreve áudios para texto

openai\_apikeystring

Chave da API OpenAI para transcrição (opcional)

Example: "sk-..."

download\_quotedboolean

Se verdadeiro, baixa mídia da mensagem citada ao invés da mensagem principal

### Responses

200Successful file download

400Bad Request

401Unauthorized

404Not Found

500Internal Server Error

Try ItCode

POST`https://free.uazapi.com/message/download`

Subdomain

.uazapi.com

token \*

##### Body

\+ Novo

Padrão

Send API Request

No response yet

Send a request to see the actual response