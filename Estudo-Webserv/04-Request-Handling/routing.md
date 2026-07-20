---
tags: [webserv, routing, request, location]
fonte-principal: subject-webserv
status: revisado
---

# Routing

## TL;DR
Roteamento é o processo de decidir qual regra de configuração aplicar a uma requisição. O servidor combina a URL com um location block, determina o caminho do arquivo e decide a ação.

## Contexto no Webserv
O subject define regras por URL/rota através de location blocks. Cada location especifica métodos, root, diretório de upload, CGI, etc. ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
Fluxo de decisão para cada requisição:

```
1. Parse da requisição → extrai método e URI
2. Match da URI contra location blocks → location mais específico
3. Verificar se o método é permitido no location
4. Determinar caminho real: root + URI
5. Decidir ação:
   ├── Se tem return → redirect
   ├── Se tem cgi_ext e arquivo corresponde → CGI
   ├── Se é diretório → index file ou autoindex
   ├── Se método POST e upload_store → upload
   ├── Se método DELETE → deletar
   └── Caso contrário → servir arquivo estático
```

> [!note]- 🔍 Aprofundando: algoritmo de match
> 1. Listar todos os locations.
> 2. Verificar quais são prefixo da URI solicitada.
> 3. Escolher o de maior comprimento (mais específico).
> 4. Se nenhum corresponder, usar o location `/` (fallback).
>
> Exemplo:
> - URI: `/cgi-bin/script.php`
> - Locations: `/` (length 1), `/cgi-bin` (length 8), `/images` (length 7)
> - Match: `/cgi-bin` (maior comprimento)
>
> **Conexões:**
> - `Complemento:` [[location-blocks]]
> - `Complemento:` [[static-file-serving]]
> - `Complemento:` [[cgi-overview]]
> - `Complemento:` [[redirecionamento]]

## O que o subject NÃO exige (mas é bom saber)
- Roteamento baseado em query string não é mencionado.
- O subject não especifica o comportamento se dois locations têm o mesmo prefixo. ⚠️ não verificado
- NGINX usa algoritmo de match mais complexo (exact match `=`, regex `~`, prefix `^~`).

## Perguntas de autoavaliação
- Qual location é usado quando a URL é `/`?
- O que acontece se o método da requisição não estiver na lista `methods` do location?
- Como decidir entre servir um arquivo estático vs executar CGI?

## Fontes consultadas
- subject Webserv 42, páginas 11-13
