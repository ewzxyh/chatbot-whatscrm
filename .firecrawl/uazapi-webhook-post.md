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

Ver Webhook da Instância

GET

Configurar Webhook da Instância

POST

Ver últimos erros do webhook local

GET

Server-Sent Events (SSE)

GET

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

POST`/webhook`

# Configurar Webhook da Instância

Gerencia a configuração de webhooks para receber eventos em tempo real da instância.
Permite gerenciar múltiplos webhooks por instância através do campo ID e action.

### 🚀 Modo Simples (Recomendado)

**Uso mais fácil - sem complexidade de IDs**:

- Não inclua `action` nem `id` no payload
- Gerencia automaticamente um único webhook por instância
- Cria novo ou atualiza o existente automaticamente
- **Recomendado**: Sempre use `"excludeMessages": ["wasSentByApi"]` para evitar loops
- **Exemplo**: `{"url": "https://meusite.com/webhook", "events": ["messages"], "excludeMessages": ["wasSentByApi"]}`

### 🧪 Sites para Testes (ordenados por qualidade)

**Para testar webhooks durante desenvolvimento**:

1. **[https://webhook.cool/](https://webhook.cool/)** \- ⭐ Melhor opção (sem rate limit, interface limpa)
2. **[https://rbaskets.in/](https://rbaskets.in/)** \- ⭐ Boa alternativa (confiável, baixo rate limit)
3. **[https://webhook.site/](https://webhook.site/)** \- ⚠️ Evitar se possível (rate limit agressivo)

### ⚙️ Modo Avançado (Para múltiplos webhooks)

**Para usuários que precisam de múltiplos webhooks por instância**:

💡 **Dica**: Mesmo precisando de múltiplos webhooks, considere usar `addUrlEvents` no modo simples.
Um único webhook pode receber diferentes tipos de eventos em URLs específicas
(ex: `/webhook/message`, `/webhook/connection`), eliminando a necessidade de múltiplos webhooks.

1. **Criar Novo Webhook**:
   - Use `action: "add"`
   - Não inclua `id` no payload
   - O sistema gera ID automaticamente
2. **Atualizar Webhook Existente**:
   - Use `action: "update"`
   - Inclua o `id` do webhook no payload
   - Todos os campos serão atualizados
3. **Remover Webhook**:
   - Use `action: "delete"`
   - Inclua apenas o `id` do webhook
   - Outros campos são ignorados

### Eventos Disponíveis

- `connection`: Alterações no estado da conexão
- `history`: Recebimento de histórico de mensagens
- `messages`: Novas mensagens recebidas
- `messages_update`: Atualizações em mensagens existentes
- `newsletter_messages`: Novos posts/mensagens de canais do WhatsApp
Para views e reactions de canais, use a rota `/newsletter/updates`.
- `call`: Eventos de chamadas VoIP
- `contacts`: Atualizações na agenda de contatos
- `presence`: Alterações no status de presença
- `groups`: Modificações em grupos
- `labels`: Gerenciamento de etiquetas
- `chats`: Eventos de conversas
- `chat_labels`: Alterações em etiquetas de conversas
- `blocks`: Bloqueios/desbloqueios
- `sender`: Atualizações de campanhas, quando inicia, e quando completa

**Remover mensagens com base nos filtros**:

- `wasSentByApi`: Mensagens originadas pela API ⚠️ **IMPORTANTE:** Use sempre este filtro para evitar loops em automações
- `wasNotSentByApi`: Mensagens não originadas pela API
- `fromMeYes`: Mensagens enviadas pelo usuário
- `fromMeNo`: Mensagens recebidas de terceiros
- `isGroupYes`: Mensagens em grupos
- `isGroupNo`: Mensagens em conversas individuais

💡 **Prevenção de Loops**: Se você tem automações que enviam mensagens via API, sempre inclua `"excludeMessages": ["wasSentByApi"]` no seu webhook. Caso prefira receber esses eventos, certifique-se de que sua automação detecta mensagens enviadas pela própria API para não criar loops infinitos.

**Ações Suportadas**:

- `add`: Registrar novo webhook
- `delete`: Remover webhook existente

**Parâmetros de URL**:

- `addUrlEvents` (boolean): Quando ativo, adiciona o tipo do evento como path parameter na URL.
Exemplo: `https://api.example.com/webhook/{evento}`
- `addUrlTypesMessages` (boolean): Quando ativo, adiciona o tipo da mensagem como path parameter na URL.
Exemplo: `https://api.example.com/webhook/{tipo_mensagem}`

**Combinações de Parâmetros**:

- Ambos ativos: `https://api.example.com/webhook/{evento}/{tipo_mensagem}`
Exemplo real: `https://api.example.com/webhook/message/conversation`
- Apenas eventos: `https://api.example.com/webhook/message`
- Apenas tipos: `https://api.example.com/webhook/conversation`

**Notas Técnicas**:

1. Os parâmetros são adicionados na ordem: evento → tipo mensagem
2. A URL deve ser configurada para aceitar esses parâmetros dinâmicos
3. Funciona com qualquer combinação de eventos/mensagens

### Request

#### Body

idstring

ID único do webhook (necessário para update/delete)

Example: "123e4567-e89b-12d3-a456-426614174000"

enabledboolean

Habilita/desabilita o webhook

Example: true

urlstringrequired

URL para receber os eventos

Example: "https://example.com/webhook"

eventsarray

Lista de eventos monitorados

excludeMessagesarray

Filtros para excluir tipos de mensagens

addUrlEventsboolean

Adiciona o tipo do evento como parâmetro na URL.

- `false` (padrão): URL normal
- `true`: Adiciona evento na URL (ex: `/webhook/message`)

addUrlTypesMessagesboolean

Adiciona o tipo da mensagem como parâmetro na URL.

- `false` (padrão): URL normal
- `true`: Adiciona tipo da mensagem (ex: `/webhook/conversation`)

actionstring

Ação a ser executada:

- add: criar novo webhook
- update: atualizar webhook existente (requer id)
- delete: remover webhook (requer apenas id)
Se não informado, opera no modo simples (único webhook)

Valores possíveis: add, update, delete

### Responses

200Webhook configurado ou atualizado com sucesso

400Requisição inválida

401Token inválido ou não fornecido

500Erro interno do servidor

Try ItCode

POST`https://free.uazapi.com/webhook`

Subdomain

.uazapi.com

token \*

##### Body

\+ Novo

Exemplo Modo Simples (Recomendado)

Send API Request

No response yet

Send a request to see the actual response