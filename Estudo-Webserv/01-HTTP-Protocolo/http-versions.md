---
tags: [webserv, http, versoes, protocolo]
fonte-principal: rfc-2616
status: revisado
---

# HTTP Versions

## TL;DR
HTTP/1.0 e HTTP/1.1 são as duas versões principais do protocolo. HTTP/1.1 é a padrão hoje. O subject sugere HTTP/1.0 como referência de estudo. ✅ `Fonte: subject Webserv 42`

## Contexto no Webserv
O subject diz: "HTTP 1.0 é sugerido como um ponto de referência, mas não é obrigatório." ✅ `Fonte: subject Webserv 42` O servidor pode implementar HTTP/1.1 ou 1.0, desde que funcione com navegadores web. ✅ `Fonte: subject Webserv 42, IV.1`

## Como funciona
| Característica | HTTP/1.0 | HTTP/1.1 |
|----------------|----------|-----------|
| Conexão | Fecha após cada requisição | Persistente por padrão |
| Host header | Opcional | Obrigatório |
| Métodos | GET, HEAD, POST | GET, HEAD, POST, PUT, DELETE, OPTIONS, etc. |
| Caching | Basic (Expires) | Elaborado (Cache-Control, ETag) |
| Chunked transfer | Não | Sim |
| Status codes | Básicos | Expandidos |

> [!note]- 🔍 Aprofundando: o que muda no Webserv
> Na prática para o projeto:
> - O servidor provavelmente usará HTTP/1.1 nos responses.
> - Se usar HTTP/1.0, a conexão é fechada após cada resposta (mais simples).
> - O subject não exige suporte a todas as features de HTTP/1.1 (virtual hosts está fora de escopo). ✅ `Fonte: subject Webserv 42, IV.1`
> - Pode-se comparar com NGINX para ver diferenças entre versões. ✅ `Fonte: subject Webserv 42, IV.1`
>
> **Conexões:**
> - `Complemento:` [[http-request-format]]
> - `Complemento:` [[keep-alive]]
> - `Complemento:` [[http-headers]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não exige HTTP/1.1 completo. Apenas o subconjunto necessário para servir um site estático.
- HTTP/2 não é mencionado.
- A escolha entre HTTP/1.0 e HTTP/1.1 afeta a implementação de keep-alive e chunked transfer.

## Perguntas de autoavaliação
- Um navegador moderno funciona com um servidor HTTP/1.0?
- Qual versão usar no Status-Line da resposta?
- O que muda no parsing se o servidor suportar HTTP/1.1 vs 1.0?

## Fontes consultadas
- subject Webserv 42, páginas 8-9
- RFC 1945 (HTTP/1.0)
- RFC 2616 (HTTP/1.1)
