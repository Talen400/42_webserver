---
tags: [webserv, arquitetura, servidor]
fonte-principal: subject-webserv
status: revisado
---

# Arquitetura do Servidor HTTP

## TL;DR
Um servidor HTTP escuta por conexões TCP, aceita clientes, lê requisições HTTP, processa (servindo arquivo, executando CGI, etc.) e envia respostas. Tudo isso sem travar e usando um único loop de eventos.

## Contexto no Webserv
O servidor Webserv deve ser **non-blocking**, usar **um único poll()** para monitorar todas as operações de I/O, e **nunca** ler ou escrever sem passar pelo poll(). ✅ `Fonte: subject Webserv 42, IV.1`

## Como funciona
O fluxo principal:

```
1. socket() → bind() → listen()  (para cada porta/config)
2. poll() loop:
   ├── poll() aguarda eventos
   ├── se listen socket pronto → accept() → adiciona ao poll()
   ├── se client socket pronto para leitura → read() → parse request
   ├── se request completa → processa → prepara response
   ├── se client socket pronto para escrita → write() → envia response
   └── fecha conexão quando terminar
```

> [!note]- 🔍 Aprofundando:单片 arquitetura event-loop
> O servidor não cria threads nem processos (exceto para CGI). Tudo acontece no mesmo processo:
> - O `poll()` retorna quais file descriptors estão prontos.
> - O servidor mantém estado por conexão (estágio: lendo headers, lendo body, escrevendo resposta).
> - Conexões que demoram demais devem ser fechadas (timeout).
>
> **Conexões:**
> - `Complemento:` [[event-loop]]
> - `Complemento:` [[non-blocking-io]]
> - `Complemento:` [[socket-programming]]

## O que o subject NÃO exige (mas é bom saber)
- Arquitetura multi-thread ou multi-process não é permitida (exceto fork para CGI). ✅ `Fonte: subject Webserv 42, IV.1`
- Servidores como NGINX usam arquitetura event-loop semelhante, mas com múltiplos workers.
- O loop pode ficar ocupado se um `write()` retornar EAGAIN — é preciso bufferizar a resposta e tentar de novo.

## Perguntas de autoavaliação
- O que acontece se o poll() retornar e o socket de listen estiver pronto?
- Como o servidor sabe que uma requisição está completa?
- Quantos poll() o servidor deve ter?

## Fontes consultadas
- subject Webserv 42, páginas 7-12
- RFC 2616
