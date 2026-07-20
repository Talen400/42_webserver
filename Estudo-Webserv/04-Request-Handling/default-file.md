---
tags: [webserv, index, default, diretorio]
fonte-principal: subject-webserv
status: revisado
---

# Default File (Index)

## TL;DR
Quando a URL solicitada aponta para um diretório, o servidor procura por um arquivo padrão (ex: index.html) dentro dele. Se existir, serve esse arquivo.

## Contexto no Webserv
O subject exige configurar o "arquivo padrão a ser servido quando o recurso solicitado é um diretório". ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
```
location / {
    index index.html index.htm;
}
```

Quando chega `GET /` ou `GET /pasta/`:
1. Servidor verifica se o path é um diretório.
2. Para cada nome em `index`:
   - Concatena com o path: `/var/www/html/index.html`
   - Verifica se o arquivo existe.
3. Se encontrou: serve o arquivo.
4. Se não encontrou: depende de `autoindex`.

> [!note]- 🔍 Aprofundando: ordem de procura
> Se `index index.html index.htm;`:
> 1. Tenta `/var/www/html/index.html`
> 2. Se não existe, tenta `/var/www/html/index.htm`
> 3. Se nenhum existe, cai no comportamento de autoindex ou erro
>
> **Importante:** O index só faz sentido para paths que terminam em `/` ou que são diretórios. Requisições para `/arquivo.html` ignoram a diretiva index.
>
> **Conexões:**
> - `Complemento:` [[directory-listing]]
> - `Complemento:` [[routing]]
> - `Complemento:` [[static-file-serving]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica se múltiplos valores de index são suportados. ⚠️ não verificado
- NGINX permite múltiplos valores e procura em ordem.
- `index.html` é o padrão universal.

## Perguntas de autoavaliação
- Qual a diferença entre acessar `/pasta` e `/pasta/`?
- O que acontece se o index file existir mas não tiver permissão de leitura?
- Como o servidor lida com `GET /` (raiz)?

## Fontes consultadas
- subject Webserv 42, páginas 11-12
