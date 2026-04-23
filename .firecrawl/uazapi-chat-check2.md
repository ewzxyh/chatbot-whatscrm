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

# uazapiGO - WhatsApp API

v2.0.1

API para gerenciamento de instâncias do WhatsApp e comunicações.

## ⚠️ Recomendação Importante: WhatsApp Business

**É ALTAMENTE RECOMENDADO usar contas do WhatsApp Business** em vez do WhatsApp normal para integração, o WhatsApp normal pode apresentar inconsistências, desconexões, limitações e instabilidades durante o uso com a nossa API.

## Autenticação

- Endpoints regulares requerem um header 'token' com o token da instância
- Endpoints administrativos requerem um header 'admintoken'

## Estados da Instância

As instâncias podem estar nos seguintes estados:

- `disconnected`: Desconectado do WhatsApp
- `connecting`: Em processo de conexão
- `connected`: Conectado e autenticado com sucesso

## Limites de Uso

- O servidor possui um limite máximo de instâncias conectadas
- Quando o limite é atingido, novas tentativas receberão erro 429
- Servidores gratuitos/demo podem ter restrições adicionais de tempo de vida

134

Endpoints

11

Schemas

2

Security

1

Servers

### Baixar Especificação OpenAPI

Baixe a especificação OpenAPI completa com todas as referências resolvidas e pronta para uso.

Baixar Especificação

### API Servers

`https://{subdomain}.uazapi.com`Servidor da API uazapiGO

Select an endpoint, webhook, or schema from the sidebar to view documentation

Select an endpoint to test