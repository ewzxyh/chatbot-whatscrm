# Integracacao WhatsCRM + UazApi v2

> Documento de referencia para integrar o WhatsCRM com a UazApi v2, substituindo o Baileys pelo modelo de conexao via credenciais (igual ao ChatCase/UChat).

---

## Contexto

### O que e o CaseZap

- Plataforma central que gerencia instancias WhatsApp
- Usuarios escaneiam QR Code pelo CaseZap para conectar suas instancias
- Apos conectar, o CaseZap gera credenciais (token, numero, nome) para uso em servicos externos
- URL do painel UazApi: `https://uazapi.dev/interno?p=conecte`

### O que e a UazApi v2 (uazapiGO)

- API REST de WhatsApp hospedada (SaaS), reescrita em Go
- Substitui o Baileys (biblioteca Node.js) por uma API HTTP
- Servidor do cliente: `https://chatcase.uazapi.com`
- Cada instancia tem um token unico (UUID) gerado apos escanear o QR Code

### Como o ChatCase (UChat) conecta hoje

O ChatCase conecta **direto na UazApi** — nao passa pelo CaseZap para enviar/receber mensagens. O formulario pede:

| Campo              | Exemplo                                    |
|--------------------|--------------------------------------------|
| Numero do WhatsApp | `558195563386`                             |
| Dominio da Api     | `https://chatcase.uazapi.com`              |
| Token de instancia | `d9d10d7c-4c65-43a6-bf5d-e0be3d5d2723`     |
| Nome da instancia  | `Eric Business`                            |

### O WhatsCRM deve funcionar da mesma forma

O usuario ja conectou a instancia pelo CaseZap. No WhatsCRM, ele so informa as credenciais. O QR Code continua sendo lido exclusivamente pelo CaseZap.

---

## Arquitetura

```
CaseZap (hub central)
  - Gerencia instancias WhatsApp (QR Code)
  - Gera credenciais (token + numero + nome)
  - Tem seu proprio webhook global na UazApi (para features internas: leads, campanhas, etc.)

UazApi (servidor WhatsApp)
  - API REST: https://chatcase.uazapi.com
  - 195 instancias ativas
  - Webhook global: aponta para CaseZap (casezap.chatcase.com.br/api/webhooks/uazapi/global)
  - Webhook por instancia: configuravel via API (POST /webhook?token=TOKEN)

ChatCase / UChat (consumidor existente)
  - Conecta DIRETO na UazApi com token da instancia
  - ENVIA mensagens: chamadas REST para UazApi
  - RECEBE mensagens: webhook por instancia configurado na UazApi

WhatsCRM (novo consumidor)
  - Deve conectar DIRETO na UazApi com token da instancia (igual ChatCase)
  - ENVIA mensagens: chamadas REST para UazApi
  - RECEBE mensagens: webhook por instancia configurado na UazApi
  - NAO modifica o CaseZap
  - NAO interfere no webhook global
```

### Fluxo de conexao

```
1. Usuario escaneia QR Code no CaseZap
2. Instancia conecta na UazApi → gera token
3. Usuario abre WhatsCRM → tela de "Conectar Instancia"
4. Informa: numero, dominio da API, token, nome
5. WhatsCRM salva na tabela `instance`
6. WhatsCRM chama UazApi para configurar webhook por instancia
   POST {dominio}/webhook?token={token}
   { "url": "https://{WHATSCRM_DOMAIN}/api/qr/uazapi-webhook", "enabled": true, ... }
7. Instancia marcada como ACTIVE
```

### Fluxo de envio de mensagem

```
1. Usuario digita mensagem no inbox do WhatsCRM
2. WhatsCRM busca credenciais da instancia no banco (dominio + token)
3. WhatsCRM chama UazApi REST API:
   POST {dominio}/send/text?token={token}
   { "number": "5511999999999", "text": "Ola!" }
4. UazApi envia pelo WhatsApp
5. Resposta com messageId retorna ao WhatsCRM
```

### Fluxo de recebimento de mensagem

```
1. Mensagem chega no WhatsApp da instancia
2. UazApi detecta via webhook por instancia
3. UazApi faz POST para: https://{WHATSCRM_DOMAIN}/api/qr/uazapi-webhook
4. WhatsCRM recebe o payload, normaliza para o schema interno
5. Salva em beta_conversation e beta_chats
6. Dispara Socket.IO para atualizar o inbox em tempo real
7. Dispara chatbot, automacao e notificacao push (pipeline existente)
```

---

## Webhook global vs por instancia

| Tipo               | URL destino                                          | Usado por  |
|--------------------|------------------------------------------------------|------------|
| Webhook global     | `casezap.chatcase.com.br/api/webhooks/uazapi/global` | CaseZap    |
| Webhook instancia  | `{WHATSCRM_DOMAIN}/api/qr/uazapi-webhook`           | WhatsCRM   |
| Webhook instancia  | (endpoint do ChatCase)                               | ChatCase   |

- O webhook global NAO deve ser alterado
- Cada instancia pode ter seu proprio webhook via API: `POST /webhook?token=TOKEN`
- Quando ambos existem (global + instancia), a UazApi envia para ambos ou prioriza o da instancia

---

## Endpoints da UazApi utilizados

### Envio de mensagens

| Tipo      | Endpoint                         | Body                                                |
|-----------|----------------------------------|-----------------------------------------------------|
| Texto     | `POST /send/text?token=T`       | `{ "number": "55...", "text": "msg" }`              |
| Imagem    | `POST /send/media?token=T`      | `{ "number": "55...", "mediaUrl": "url", "type": "image", "caption": "..." }` |
| Video     | `POST /send/media?token=T`      | `{ "number": "55...", "mediaUrl": "url", "type": "video" }` |
| Audio     | `POST /send/media?token=T`      | `{ "number": "55...", "mediaUrl": "url", "type": "audio" }` |
| Documento | `POST /send/media?token=T`      | `{ "number": "55...", "mediaUrl": "url", "type": "document" }` |
| Localizacao | `POST /send/location?token=T` | `{ "number": "55...", "lat": "-8.05", "lng": "-34.87" }` |
| Contato   | `POST /send/contact?token=T`    | `{ "number": "55...", "contact": {...} }`           |

### Gerenciamento

| Acao                | Endpoint                           |
|---------------------|------------------------------------|
| Status da instancia | `GET /instance/status?token=T`     |
| Configurar webhook  | `POST /webhook?token=T`            |
| Verificar numero    | `POST /chat/check?token=T`         |
| Download de midia   | `POST /message/download?token=T`   |
| Info do contato     | `GET /chat/details?token=T`        |
| Marcar como lido    | `POST /message/markread?token=T`   |
| Reagir a mensagem   | `POST /message/react?token=T`      |

---

## Arquivos do WhatsCRM que serao modificados

### Arquivo principal: `helper/addon/qr/index.js`

Hoje (Baileys):
- `createSession()` → cria socket WhatsApp com makeWASocket
- `getSession()` → retorna socket da Map em memoria
- `sendMessage()` → session.sendMessage() via Baileys
- `isExists()` → session.onWhatsApp() via Baileys
- `deleteSession()` → session.logout() + remove da Map
- `init()` → restaura sessoes ativas do banco
- `checkQr()` → retorna true (feature flag)

Novo (UazApi):
- `createSession()` → salva credenciais no banco + configura webhook via API
- `getSession()` → retorna credenciais do banco (dominio + token)
- `sendMessage()` → POST /send/* na UazApi
- `isExists()` → POST /chat/check na UazApi
- `deleteSession()` → remove webhook + marca INACTIVE
- `init()` → verifica status das instancias ativas via GET /instance/status
- `checkQr()` → retorna true

### Arquivo de processamento: `helper/addon/qr/processThings.js`

Hoje (Baileys):
- `processBaileysMsg()` → parseia formato protobuf do Baileys
- `downloadMediaPromise()` → usa downloadMediaMessage do Baileys

Novo (UazApi):
- `processUazapiMsg()` → parseia formato JSON do webhook da UazApi
- `downloadMediaPromise()` → usa POST /message/download da UazApi

### Arquivo de envio: `helper/socket/function.js`

Hoje:
- `sendQrMsg()` → getSession() retorna socket, chama session.sendMessage()
- `sendNewMessage()` → mesmo padrao

Novo:
- `sendQrMsg()` → busca credenciais do banco, chama UazApi REST
- `sendNewMessage()` → mesmo padrao

### Nova rota: `routes/qr.js`

Hoje:
- `POST /gen_qr` → cria sessao Baileys com QR Code

Novo:
- `POST /gen_qr` → substituido por formulario de credenciais (ou nova rota)
- `POST /api/qr/uazapi-webhook` → endpoint para receber webhooks da UazApi
- `POST /connect_uazapi` → nova rota para conectar via credenciais

### Sem alteracao necessaria

- `helper/inbox/inbox.js` → ja recebe dados normalizados, nao muda
- `socket.js` → Socket.IO continua igual
- `automation/` → pipeline de chatbot/automacao continua igual
- Frontend → precisa de nova tela de formulario (numero, dominio, token, nome)

---

## Tabela `instance` — campos

| Campo      | Tipo         | Uso atual (Baileys)             | Uso novo (UazApi)                    |
|------------|--------------|---------------------------------|--------------------------------------|
| `id`       | int          | auto increment                  | sem mudanca                          |
| `uid`      | varchar(999) | ID do usuario WhatsCRM          | sem mudanca                          |
| `title`    | varchar(999) | nome da instancia               | nome da instancia (do CaseZap)       |
| `number`   | varchar(999) | numero extraido do Baileys      | numero informado pelo usuario        |
| `uniqueId` | varchar(999) | session ID (uid_random)         | pode ser o token da UazApi ou UUID   |
| `qr`       | longtext     | QR code base64                  | NAO USADO (QR fica no CaseZap)       |
| `data`     | longtext     | dados do usuario WA (JSON)      | credenciais UazApi (JSON): `{ "apiDomain": "...", "instanceToken": "...", "instanceName": "..." }` |
| `other`    | longtext     | status online (JSON)            | sem mudanca                          |
| `status`   | longtext     | GENERATING/ACTIVE/INACTIVE      | ACTIVE/INACTIVE (sem GENERATING)     |
| `createdAt`| timestamp    | auto                            | sem mudanca                          |

---

## Eventos de webhook da UazApi

Eventos que o WhatsCRM precisa escutar:

| Evento            | Descricao                                    |
|-------------------|----------------------------------------------|
| `messages`        | Mensagem recebida (texto, midia, etc.)       |
| `messages_update` | Status de entrega/leitura (sent/delivered/read) |
| `connection`      | Mudanca de status da conexao                 |

Eventos a excluir (reject):

| Evento            | Motivo                                       |
|-------------------|----------------------------------------------|
| `wasSentByApi`    | Evitar loop (mensagens enviadas pelo proprio WhatsCRM voltando como webhook) |
| `isGroupYes`      | WhatsCRM atual so processa chats individuais  |

---

## Dependencias

### Remover (Baileys)

- `baileys` (^7.0.0-rc.9)
- `mysql-baileys` (^1.5.3)
- `mongoSession.js` (custom MongoDB auth state)

### Manter

- `node-fetch` (ja existe no projeto — usado para chamadas HTTP)
- `qrcode` (nao mais necessario para QR, mas pode manter)
- Todas as outras dependencias do WhatsCRM

### Adicionar

- Nenhuma dependencia nova necessaria (`node-fetch` ja existe)

---

## Estimativa de esforco

| Componente                          | Esforco estimado |
|-------------------------------------|------------------|
| Reescrever `index.js` (addon)      | ~4h              |
| Adaptar `processThings.js`         | ~4h              |
| Adaptar `function.js` (senders)    | ~2h              |
| Nova rota de conexao + webhook     | ~3h              |
| Adaptar `routes/qr.js` (REST API)  | ~2h              |
| Frontend (formulario de credenciais)| ~3h              |
| Testes e ajustes                    | ~4h              |
| **Total**                           | **~22h (~3 dias)** |

---

## Requisitos para deploy

1. **Dominio publico para o WhatsCRM** — a UazApi precisa enviar webhooks via HTTP POST
2. **HTTPS obrigatorio** — UazApi nao envia webhooks para HTTP sem SSL
3. **Porta acessivel** — a rota `/api/qr/uazapi-webhook` precisa estar acessivel pela internet

---

## Decisoes pendentes

- [ ] O frontend do WhatsCRM ja tem tela de configuracao para Meta API — reutilizar o mesmo padrao visual?

### Decisoes resolvidas

- [x] ~~Qual sera o dominio publico do WhatsCRM?~~ **`app.chatcase.com.br`** — webhook URL: `https://app.chatcase.com.br/api/qr/uazapi-webhook`
- [x] ~~Confirmar formato do payload de webhook da UazApi~~ **FEITO.** Documentacao completa extraida da spec OpenAPI v2.0.1 e salva em `docs/uazapi/schemas/schemas.md`. Formato do evento `messages` confirmado com todos os campos.
- [x] ~~Precisa configurar webhook no painel da UazApi?~~ **NAO.** O WhatsCRM configura o webhook por instancia programaticamente via `POST /webhook` (header `token`), usando o dominio + token que o usuario informou. Totalmente separado do webhook global do CaseZap. Mesmo modelo que o ChatCase usa.

## Documentacao de referencia

Documentacao completa da UazApi v2 extraida e organizada em `docs/uazapi/`:

| Arquivo | Conteudo |
|---------|----------|
| `README.md` | Overview, auth, base URL |
| `instancia/endpoints.md` | create, connect, disconnect, status, etc. |
| `enviar-mensagem/endpoints.md` | text, media, contact, location, menu, carousel, etc. |
| `acoes-mensagem/endpoints.md` | find, download, markread, react, delete, edit |
| `webhook/endpoints.md` | GET/POST webhook, eventos, filtros, URLs dinamicas |
| `chats/endpoints.md` | find, details, check, read, archive, labels |
| `contatos/endpoints.md` | list, add, remove |
| `grupos/endpoints.md` | create, info, join, leave, list |
| `perfil/endpoints.md` | name, image, privacy |
| `admin/endpoints.md` | instance/create, globalwebhook |
| `mensagem-massa/endpoints.md` | sender/simple, sender/advanced |
| `schemas/schemas.md` | **Payloads de webhook** (messages, messages_update, connection, etc.) + schemas |
