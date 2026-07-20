---
tags: [webserv, event-loop, abstracao, poll, epoll, kqueue]
fonte-principal: subject-webserv
status: revisado
---

# Abstração do Event Loop

## TL;DR
O subject aceita `poll()`, `select()`, `kqueue()` ou `epoll()`. Uma camada de abstração encapsula as diferenças de API, permitindo trocar a implementação sem alterar o resto do servidor.

## Contexto no Webserv
O subject diz: "Mesmo que poll() seja mencionado no subject e na régua de avaliação, você pode usar qualquer função equivalente, como select(), kqueue() ou epoll()." ✅ `Fonte: subject Webserv 42, páginas 7-8`

## Como funciona
Cada API tem sua própria estrutura de dados e chamadas. Uma abstração unifica:

```
┌──────────────────────────────┐
│       EventLoop class        │ ← Interface comum
├──────────────────────────────┤
│  add_fd(fd, events)          │
│  remove_fd(fd)               │
│  wait(timeout) → events[]    │
└──────────────────────────────┘
         ↙        ↓        ↘
    select()   poll()   epoll/kqueue
```

> [!note]- 🔍 Aprofundando: diferenças de API
> **poll():**
> - Usa `struct pollfd { fd, events, revents }`.
> - Requer passar o array inteiro a cada chamada.
> - `poll(fds, nfds, timeout)` — retorna número de FDs prontos.
>
> **select():**
> - Usa `fd_set` (bitmask com limite de FD_SETSIZE).
> - Não é ideal para muitos FDs.
> - Modifica os sets a cada chamada (precisa readicionar).
>
> **epoll (Linux):**
> - `epoll_create()` → `epoll_ctl(EPOLL_CTL_ADD)` → `epoll_wait()`.
> - Adiciona/remove FDs separadamente, não precisa repassar a lista.
> - Retorna apenas os FDs com eventos.
>
> **kqueue (macOS/BSD):**
> - `kqueue()` → `kevent()`.
> - Similar ao epoll, mas com struct `kevent`.
> - Suporta mais tipos de eventos (arquivo, sinal, processo).
>
> **Estratégia de abstração:**
> ```cpp
> class EventLoop {
> public:
>     virtual ~EventLoop() {}
>     virtual void add_fd(int fd, int events) = 0;  // events: READ | WRITE
>     virtual void remove_fd(int fd) = 0;
>     virtual int wait(Event* events, int max_events, int timeout_ms) = 0;
> };
> ```
>
> **Conexões:**
> - `Complemento:` [[poll-vs-select-vs-kqueue]]
> - `Complemento:` [[event-loop]]
> - `Complemento:` [[non-blocking-io]]

## O que o subject NÃO exige (mas é bom saber)
- A abstração não é exigida pelo subject, mas facilita portabilidade e testes.
- O subject diz para usar `fcntl()` com `O_NONBLOCK` no macOS; isso é independente da escolha de event loop.
- `epoll` no Linux e `kqueue` no macOS são as opções mais eficientes, mas `poll()` é a mais portável.

## Perguntas de autoavaliação
- Que métodos uma classe EventLoop abstrata precisaria ter?
- Como adaptar o loop existente que usa poll() para usar epoll()?
- Quais cuidados tomar para garantir que a abstração não adicione overhead desnecessário?

## Fontes consultadas
- subject Webserv 42, páginas 7-8
- man poll(2), man epoll(7), man kqueue(2), man select(2)
