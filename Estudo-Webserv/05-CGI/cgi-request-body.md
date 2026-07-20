---
tags: [webserv, cgi, request, body, stdin]
fonte-principal: rfc-3875
status: revisado
---

# Envio do Corpo da Requisição para o CGI

## TL;DR
Para requisições POST, o servidor envia o corpo da requisição para o CGI via stdin. O servidor fecha o pipe quando termina, e o CGI interpreta EOF como final dos dados.

## Contexto no Webserv
O subject afirma que "para solicitações em partes, seu servidor precisa desagrupá-las" e que "o CGI esperará EOF como o final do corpo". ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
```
Servidor                          CGI
   │                               │
   │   fork() + execve()           │
   │══════════════════════════════>│
   │                               │
   │   write(pipe_stdin, body)     │
   │══════════════════════════════>│ (lê do stdin)
   │                               │
   │   close(pipe_stdin)           │
   │══════════════════════════════>│ (EOF - corpo terminou)
   │                               │
```

1. Servidor cria pipe(s) antes do fork.
2. No fork: filho fecha pontas não usadas, `dup2()` stdin para o pipe.
3. Servidor escreve o corpo da requisição no pipe.
4. Servidor fecha o pipe (EOF).

> [!note]- 🔍 Aprofundando: chunked vs multipart — não confundir
> **Chunked Transfer Encoding** (`Transfer-Encoding: chunked`) é uma forma de o cliente enviar o corpo em pedaços sem saber o tamanho total. O servidor **deve** desagrupar (de-chunk) esses dados antes de passar ao CGI, porque o CGI espera o corpo completo seguido de EOF. ✅ `Fonte: subject Webserv 42, IV.3`
>
> **Multipart/form-data** (`Content-Type: multipart/form-data; boundary=---`) é um formato de corpo com várias partes separadas por um boundary. O CGI recebe o multipart **já de-chunked** (se veio chunked pela rede) — o servidor primeiro resolve o chunked, depois entrega o corpo resultante (que pode ser multipart) para o CGI. O CGI é quem parseia o multipart, não o servidor (a menos que o upload seja para salvar arquivo no disco, não via CGI).
>
> **Conexões:**
> - `Complemento:` [[cgi-overview]]
> - `Complemento:` [[file-upload]]
> - `Complemento:` [[cgi-response]]
> - `Complemento:` [[chunked-transfer-encoding]]
> - `Complemento:` [[multipart-form-data]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica o tamanho do buffer para escrever no pipe. ⚠️ não verificado
- CGIs que esperam stdin podem travar se o pipe não for fechado.
- Para GET, não há corpo para enviar (CONTENT_LENGTH = 0 ou ausente).

## Perguntas de autoavaliação
- Como evitar deadlock se o CGI escrever muito na saída enquanto o servidor ainda está escrevendo na entrada?
- O que acontece se o corpo da requisição for maior que o buffer do pipe?
- Quando fechar o pipe de escrita para o CGI?

## Fontes consultadas
- subject Webserv 42, páginas 11-13
- RFC 3875 (CGI 1.1), seção 5.2
