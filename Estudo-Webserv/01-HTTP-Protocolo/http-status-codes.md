---
tags: [webserv, http, protocolo, status-codes]
fonte-principal: rfc-2616
status: revisado
---

# HTTP Status Codes

## TL;DR
Cada resposta HTTP tem um código de três dígitos que indica o resultado da requisição. O subject exige que os códigos sejam precisos. ✅ `Fonte: subject Webserv 42, IV.1`

## Contexto no Webserv
O servidor deve escolher o código correto para cada situação. Códigos errados podem levar a nota 0 na avaliação. É referência comparar com NGINX. ✅ `Fonte: subject Webserv 42, IV.1`

## Como funciona

| Código | Classe | Significado | Uso no Webserv |
|--------|--------|-------------|----------------|
| 200 | Sucesso | OK | GET/POST bem-sucedidos |
| 204 | Sucesso | No Content | DELETE bem-sucedido (sem corpo) |
| 301 | Redirecionamento | Moved Permanently | Redirect configurado |
| 302 | Redirecionamento | Found | Redirect temporário |
| 400 | Erro cliente | Bad Request | Requisição mal formatada |
| 403 | Erro cliente | Forbidden | Sem permissão de arquivo |
| 404 | Erro cliente | Not Found | Recurso inexistente |
| 405 | Erro cliente | Method Not Allowed | Método não permitido na rota |
| 413 | Erro cliente | Payload Too Large | Corpo excede client-max-body-size |
| 500 | Erro servidor | Internal Server Error | Erro interno (CGI falhou, etc.) |
| 501 | Erro servidor | Not Implemented | Método não implementado |

> [!note]- 🔍 Aprofundando: escolha do código
> - **200**: recurso encontrado e servido com sucesso.
> - **204**: DELETE bem-sucedido (sem corpo na resposta).
> - **301/302**: quando configurado no location block.
> - **400**: request-line mal formatada ou headers inválidos.
> - **403**: arquivo existe mas permissão de leitura negada.
> - **404**: arquivo não encontrado no sistema de arquivos.
> - **405**: verificar lista `allow_methods` do location.
> - **413**: comparar `Content-Length` com `client_max_body_size`.
> - **500**: erro genérico (CGI retornou erro, pipe quebrado, etc.).
>
> **Conexões:**
> - `Complemento:` [[error-handling]]
> - `Complemento:` [[http-response-format]]

## O que o subject NÃO exige (mas é bom saber)
- Códigos 1xx (informacionais) não são usados em HTTP/1.0 e não são exigidos.
- Código 505 (HTTP Version Not Supported) pode ser necessário se o cliente usar HTTP/1.0 e o servidor só suportar 1.1. ⚠️ não verificado
- Códigos 207, 304, etc. não são relevantes para o escopo.

## Perguntas de autoavaliação
- Quando retornar 403 vs 404?
- Quando retornar 405 vs 501?
- O que acontece se o CGI retornar um código de status próprio?

## Fontes consultadas
- subject Webserv 42, página 9
- RFC 2616, seção 10
