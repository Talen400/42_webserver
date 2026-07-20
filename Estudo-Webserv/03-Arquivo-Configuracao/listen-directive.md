---
tags: [webserv, configuracao, listen, porta]
fonte-principal: subject-webserv
status: revisado
---

# Diretiva listen

## TL;DR
Define em qual interface e porta o servidor vai escutar por conexões. É o ponto de entrada para cada server block.

## Contexto no Webserv
O subject exige definir "todos os pares interface:porta nos quais seu servidor irá ouvir (definindo vários sites servidos pelo seu programa)". ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
Formato da diretiva:

```
listen <interface>:<porta>;
```

Exemplos:
```
listen 127.0.0.1:8080;    # escuta em localhost porta 8080
listen 0.0.0.0:80;        # escuta em todas as interfaces porta 80
listen 192.168.1.10:3000; # escuta em IP específico
```

> [!note]- 🔍 Aprofundando: resolução de endereço
> O servidor deve usar `getaddrinfo()` para resolver o endereço:
> ```c
> struct addrinfo hints, *res;
> memset(&hints, 0, sizeof(hints));
> hints.ai_family = AF_INET;
> hints.ai_socktype = SOCK_STREAM;
> hints.ai_flags = AI_PASSIVE;
> getaddrinfo(ip_str, port_str, &hints, &res);
> socket(res->ai_family, res->ai_socktype, res->ai_protocol);
> bind(fd, res->ai_addr, res->ai_addrlen);
> ```
>
> `getaddrinfo` e `freeaddrinfo` estão na lista de funções autorizadas. ✅ `Fonte: subject Webserv 42`
>
> **Conexões:**
> - `Complemento:` [[socket-programming]]
> - `Complemento:` [[multiple-ports]]
> - `Complemento:` [[config-format]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica o formato se apenas a porta for fornecida (ex: `listen 8080`). ⚠️ não verificado
- NGINX permite `listen *:8080` e `listen 8080` (assume 0.0.0.0).
- Não há menção a IPv6 no subject.

## Perguntas de autoavaliação
- Como o parser separa interface da porta em "127.0.0.1:8080"?
- O que fazer se getaddrinfo() falhar?
- Pode haver dois server blocks com o mesmo listen?

## Fontes consultadas
- subject Webserv 42, páginas 11-12
- man getaddrinfo(3)
