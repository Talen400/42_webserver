---
tags: [webserv, cgi, response, stdout]
fonte-principal: rfc-3875
status: revisado
---

# Leitura da Saída do CGI

## TL;DR
O CGI escreve a resposta (headers + corpo) no stdout. O servidor lê essa saída e a encaminha para o cliente, possivelmente ajustando ou completando headers.

## Contexto no Webserv
O subject afirma que "se nenhum content_length for retornado do CGI, EOF marcará o final dos dados retornados". ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
```
CGI                                Servidor
  │                                  │
  │   write(stdout, resposta)        │
  │═══════════════════════════════>  │ (lê do pipe)
  │                                  │
  │   exit()                         │
  │═══════════════════════════════>  │ (EOF + waitpid)
  │                                  │
  │                           encaminha ao cliente
```

O CGI escreve no stdout:
```
Status: 200 OK
Content-Type: text/html

<html><body>Hello!</body></html>
```

O servidor deve:
1. Ler headers da saída do CGI (linhas até `\r\n\r\n`).
2. Interpretar headers (principalmente `Content-Type` e `Status`).
3. Ler o corpo (pelo Content-Length ou até EOF).
4. Montar resposta HTTP para o cliente.

> [!note]- 🔍 Aprofundando: parsando saída do CGI
> A saída do CGI pode conter:
> - **Headers resposta CGI**: `Content-Type`, `Location`, `Status`. O servidor usa `Status` para definir o código HTTP.
> - **Corpo**: o restante após a linha vazia.
>
> Se o CGI retornar `Location:` sem `Status`, o servidor deve usar 302 (Found).
>
> O servidor precisa tomar cuidado:
> - Se o CGI não retornar `Content-Length`, o servidor lê até EOF e calcula ele mesmo.
> - O pipe de saída deve ser configurado como non-blocking para evitar travamentos.
>
> **Conexões:**
> - `Complemento:` [[cgi-overview]]
> - `Complemento:` [[cgi-execution]]
> - `Complemento:` [[http-response-format]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica se o servidor pode modificar headers do CGI.
- CGI pode retornar `Status:` header para definir código HTTP.
- Se o CGI falhar (exit não-zero), o servidor deve retornar 502 Bad Gateway ou 500.

## Perguntas de autoavaliação
- Como ler a saída do CGI sem travar (non-blocking)?
- O que fazer se o CGI retornar um header Location?
- Como detectar que o CGI terminou (EOF vs erro)?

## Fontes consultadas
- subject Webserv 42, páginas 11-13
- RFC 3875 (CGI 1.1), seção 6
