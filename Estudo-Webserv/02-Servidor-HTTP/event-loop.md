---
tags: [webserv, event-loop, poll, arquitetura]
fonte-principal: subject-webserv
status: revisado
---

# Event Loop

## TL;DR
O coração do servidor: um loop infinito que chama poll() repetidamente, processa eventos de leitura e escrita, e gerencia milhares de conexões com uma única thread.

## Contexto no Webserv
O subject exige **um único poll()** monitorando leitura e escrita simultaneamente. ✅ `Fonte: subject Webserv 42, IV.1` O servidor nunca deve ler ou escrever sem passar pelo poll(). ✅ `Fonte: subject Webserv 42, IV.1`

## Como funciona
Estrutura do loop principal:

```c
while (true) {
    int nfds = poll(fds, nfds, timeout);

    for (int i = 0; i < nfds; i++) {
        if (fds[i].revents & POLLIN) {
            if (é listen socket)
                accept_client(fds[i].fd);
            else
                handle_read(fds[i].fd);
        }
        if (fds[i].revents & POLLOUT) {
            handle_write(fds[i].fd);
        }
        if (fds[i].revents & (POLLERR | POLLHUP | POLLNVAL)) {
            handle_error(fds[i].fd);
        }
    }
}
```

> [!note]- 🔍 Aprofundando: máquina de estados por conexão
> Cada conexão de cliente passa por estágios:
> 1. **READING_HEADERS**: lê dados até encontrar `\r\n\r\n`
> 2. **READING_BODY**: lê Content-Length bytes
> 3. **PROCESSING**: processa a requisição (abre arquivo, executa CGI)
> 4. **WRITING**: envia a resposta (pode levar múltiplos writes)
> 5. **DONE**: fecha conexão (ou mantém se keep-alive)
>
> O timeout é verificado em cada iteração: se uma conexão ficou muito tempo no mesmo estado, é fechada.
>
> **Listen sockets** também estão no poll(): quando POLLIN chega, significa que há clientes para aceitar.
>
> **Conexões:**
> - `Complemento:` [[non-blocking-io]]
> - `Complemento:` [[arquitetura-servidor-http]]
> - `Complemento:` [[multiple-ports]]
> - `Complemento:` [[timeouts]]

## O que o subject NÃO exige (mas é bom saber)
- O timeout do poll() pode ser -1 (infinito) ou um valor em milissegundos. Para o Webserv, um timeout é importante para detectar clientes lentos.
- NGINX usa uma arquitetura de event-loop similar, mas com múltiplos workers.
- O loop deve tratar `EINTR` (se um sinal chegar durante poll()).

## Perguntas de autoavaliação
- Quais eventos poll() pode retornar e o que cada um significa?
- Quando um fd é marcado como POLLOUT?
- O que fazer se poll() retornar 0 (timeout)?

## Fontes consultadas
- subject Webserv 42, páginas 7-12
- man poll(2)
