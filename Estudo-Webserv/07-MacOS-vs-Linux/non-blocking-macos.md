---
tags: [webserv, macos, non-blocking, fcntl, fd_cloexec]
fonte-principal: subject-webserv
status: revisado
---

# Non-blocking no macOS (O_NONBLOCK e FD_CLOEXEC)

## TL;DR
No macOS, sockets não são configurados como não bloqueantes por padrão. Use `fcntl()` com `F_SETFL`/`O_NONBLOCK` para comportamento non-blocking, e `F_SETFD`/`FD_CLOEXEC` para evitar vazamento de FDs para CGI.

## Contexto no Webserv
O subject diz: "Você deve usar descritores de arquivo no modo não bloqueante para obter um comportamento semelhante ao de outros sistemas operacionais Unix." ✅ `Fonte: subject Webserv 42, IV.2`

## Como funciona
Após criar o socket com `socket()` ou `accept()`, configure:

```c
// Após accept()
fcntl(client_fd, F_SETFL, O_NONBLOCK);
fcntl(client_fd, F_SETFD, FD_CLOEXEC);
```

**O_NONBLOCK:** `read()` e `write()` retornam -1 com `errno = EAGAIN` em vez de bloquear.

**FD_CLOEXEC:** Garante que o fd será fechado automaticamente quando `execve()` for chamado (CGI). Sem isso, FDs do servidor vazam para scripts CGI.

> [!note]- 🔍 Aprofundando: diferenças de comportamento entre Linux e macOS
> **Linux:**
> - `socket()` com `SOCK_NONBLOCK | SOCK_CLOEXEC` já cria fd non-blocking + close-on-exec. (⚠️ flags não portáveis)
> - `write()` em socket non-blocking sempre respeita EAGAIN.
>
> **macOS:**
> - `socket()` não tem suporte a `SOCK_NONBLOCK`.
> - **Problema conhecido:** `write()` em macOS pode bloquear mesmo com `O_NONBLOCK` em certas condições (buffer de envio cheio + certas flags). O subject reconhece esta diferença e permite `fcntl()` para mitigar. ✅ `Fonte: subject Webserv 42, IV.2`
> - **FD_CLOEXEC é essencial:** Sem ele, após `fork()` + `execve()`, o CGI herda todos os FDs do servidor, incluindo sockets de cliente e pipes de outros CGIs.
>
> **O subject é explícito sobre as flags permitidas:**
> - Apenas: `F_SETFL`, `O_NONBLOCK`, `FD_CLOEXEC`.
> - "Qualquer outra flag é proibida." ✅ `Fonte: subject Webserv 42, IV.2`
>
> **Conexões:**
> - `Complemento:` [[fcntl-macos]]
> - `Complemento:` [[non-blocking-io]]
> - `Complemento:` [[cgi-execution]]
> - `Complemento:` [[socket-programming]]

## O que o subject NÃO exige (mas é bom saber)
- `F_GETFL` não é mencionado, mas pode ser necessário para ler flags atuais antes de modificar (ex: `fcntl(fd, F_SETFL, fcntl(fd, F_GETFL) | O_NONBLOCK)`). ⚠️ não verificado se a combinação é aceita.
- No Linux, `accept4()` pode retornar fd com flags configuradas (non-blocking + close-on-exec), mas a função não está na lista de autorizadas.
- `fcntl()` com `F_SETFD` não afeta o comportamento de I/O — apenas o close-on-exec.

## Perguntas de autoavaliação
- Por que FD_CLOEXEC é importante em um servidor que usa CGI?
- O que acontece se O_NONBLOCK não for configurado após accept()?
- Quais flags de fcntl são explicitamente permitidas? Quais são proibidas?

## Fontes consultadas
- subject Webserv 42, páginas 10-11
- man fcntl(2), man write(2)
