---
tags: [webserv, redirect, redirecionamento, http]
fonte-principal: subject-webserv
status: revisado
---

# Redirecionamento HTTP

## TL;DR
Redirecionamento instrui o cliente a buscar o recurso em outra URL. O servidor envia um código 301 ou 302 com o header Location apontando para o novo endereço.

## Contexto no Webserv
O subject exige a opção de configurar redirecionamento HTTP em location blocks. ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
Configuração:
```
location /old-page {
    return 301 /new-page;
}
location /external {
    return 302 http://outro-site.com;
}
```

Quando o servidor encontra uma diretiva `return` no location:
1. Prepara resposta com código de status (301, 302, etc.)
2. Adiciona header `Location: <url-destino>`
3. Envia resposta sem corpo (ou com corpo mínimo)

> [!note]- 🔍 Aprofundando: tipos de redirect
> | Código | Significado | Uso |
> |--------|-------------|-----|
> | 301 | Moved Permanently | URL mudou permanentemente. Browser atualiza cache. |
> | 302 | Found (temporário) | URL mudou temporariamente. Browser usa URL original no futuro. |
> | 307 | Temporary Redirect | Similar a 302, mas mantém método HTTP. |
> | 308 | Permanent Redirect | Similar a 301, mas mantém método HTTP. |
>
> O subject não especifica quais códigos de redirect implementar. 301 e 302 são os mais comuns.
>
> **Resposta de redirect:**
> ```
> HTTP/1.1 301 Moved Permanently
> Location: /new-page
> Content-Length: 0
>
> ```
>
> **Conexões:**
> - `Complemento:` [[location-blocks]]
> - `Complemento:` [[http-status-codes]]
> - `Complemento:` [[routing]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica o formato da diretiva `return`. ⚠️ não verificado (inferido do NGINX)
- NGINX permite `return 301 http://...` ou `return 301 /interno`.
- O corpo da resposta de redirect é opcional; pode ser uma página HTML com link.
- Redirects muito profundos podem causar loop; o servidor não precisa detectar isso.

## Perguntas de autoavaliação
- Qual a diferença entre 301 e 302?
- O servidor deve seguir o redirect ou apenas retorná-lo ao cliente?
- O que fazer se a URL de destino do redirect for inválida?

## Fontes consultadas
- subject Webserv 42, páginas 11-12
- RFC 2616, seções 10.3.2, 10.3.3
