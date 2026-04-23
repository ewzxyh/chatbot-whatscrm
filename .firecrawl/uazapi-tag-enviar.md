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

TAG

# Enviar Mensagem

Endpoints para envio de mensagens do WhatsApp com diferentes tipos de conteúdo.

## Campos Opcionais Comuns

Todos os endpoints de envio de mensagem suportam os seguintes campos opcionais:

- **`delay`** _(integer)_: Atraso em milissegundos antes do envio
  - Durante o atraso aparecerá "Digitando..." ou "Gravando áudio..." dependendo do tipo
  - Exemplo: `5000` (5 segundos)
- **`readchat`** _(boolean)_: Marcar chat como lido após envio
  - Remove o contador de mensagens não lidas do chat
  - Exemplo: `true`
- **`readmessages`** _(boolean)_: Marcar últimas mensagens recebidas como lidas
  - Marca as últimas 10 mensagens **recebidas** (não enviadas por você) como lidas
  - Útil para confirmar leitura de mensagens pendentes antes de responder
  - Diferente do `readchat` que apenas remove contador de não lidas
  - Exemplo: `true`
- **`replyid`** _(string)_: ID da mensagem para responder
  - Cria uma resposta vinculada à mensagem original
  - Suporte varia por tipo de mensagem
- **`viewOnce`** _(boolean)_: Recomendado quando quiser mídia de visualização única
  - Use em `/send/media` com `type` compatível (`image`, `video`, `videoplay`, `ptv`, `audio`, `myaudio`, `ptt`)
  - Nos demais endpoints de envio, o campo é aceito, mas é ignorado silenciosamente
  - Exemplo: `"3A12345678901234567890123456789012"`
- **`mentions`** _(string)_: Números para mencionar (apenas para envio em grupos)
  - Números específicos: `"5511999999999,5511888888888"`
  - Mencionar todos: `"all"`
- **`forward`** _(boolean)_: Marca a mensagem como encaminhada no WhatsApp
  - Adiciona o indicador "Encaminhada" na mensagem
  - Exemplo: `true`
- **`track_source`** _(string)_: Origem do rastreamento da mensagem
  - Identifica o sistema ou fonte que está enviando a mensagem
  - Útil para integrações (ex: "chatwoot", "crm", "chatbot")
  - Exemplo: `"chatwoot"`
- **`track_id`** _(string)_: ID para rastreamento da mensagem
  - Identificador livre para acompanhar a mensagem em sistemas externos
  - Permite correlacionar mensagens entre diferentes plataformas
  - **Nota**: O sistema aceita valores duplicados - não há validação de unicidade
  - Use o mesmo ID em várias mensagens se fizer sentido para sua integração
  - Exemplo: `"msg_123456789"`
- **`async`** _(boolean)_: Envia pela fila interna sem bloquear a requisição
  - Resposta 200 indica que a mensagem entrou na fila; o envio real pode falhar depois
  - Em caso de falha, pesquise em `/message/find` com `status=failed`

## Diagnóstico de limites do WhatsApp

Em alguns casos, o WhatsApp pode recusar novas conversas por regras próprias de volume, qualidade ou limitação temporária.

Quando isso acontecer, a resposta de erro pode incluir:

- `error_source: "whatsapp_server"`
- `provider: "whatsapp"`
- `provider_code: 463`
- `error_key`
- `message`
- `message_ptbr`
- `provider_message`
- `provider_message_ptbr`
- `diagnostics_endpoint`
- `details.new_chat_message_capping`
- `details.reachout_timelock`

Para consultar o estado atual desses limites para a conta atualmente conectada, use:

- `GET /instance/wa_messages_limits`

### Envio para Grupos

- **`number`** _(string)_: Para enviar mensagem para grupo, use o ID do grupo que termina com `@g.us`
  - Exemplo: `"redacted@example.invalid"`
  - **Como obter o ID do grupo:**
    - Use o `chatid` do webhook recebido quando alguém envia mensagem no grupo
    - Use o endpoint `GET /group/list` para listar todos os grupos e seus IDs

## Placeholders Disponíveis

Todos os endpoints de envio de mensagem suportam placeholders dinâmicos para personalização automática:

### Campos de Nome

- **`{{name}}`**: Nome consolidado do chat, usando a primeira opção disponível:
1. Nome do lead (`lead_name`)
2. Nome completo do lead (`lead_fullName`)
3. Nome do contato no WhatsApp (`wa_contactName`)
4. Nome do perfil do WhatsApp (`wa_name`)
- **`{{first_name}}`**: Primeira palavra válida do nome consolidado (mínimo 2 caracteres)


### Campos do WhatsApp

- **`{{wa_name}}`**: Nome do perfil do WhatsApp
- **`{{wa_contactName}}`**: Nome do contato como salvo no WhatsApp

### Campos do Lead

- **`{{lead_name}}`**: Nome do lead
- **`{{lead_fullName}}`**: Nome completo do lead
- **`{{lead_personalid}}`**: ID pessoal (CPF, CNPJ, etc)
- **`{{lead_email}}`**: Email do lead
- **`{{lead_status}}`**: Status atual do lead
- **`{{lead_notes}}`**: Anotações do lead
- **`{{lead_assignedAttendant_id}}`**: ID do atendente designado

### Campos Personalizados

Campos adicionados via custom fields são acessíveis usando `{{lead_field01}}` à `{{lead_field20}}` ou usar `{{nomedocampo}}` definido em `/instance/updateFieldsMap`.

### Exemplo de Uso

```
Olá {{name}}! Vi que você trabalha na {{company}}.
Seu email {{lead_email}} está correto?
```

**💡 Dica**: Use `/chat/find` para buscar dados do chat e ver os campos disponíveis antes de enviar mensagens com placeholders.

POST

### Enviar mensagem de texto

POST

### Enviar mídia (imagem, vídeo, áudio ou documento)

POST

### Enviar cartão de contato (vCard)

POST

### Enviar localização geográfica

POST

### Enviar atualização de presença

POST

### Enviar Stories (Status)

POST

### Enviar menu interativo (botões, carrosel, lista ou enquete)

POST

### Enviar carrossel de mídia com botões

POST

### Solicitar localização do usuário

POST

### Solicitar pagamento

POST

### Enviar botão PIX

Select an endpoint to test