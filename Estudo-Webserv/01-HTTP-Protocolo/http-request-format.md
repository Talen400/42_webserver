---
tags: [webserv, http, protocolo, request]
fonte-principal: rfc-2616
status: revisado
---

# HTTP Request Format

## TL;DR
Toda requisição HTTP começa com uma linha de requisição (método, URI, versão), seguida por cabeçalhos opcionais e um corpo opcional. O servidor parseia essa estrutura para saber o que o cliente quer.

## Contexto no Webserv
O servidor precisa parsear a requisição HTTP completa para tomar decisões de roteamento, validar métodos, e processar o corpo (se houver). O subject exige suporte a GET, POST e DELETE. ✅ `Fonte: subject Webserv 42, IV.1`

## Como funciona
A requisição HTTP tem três partes:

```
<method> <URI> <HTTP-Version>\r\n
<headers>\r\n
\r\n
<body>
```

1. **Request-Line**: primeira linha, terminada em `\r\n`. Ex: `GET /index.html HTTP/1.1\r\n`
2. **Headers**: pares `nome: valor\r\n`, terminados por um `\r\n` vazio.
3. **Body**: presente em POST, delimitado por `Content-Length` ou `Transfer-Encoding: chunked`.

> [!note]- 🔍 Aprofundando: parsing linha a linha
> O parsing pode ser feito lendo o buffer byte a byte até encontrar `\r\n`. Como o servidor é non-blocking, a requisição pode chegar em partes — o parser deve ser stateful:
> 1. `REQUEST_LINE`: lê até `\r\n`, extrai method, URI, version.
> 2. `HEADERS`: lê linhas de header até `\r\n` vazio.
> 3. `BODY`: lê `Content-Length` bytes ou faz parse de chunked encoding.
>
> **Conexões:**
> - `Complemento:` [[request-parsing]]
> - `Complemento:` [[http-methods]]
> - `Complemento:` [[http-headers]]

## O que o subject NÃO exige (mas é bom saber)
- Parse de `Transfer-Encoding: chunked` não é explicitamente mencionado, mas é necessário para CGI com requisições multipart (`Fonte: subject Webserv 42, IV.3`).
- HTTP/2 não é exigido.
- Virtual hosts (Host header) estão fora do escopo, embora mencionados como opcionais. ✅ `Fonte: subject Webserv 42, IV.1`

## Perguntas de autoavaliação
- Como o parser sabe quando parou de ler headers e começou o body?
- O que acontece se a request-line chegar fragmentada em vários read()?
- Como diferenciar uma requisição completa de uma incompleta em um servidor non-blocking?

## Fontes consultadas
- RFC 2616, seções 4-5
- subject Webserv 42, páginas 7-12
