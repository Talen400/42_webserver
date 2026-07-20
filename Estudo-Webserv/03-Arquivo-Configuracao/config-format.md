---
tags: [webserv, configuracao, arquivo, nginx]
fonte-principal: subject-webserv
status: revisado
---

# Formato do Arquivo de Configuração

## TL;DR
O servidor lê um arquivo de configuração (inspirado no NGINX) que define portas, diretórios, limites e regras de roteamento. É passado como argumento na linha de comando. ✅ `Fonte: subject Webserv 42`

## Contexto no Webserv
O subject exige um arquivo de configuração, fornecido como argumento ou disponível em caminho padrão. ✅ `Fonte: subject Webserv 42, IV` O formato é inspirado no NGINX. ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
Estrutura geral:

```
server {
    listen              127.0.0.1:8080;
    server_name         exemplo.com;       # opcional (virtual hosts)
    root                /var/www/html;
    client_max_body_size 10M;
    error_page          404 /error/404.html;
    error_page          500 /error/500.html;

    location / {
        methods             GET POST;
        index               index.html;
        autoindex           off;
    }

    location /upload {
        methods             POST;
        upload_store        /var/uploads;
    }

    location /cgi-bin {
        methods             GET POST;
        cgi_ext             .php;
    }
}
```

> [!note]- 🔍 Aprofundando: parser do config
> O parser deve:
> 1. Tokenizar o arquivo (palavras, chaves, ponto-e-vírgula).
> 2. Construir uma árvore de blocos `server` e `location`.
> 3. Validar cada diretiva (nome conhecido, valor válido).
> 4. Associar cada `server` a um listen socket.
> 5. Cada `location` herda do server e pode sobrescrever valores.
>
> Não é necessário suportar herança complexa como NGINX — apenas o básico: location dentro de server.
>
> **Conexões:**
> - `Complemento:` [[listen-directive]]
> - `Complemento:` [[location-blocks]]
> - `Complemento:` [[routing]]

## O que o subject NÃO exige (mas é bom saber)
- O subject diz "você pode se inspirar na seção server do NGINX", mas não exige compatibilidade total com NGINX.
- Expressões regulares em location não são exigidas. ✅ `Fonte: subject Webserv 42, IV.3`
- Diretivas como `server_name` são opcionais (para virtual hosts). ✅ `Fonte: subject Webserv 42, IV.3`
- Herança de diretivas (location herdar configurações do server) não está especificada no subject. ⚠️ não verificado

## Perguntas de autoavaliação
- Como o parser lida com blocos aninhados?
- O que acontece se uma diretiva obrigatória estiver ausente?
- Como diferenciar valores obrigatórios de opcionais no parser?

## Fontes consultadas
- subject Webserv 42, páginas 11-13
- Documentação do NGINX (para inspiração de formato)
