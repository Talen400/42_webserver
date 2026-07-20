---
tags: [webserv, configuracao, location, roteamento]
fonte-principal: subject-webserv
status: revisado
---

# Location Blocks

## TL;DR
Blocos `location` no arquivo de configuração definem regras específicas para URLs ou rotas: quais métodos são aceitos, redirecionamentos, diretório base, listagem de diretórios, upload e CGI.

## Contexto no Webserv
O subject exige especificar regras em um URL/rota para um site, com as seguintes opções. ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona

```
server {
    location / {
        methods          GET;
        root             /var/www/html;
        index            index.html;
        autoindex        off;
    }

    location /cgi-bin {
        methods          GET POST;
        cgi_ext          .php;
        root             /var/www/cgi;
    }

    location /redirect {
        return           301 http://novo-site.com;
    }

    location /upload {
        methods          POST;
        upload_store     /var/uploads;
        client_max_body_size 100M;
    }
}
```

**Diretivas possíveis dentro de location:**

| Diretiva | Função |
|----------|--------|
| `methods` | Lista de métodos HTTP permitidos |
| `return` | Redirecionamento HTTP (código + URL) |
| `root` | Diretório base para arquivos |
| `autoindex` | Ativar/desativar listagem de diretórios |
| `index` | Arquivo padrão para diretórios |
| `upload_store` | Diretório para armazenar uploads |
| `cgi_ext` | Extensão de arquivo para executar como CGI |
| `client_max_body_size` | Sobrescreve limite do server |

> [!note]- 🔍 Aprofundando: matching de location
> O location mais específico que corresponde ao path da URL deve ser usado. Ex:
> - Location `/` corresponde a qualquer URL (fallback).
> - Location `/cgi-bin` corresponde a URLs começando com `/cgi-bin`.
> - Se a URL é `/cgi-bin/script.php`, o location `/cgi-bin` é usado.
>
> **Conexões:**
> - `Complemento:` [[routing]]
> - `Complemento:` [[config-format]]
> - `Complemento:` [[redirecionamento]]
> - `Complemento:` [[cgi-overview]]

## O que o subject NÃO exige (mas é bom saber)
- Regex em location não é necessário. ✅ `Fonte: subject Webserv 42, IV.3`
- Herança de diretivas (location herdar do server) não está especificada. ⚠️ não verificado
- NGINX suporta location com prefixo, regex, exact match (`=`), etc.

## Perguntas de autoavaliação
- Qual location é usado se a URL é `/cgi-bin/script.php` e existem `/` e `/cgi-bin`?
- Como o servidor decide entre servir arquivo estático vs executar CGI para uma mesma URL?
- O que fazer se um location permitir `methods POST` mas o cliente enviar GET?

## Fontes consultadas
- subject Webserv 42, páginas 11-13
