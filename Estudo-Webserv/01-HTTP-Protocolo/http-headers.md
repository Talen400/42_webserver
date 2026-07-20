---
tags: [webserv, http, protocolo, headers]
fonte-principal: rfc-2616
status: revisado
---

# HTTP Headers

## TL;DR
Headers são pares `nome: valor` que transportam metadados sobre a requisição ou resposta. O servidor precisa ler headers de requisição e escrever headers de resposta corretamente.

## Contexto no Webserv
O servidor deve parsear headers da requisição (Content-Length, Host, Content-Type) e gerar headers de resposta (Content-Type, Content-Length, Location, Connection). ✅ `Fonte: subject Webserv 42`

## Como funciona
Headers seguem o formato: `Nome: valor\r\n`

**Headers de requisição relevantes:**
- `Host`: domínio do servidor (opcional no escopo do subject)
- `Content-Length`: tamanho do corpo em bytes
- `Content-Type`: tipo MIME do corpo (multipart/form-data para uploads)
- `Transfer-Encoding`: chunked (se aplicável)

**Headers de resposta relevantes:**
- `Content-Type`: tipo MIME do recurso (text/html, text/plain, image/png, etc.)
- `Content-Length`: tamanho do corpo em bytes
- `Location`: usado em redirects (301/302)
- `Connection`: close ou keep-alive

> [!note]- 🔍 Aprofundando: Content-Type por extensão
> O servidor deve mapear extensões de arquivo para MIME types. Ex:
> - `.html` → `text/html`
> - `.css` → `text/css`
> - `.js` → `application/javascript`
> - `.png` → `image/png`
> - `.jpg` → `image/jpeg`
> - `.pdf` → `application/pdf`
> - Extensão desconhecida → `application/octet-stream`
>
> **Conexões:**
> - `Complemento:` [[static-file-serving]]
> - `Complemento:` [[http-request-format]]
> - `Complemento:` [[http-response-format]]

## O que o subject NÃO exige (mas é bom saber)
- Headers de cache (Cache-Control, ETag, Last-Modified) não são exigidos.
- Cookie/Set-Cookie são bônus. ✅ `Fonte: subject Webserv 42, VI`
- O header `Server` não é exigido, mas é boa prática.
- CORS headers não são necessários.

## Perguntas de autoavaliação
- O que fazer se o header `Content-Length` estiver ausente em um POST?
- Como o servidor determina o `Content-Type` de um arquivo sem extensão?
- É seguro confiar no `Content-Length` enviado pelo cliente?

## Fontes consultadas
- RFC 2616, seções 4.2, 7.1, 14
- subject Webserv 42, páginas 7-12
