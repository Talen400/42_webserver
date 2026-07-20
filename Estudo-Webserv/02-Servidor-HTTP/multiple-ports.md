---
tags: [webserv, servidor, portas, configuracao]
fonte-principal: subject-webserv
status: revisado
---

# Múltiplas Portas

## TL;DR
O servidor pode escutar em várias portas simultaneamente, cada uma servindo conteúdo diferente dependendo da configuração. Isso permite que um único processo atenda múltiplos sites.

## Contexto no Webserv
O subject exige que o servidor "seja capaz de ouvir várias portas para fornecer conteúdo diferente". ✅ `Fonte: subject Webserv 42, IV.1` Isso é configurado no arquivo de configuração com a diretiva `listen`. ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
Cada par `interface:porta` no config gera um socket.listen separado:

```
# Config exemplo
server {
    listen 127.0.0.1:8080;
    root /var/www/site1;
}
server {
    listen 127.0.0.1:8081;
    root /var/www/site2;
}
```

- Cada server block cria um `socket() + bind() + listen()`.
- Todos os listen sockets são adicionados ao mesmo `poll()`.
- Quando `poll()` sinaliza POLLIN em um listen socket, o servidor chama `accept()` e determina qual configuração usar para a nova conexão.

> [!note]- 🔍 Aprofundando: relacionando conexão ao server block
> Como o subject considera virtual hosts fora de escopo ✅ `Fonte: subject Webserv 42, IV.1`, a associação é simples: cada listen socket tem sua própria configuração. Quando um cliente é aceito, ele herda a configuração do listen socket correspondente.
>
> **Conexões:**
> - `Complemento:` [[listen-directive]]
> - `Complemento:` [[event-loop]]
> - `Complemento:` [[config-format]]

## O que o subject NÃO exige (mas é bom saber)
- Virtual hosts (mesma porta, nomes diferentes) estão fora de escopo, mas podem ser implementados como bônus. ✅ `Fonte: subject Webserv 42, IV.1`
- NGINX permite múltiplos `server` blocks no mesmo arquivo de configuração para servir diferentes sites.
- O subject não especifica um número máximo de portas.

## Perguntas de autoavaliação
- Como o servidor sabe qual configuração aplicar a um cliente recém-conectado?
- Quantos listen sockets podem existir simultaneamente?
- O que acontece se duas configurações usarem a mesma porta?

## Fontes consultadas
- subject Webserv 42, páginas 10-11
