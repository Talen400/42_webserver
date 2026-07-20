---
tags: [webserv, configuracao, root, diretorio]
fonte-principal: subject-webserv
status: revisado
---

# Diretiva root

## TL;DR
Mapeia a URL para um diretório no sistema de arquivos. Se o URL é `/kapouet/pouic/toto` e o root é `/tmp/www`, o servidor busca `/tmp/www/pouic/toto/pouet`. ✅ `Fonte: subject Webserv 42, IV.3`

## Contexto no Webserv
O subject exige configurar o diretório onde o arquivo solicitado deve estar localizado. ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
```
root /var/www/html;
```

Quando chega uma requisição `GET /pasta/arquivo.html`, o servidor:
1. Toma o path da URL: `/pasta/arquivo.html`
2. Concatena com root: `/var/www/html/pasta/arquivo.html`
3. Tenta abrir o arquivo nesse caminho.

```
URL:          /kapouet/pouic/toto/pouet
Root:         /tmp/www
Caminho real: /tmp/www/pouic/toto/pouet
```

> [!note]- 🔍 Aprofundando: root no server vs location
> A diretiva `root` pode aparecer no nível `server` (valor padrão para todos os locations) ou dentro de um `location` (sobrescreve para aquela rota):
>
> ```
> server {
>     root /var/www;
>     location /images {
>         root /var/www/images;
>     }
> }
> ```
>
> **Segurança:** O servidor deve garantir que o caminho resolvido não saia do diretório raiz (path traversal: `../../etc/passwd`). O path deve ser normalizado e verificado.
>
> **Conexões:**
> - `Complemento:` [[routing]]
> - `Complemento:` [[static-file-serving]]
> - `Complemento:` [[location-blocks]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não menciona proteção contra path traversal. ⚠️ não verificado, mas é uma prática de segurança essencial.
- NGINX bloqueia `..` no path por padrão.
- `alias` (outra diretiva do NGINX) não é mencionada.

## Perguntas de autoavaliação
- Como o servidor deve tratar um path como `/../../../etc/passwd`?
- O que acontece se o root não existir no sistema de arquivos?
- A diretiva root pode ser definida dentro de um location?

## Fontes consultadas
- subject Webserv 42, páginas 11-12
