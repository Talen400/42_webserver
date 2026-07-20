---
tags: [webserv, chunked, transfer-encoding, http]
fonte-principal: rfc-2616
status: revisado
---

# Chunked Transfer Encoding

## TL;DR
Chunked encoding permite enviar o corpo da resposta (ou requisição) em partes (chunks), cada uma com seu próprio tamanho, sem precisar saber o tamanho total antecipadamente. Usado quando o servidor gera conteúdo dinamicamente.

## Contexto no Webserv
O subject menciona que "para solicitações em partes, seu servidor precisa desagrupá-las". ✅ `Fonte: subject Webserv 42, IV.3` Navegadores modernos usam chunked quando não sabem o `Content-Length` de antemão. O servidor pode receber chunked em POST ou produzir chunked em respostas CGI.

## Como funciona
Formato chunked:

```
[ tamanho em hexa ]\r\n
[ dados ]\r\n
[ tamanho em hexa ]\r\n
[ dados ]\r\n
...
[ 0 ]\r\n                    ← chunk de tamanho zero = final
\r\n                         ← trailer (pode ter headers)
```

Exemplo:
```
1E\r\n
<html><body>Hello Worl
6\r\n
d!</body>
0\r\n
\r\n
```

O corpo completo é: `"<html><body>Hello World!</body>"`.

> [!note]- 🔍 Aprofundando: integração com a máquina de estados
> O parsing de chunked adiciona estados extras à máquina:
>
> ```
> HEADERS → CHUNK_SIZE → CHUNK_DATA → CHUNK_END → BODY_COMPLETE
> ```
>
> **CHUNK_SIZE:** Lê linha com o tamanho em hexadecimal. Se for `0`, vai para trailer.
> **CHUNK_DATA:** Lê exatamente N bytes (o tamanho do chunk). Depois lê `\r\n`.
> **CHUNK_END / TRAILER:** Lê `\r\n` final (ou headers finais opcionais).
>
> **Problema no parsing:** O tamanho e os dados podem cair em `read()` diferentes. O buffer de acumulação é essencial.
>
> **Conexões:**
> - `Complemento:` [[state-machine-parsing]]
> - `Complemento:` [[http-headers]]
> - `Complemento:` [[cgi-response]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não exige explicitamente suporte a chunked na resposta, mas pode ser necessário para CGI sem Content-Length. ⚠️ não verificado
- O trailer de chunked (headers após o chunk final) é raramente usado e não é exigido.
- HTTP/1.1 usa chunked quando `Content-Length` está ausente. Se o servidor não suportar, deve retornar erro.

## Perguntas de autoavaliação
- Como o servidor sabe se o corpo é chunked ou tem Content-Length?
- O que fazer se um chunk tiver tamanho 0 mas não for o último?
- Como lidar com um chunk cujo tamanho declarado não corresponde aos dados reais recebidos?

## Fontes consultadas
- RFC 2616, seção 3.6.1
- subject Webserv 42, página 12
