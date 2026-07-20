---
tags: [webserv, request, parsing, http]
fonte-principal: rfc-2616
status: revisado
---

# Request Parsing

## TL;DR
O servidor precisa transformar os bytes recebidos do cliente em uma estrutura de dados compreensível: método, URI, versão, headers e corpo.

## Contexto no Webserv
O servidor deve parsear requisições HTTP para rotear corretamente. Como o I/O é non-blocking, o parser deve ser **stateful**: a requisição pode chegar em várias chamadas de `read()`. ✅ `Fonte: subject Webserv 42, IV.1`

## Como funciona
O parser opera em estados:

```
PARSE_REQUEST_LINE → PARSE_HEADERS → PARSE_BODY → DONE
```

1. **Request Line**: lê até `\r\n`. Separa method, URI, version por espaço.
2. **Headers**: lê linhas até `\r\n` vazio. Armazena em mapa (case-insensitive).
3. **Body**: lê exatamente `Content-Length` bytes (ou até EOF se chunked).

> [!note]- 🔍 Aprofundando: edge cases do parsing
> - **Linhas longas**: se uma linha não couber no buffer, o servidor deve retornar 400.
> - **Headers duplicados**: o último valor sobrescreve ou combina (dependendo do header).
> - **URI mal formatada**: caracteres inválidos devem resultar em 400.
> - **Versão HTTP**: se não for `HTTP/1.1` ou `HTTP/1.0`, retornar 505.
> - **Buffer parcial**: o parser não pode supor que uma linha inteira chegou em um único read().
>
> ```c
> // Exemplo: máquina de estados
> enum ParseState {
>     REQUEST_LINE,
>     HEADERS,
>     BODY,
>     COMPLETE
> };
> ```
>
> **Conexões:**
> - `Complemento:` [[http-request-format]]
> - `Complemento:` [[routing]]
> - `Complemento:` [[non-blocking-io]]

## O que o subject NÃO exige (mas é bom saber)
- Parse de chunked encoding é necessário para CGI com multipart. ✅ `Fonte: subject Webserv 42, IV.3`
- Validação de URI (segundo RFC 3986) não é exigida em detalhes.
- O subject não menciona limite de tamanho de headers. ⚠️ não verificado

## Perguntas de autoavaliação
- Como o parser diferencia o fim dos headers do início do body?
- O que fazer se a request-line chegar em dois reads diferentes?
- Como tratar caracteres inválidos no header name?

## Fontes consultadas
- RFC 2616, seções 4-5
- subject Webserv 42, páginas 7-12
