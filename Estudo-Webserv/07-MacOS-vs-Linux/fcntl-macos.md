---
tags: [webserv, macos, fcntl, non-blocking]
fonte-principal: subject-webserv
status: revisado
---

# fcntl e Diferenças do macOS

## TL;DR
O macOS trata `write()` de forma diferente de outros Unix. Para compensar, o subject permite (e exige no macOS) o uso de `fcntl()` com flags específicas para configurar descritores como não bloqueantes.

## Contexto no Webserv
O subject dedica uma seção específica ao macOS: "Como o macOS lida com write() de forma diferente de outros sistemas operacionais baseados em Unix, você está autorizado a usar fcntl()." ✅ `Fonte: subject Webserv 42, IV.2`

## Como funciona
No macOS, descritores de arquivo (incluindo sockets) podem ter comportamento bloqueante mesmo com a configuração padrão. Para garantir comportamento consistente entre plataformas:

```c
fcntl(fd, F_SETFL, O_NONBLOCK);      // modo não bloqueante
fcntl(fd, F_SETFD, FD_CLOEXEC);       // fechar no exec()
```

**Flags permitidas:**
- `F_SETFL` — configurar flags de status
- `O_NONBLOCK` — modo não bloqueante
- `FD_CLOEXEC` — fechar descritor ao executar `execve()`

> [!note]- 🔍 Aprofundando: por que isso importa
> No Linux, sockets criados com `socket()` já são internally configurados para aceitar non-blocking de forma confiável. No macOS, o comportamento do `write()` pode ser diferente:
> - `write()` em macOS pode bloquear mesmo com `O_NONBLOCK` em certas condições.
> - Para garantir que o servidor funcione de forma idêntica em ambas as plataformas, o subject exige o uso de `fcntl()` com `F_SETFL` e `O_NONBLOCK`.
>
> **FD_CLOEXEC** é importante para CGI: sem ele, os descritores de arquivo do servidor vazam para o processo filho, podendo causar:
> - Uso desnecessário de FDs
> - Comportamento inesperado no CGI
> - Impedir que portas sejam liberadas
>
> **Conexões:**
> - `Complemento:` [[non-blocking-io]]
> - `Complemento:` [[cgi-execution]]

## O que o subject NÃO exige (mas é bom saber)
- O subject proíbe explicitamente outras flags de fcntl. ✅ `Fonte: subject Webserv 42, IV.2`
- No Linux, é possível usar `SOCK_CLOEXEC` e `SOCK_NONBLOCK` diretamente no `socket()`, mas essas flags não são portáveis para macOS. ✅ `Fonte: man socket(2)`
- O subject não exige que o servidor funcione no macOS, mas as regras para macOS existem para quem testa nessa plataforma.

## Perguntas de autoavaliação
- Por que FD_CLOEXEC é importante para servidores que usam CGI?
- Qual a diferença de comportamento do write() entre Linux e macOS?
- Quais flags de fcntl são permitidas pelo subject?

## Fontes consultadas
- subject Webserv 42, páginas 10-11
- man fcntl(2), man write(2)
