---
tags: [webserv, configuracao, erro, paginas]
fonte-principal: subject-webserv
status: revisado
---

# Páginas de Erro

## TL;DR
O administrador pode configurar páginas HTML personalizadas para cada código de erro HTTP. Se não houver configuração, o servidor usa páginas padrão.

## Contexto no Webserv
O subject exige que o servidor tenha páginas de erro padrão se nenhuma for fornecida. ✅ `Fonte: subject Webserv 42, IV.1` Também permite configurar páginas de erro personalizadas no arquivo de configuração. ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
No arquivo de configuração:

```
error_page 404 /errors/404.html;
error_page 500 /errors/500.html;
error_page 403 /errors/403.html;
```

Quando um erro ocorre:
1. O servidor verifica se há uma `error_page` configurada para aquele código.
2. Se sim, serve o arquivo HTML configurado (dentro do root).
3. Se não, gera uma página HTML inline simples com o código e mensagem.

> [!note]- 🔍 Aprofundando: página padrão inline
> A página padrão pode ser algo como:
> ```html
> <html>
> <head><title>404 Not Found</title></head>
> <body>
> <h1>404 Not Found</h1>
> </body>
> </html>
> ```
>
> O servidor deve garantir que a resposta tenha:
> - Status code correto
> - Content-Type: text/html
> - Content-Length apropriado
>
> **Conexões:**
> - `Complemento:` [[http-status-codes]]
> - `Complemento:` [[error-handling]]
> - `Complemento:` [[config-format]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica o formato das páginas de erro padrão. ⚠️ não verificado
- NGINX permite herdar error_page de níveis superiores.
- Páginas de erro podem ser arquivos HTML completos com CSS e imagens.

## Perguntas de autoavaliação
- O que fazer se a página de erro configurada não existir?
- A página de erro deve ser servida com o código de status do erro ou com 200?
- Como decidir entre página personalizada e padrão?

## Fontes consultadas
- subject Webserv 42, páginas 9, 11
