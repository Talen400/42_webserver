---
tags: [webserv, io, poll, select, kqueue, epoll, comparacao]
fonte-principal: subject-webserv
status: revisado
---

# poll vs select vs kqueue vs epoll

## TL;DR
São funções que monitoram múltiplos file descriptors e avisam quando um está pronto para leitura ou escrita. O subject permite qualquer uma delas. ✅ `Fonte: subject Webserv 42, IV.1`

## Contexto no Webserv
O subject menciona `poll()` como padrão mas permite `select()`, `kqueue()` ou `epoll()`. O comportamento exigido é o mesmo: um único loop, monitorando leitura e escrita simultaneamente. ✅ `Fonte: subject Webserv 42, IV.1`

## Como funciona

| Função | Plataforma | Limite de FDs | Performance | Complexidade |
|--------|-----------|---------------|-------------|--------------|
| `select()` | Todas | FD_SETSIZE (1024) | O(n) — varre tudo | Baixa |
| `poll()` | Todas | Ilimitado (teórico) | O(n) — varre tudo | Média |
| `epoll()` | Linux | Ilimitado | O(1) — só os ativos | Alta |
| `kqueue()` | macOS/BSD | Ilimitado | O(1) — só os ativos | Alta |

> [!note]- 🔍 Aprofundando: como cada uma funciona
> **select():**
> - Usa `fd_set` (bitmask), limitado a 1024 FDs.
> - A cada chamada, precisa readicionar os FDs.
> - Ineficiente para muitos FDs.
>
> **poll():**
> - Usa array de `struct pollfd`.
> - Não tem limite fixo de FDs.
> - A cada chamada, passa o array inteiro.
> - O(n) — kernel varre todos os FDs a cada chamada.
>
> **epoll (Linux):**
> - Cria um contexto com `epoll_create`.
> - Adiciona/remove FDs com `epoll_ctl` (uma vez só).
> - `epoll_wait` retorna **apenas** os FDs com eventos.
> - O(1) — não varre, só notifica.
>
> **kqueue (macOS/BSD):**
> - Similar ao epoll, específico do BSD/macOS.
> - Usa `kqueue()` e `kevent()`.
> - Suporta notificações de arquivo, processo, sinal, etc.
>
> **Conexões:**
> - `Complemento:` [[non-blocking-io]]
> - `Complemento:` [[event-loop]]

## O que o subject NÃO exige (mas é bom saber)
- O subject só menciona `poll()`, `select()`, `kqueue()` e `epoll()`. Outras APIs (como `io_uring` no Linux) não são mencionadas. ⚠️ não verificado se seriam aceitas.
- `select()` pode ser problemático no Linux se o servidor tiver muitos clientes.
- `kqueue()` é a escolha natural no macOS, já que `epoll()` não existe.

## Perguntas de autoavaliação
- Qual a principal vantagem do epoll sobre o poll()?
- Por que select() tem limite de 1024 FDs?
- O subject permite usar mais de um poll()?

## Fontes consultadas
- subject Webserv 42, página 7
- man poll(2), man select(2), man epoll(7), man kqueue(2)
