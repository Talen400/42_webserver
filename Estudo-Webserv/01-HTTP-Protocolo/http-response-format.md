---
tags: [webserv, http, protocolo, response]
fonte-principal: rfc-2616
status: revisado
---

# HTTP Response Format

## TL;DR
Toda resposta HTTP começa com uma linha de status (versão, código, frase), seguida por cabeçalhos e um corpo opcional. É o que o servidor envia de volta para o cliente.

## Contexto no Webserv
O servidor constrói respostas HTTP para cada requisição processada. O subject exige que os códigos de status sejam precisos e que páginas de erro padrão sejam fornecidas. ✅ `Fonte: subject Webserv 42, IV.1`

## Como funciona
A resposta HTTP tem três partes:

```
<HTTP-Version> <Status-Code> <Reason-Phrase>\r\n
<headers>\r\n
\r\n
<body>
```

1. **Status-Line**: `HTTP/1.1 200 OK\r\n`
2. **Headers**: metadados da resposta (Content-Type, Content-Length, Connection, etc.).
3. **Body**: conteúdo do recurso solicitado ou página de erro.

> [!note]- 🔍 Aprofundando: construção da resposta
> O servidor precisa montar a resposta dinamicamente:
> - Para arquivos estáticos: ler o arquivo, determinar Content-Type pela extensão, enviar com 200 OK.
> - Para erros: montar corpo HTML simples com o código e mensagem.
> - Para redirects: enviar 301/302 com header Location.
> - Para CGI: ler saída do CGI e repassar como resposta (ou modificar headers).
>
> **Conexões:**
> - `Complemento:` [[http-status-codes]]
> - `Complemento:` [[http-headers]]
> - `Complemento:` [[error-handling]]

## O que o subject NÃO exige (mas é bom saber)
- Respostas com `Transfer-Encoding: chunked` não são obrigatórias, mas podem ser úteis para CGI sem Content-Length.
- Cabeçalhos de cache (Cache-Control, ETag) não são exigidos.
- Conexões persistentes (keep-alive) são opcionais. `Fonte: subject Webserv 42, IV.1 diz que virtual host está fora de escopo, keep-alive não é mencionado.` ⚠️ não verificado

## Perguntas de autoavaliação
- Qual a diferença entre responder com 200 e com 404?
- Como o servidor determina o Content-Type de um arquivo?
- O que acontece se o CGI não retornar um header Content-Length?

## Fontes consultadas
- RFC 2616, seção 6
- subject Webserv 42, páginas 7-12
