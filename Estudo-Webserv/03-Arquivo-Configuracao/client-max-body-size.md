---
tags: [webserv, configuracao, body, limite, segurança]
fonte-principal: subject-webserv
status: revisado
---

# client_max_body_size

## TL;DR
Define o tamanho máximo permitido para o corpo das requisições dos clientes. Requisições com corpo maior que o limite recebem 413 Payload Too Large.

## Contexto no Webserv
O subject exige "definir o tamanho máximo permitido para os corpos de solicitação do cliente". ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
```
client_max_body_size 10M;   # 10 megabytes
```

O servidor deve:
1. Ler o header `Content-Length` da requisição.
2. Comparar com o valor configurado.
3. Se `Content-Length > client_max_body_size`, rejeitar com **413 Payload Too Large** antes de ler o corpo.
4. Se o corpo for chunked, pode ser necessário limitar durante a leitura.

> [!note]- 🔍 Aprofundando: formatos de valor
> O parser precisa suportar sufixos de tamanho:
> - `10M` → 10 * 1024 * 1024 bytes
> - `500K` → 500 * 1024 bytes
> - `1G` → 1 * 1024 * 1024 * 1024 bytes
> - Apenas número (ex: `1024`) → bytes
>
> Este comportamento é uma convenção inspirada no NGINX. O subject não especifica o formato do valor. ⚠️ não verificado
>
> **Conexões:**
> - `Complemento:` [[request-parsing]]
> - `Complemento:` [[http-headers]]
> - `Complemento:` [[config-format]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica o formato do valor (K, M, G). ⚠️ não verificado
- NGINX usa `client_max_body_size 1m` (case-insensitive).
- Se não configurado, o valor padrão no NGINX é 1MB. Para o Webserv, pode ser um valor definido internamente.
- Requisições chunked precisam de tratamento especial: limitar acumulativamente.

## Perguntas de autoavaliação
- Como impedir que o servidor leia um corpo maior que o limite?
- O que fazer se o client enviar chunked sem Content-Length?
- O bloqueio deve acontecer antes ou depois de ler os headers?

## Fontes consultadas
- subject Webserv 42, páginas 11-12
