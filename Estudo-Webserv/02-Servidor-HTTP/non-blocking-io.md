---
tags: [webserv, io, non-blocking, poll]
fonte-principal: subject-webserv
status: revisado
---

# Non-blocking I/O

## TL;DR
I/O não bloqueante significa que chamadas como read() e write() retornam imediatamente, mesmo que não haja dados disponíveis. O servidor usa poll() para saber quando pode ler ou escrever sem travar.

## Contexto no Webserv
O subject é categórico: o servidor deve ser **não bloqueante em todos os momentos** e usar **apenas 1 poll()** (ou equivalente) para todas as operações de I/O. ✅ `Fonte: subject Webserv 42, IV.1` Usar read/write sem poll() resulta em nota 0. ✅ `Fonte: subject Webserv 42, IV.1`

## Como funciona
Sem I/O não bloqueante, um `read()` em um socket sem dados travaria o servidor inteiro até chegar algo. Com `O_NONBLOCK`:

- `read()` retorna -1 com `errno = EAGAIN` / `EWOULDBLOCK` se não há dados.
- `write()` retorna -1 com `errno = EAGAIN` / `EWOULDBLOCK` se o buffer de envio está cheio.

O `poll()` resolve isso: ele só retorna quando o fd está "pronto" (leitura tem dados, escrita tem espaço).

> [!note]- 🔍 Aprofundando: por que um único poll()
> O subject exige **um único poll()** para todo o I/O. Isso força uma arquitetura de evento único:
> - Todos os listen sockets e client sockets estão no mesmo conjunto.
> - O loop chama `poll()` uma vez por iteração.
> - Quando poll() retorna, o servidor itera pelos fds prontos e processa cada um.
> - Isso elimina busy-waiting e uso de CPU desnecessário.
>
> **Proibido verificar errno:** O subject proíbe verificar `errno` após read/write para ajustar comportamento. ✅ `Fonte: subject Webserv 42, IV.1`
>
> **Conexões:**
> - `Complemento:` [[event-loop]]
> - `Complemento:` [[poll-vs-select-vs-kqueue]]
> - `Complemento:` [[socket-programming]]

## O que o subject NÃO exige (mas é bom saber)
- O subject permite `select()`, `kqueue()` ou `epoll()` como alternativas ao `poll()`. ✅ `Fonte: subject Webserv 42`
- A leitura do arquivo de configuração **pode** ser feita sem poll(). ✅ `Fonte: subject Webserv 42, IV.1`
- `fcntl()` é necessário para configurar `O_NONBLOCK` em sockets (no macOS é obrigatório). ✅ `Fonte: subject Webserv 42, IV.2`

## Perguntas de autoavaliação
- O que significa EAGAIN em um read() não bloqueante?
- Como o poll() sabe que um socket está pronto para leitura?
- Por que verificar errno após read/write é proibido?

## Fontes consultadas
- subject Webserv 42, páginas 7-12
- man poll(2), man fcntl(2)
