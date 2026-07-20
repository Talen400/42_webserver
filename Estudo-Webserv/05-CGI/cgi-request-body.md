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

> [!note]- 🔍 Aprofundando: multipart
> Se a requisição for `multipart/form-data` (upload de arquivo via formulário), o servidor precisa:
> 1. Parsear o boundary do header Content-Type.
> 2. Separar cada parte do corpo.
> 3. Passar cada parte individualmente ou o corpo completo para o CGI.
>
> O subject não especifica se o CGI recebe o multipart bruto ou já processado. ⚠️ não verificado
>
> **Conexões:**
> - `Complemento:` [[cgi-overview]]
> - `Complemento:` [[file-upload]]
> - `Complemento:` [[cgi-response]]

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
