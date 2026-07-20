---
tags: [webserv, socket, network, tcp]
fonte-principal: man-pages
status: revisado
---

# Socket Programming

## TL;DR
Socket é a interface do sistema operacional para comunicação de rede. O servidor cria um socket, bind() em uma porta, listen() para aguardar conexões e accept() para cada cliente.

## Contexto no Webserv
O servidor precisa criar sockets TCP para cada interface:porta configurada. O subject permite usar `socket()`, `bind()`, `listen()`, `accept()` — todas funções C clássicas de socket. ✅ `Fonte: subject Webserv 42`

## Como funciona

```
socket(AF_INET, SOCK_STREAM, 0)
    ↓
bind(fd, &addr, sizeof(addr))
    ↓
listen(fd, SOMAXCONN)
    ↓
accept(fd, &client_addr, &client_len)
```

1. **socket()**: cria um endpoint de comunicação. `AF_INET` = IPv4, `SOCK_STREAM` = TCP.
2. **bind()**: associa o socket a um endereço (IP + porta). Permite reuso com `SO_REUSEADDR`.
3. **listen()**: marca o socket como passivo (aceitar conexões).
4. **accept()**: aceita uma conexão de cliente, retorna um novo fd.

> [!note]- 🔍 Aprofundando: configurando o socket
> **Non-blocking:**
> Após `accept()`, configurar o socket do cliente com `fcntl(fd, F_SETFL, O_NONBLOCK)`.
>
> **SO_REUSEADDR:**
> `setsockopt(fd, SOL_SOCKET, SO_REUSEADDR, &opt, sizeof(opt))` permite reusar a porta imediatamente após o servidor fechar, evitando "Address already in use".
>
> **getsockname():**
> Útil para descobrir em qual porta um socket está escutando (se a porta foi configurada como 0 para atribuição automática).
>
> **Conexões:**
> - `Complemento:` [[arquitetura-servidor-http]]
> - `Complemento:` [[event-loop]]
> - `Complemento:` [[multiple-ports]]

## O que o subject NÃO exige (mas é bom saber)
- IPv6 (`AF_INET6`) não é mencionado no subject. ⚠️ não verificado
- O subject não especifica o valor do backlog do listen. `SOMAXCONN` é uma prática comum.
- `SO_REUSEADDR` não é mencionado no subject, mas é essencial para desenvolvimento.

## Perguntas de autoavaliação
- Por que o fd retornado por accept() é diferente do fd do socket de listen?
- O que acontece se bind() falhar porque a porta já está em uso?
- O que fazer com o fd do cliente após fechar a conexão?

## Fontes consultadas
- man socket(2), man bind(2), man listen(2), man accept(2)
- subject Webserv 42, páginas 7-8
