---
tags: [webserv, directory, listing, autoindex]
fonte-principal: subject-webserv
status: revisado
---

# Directory Listing

## TL;DR
Quando o URL solicitado é um diretório e não há um arquivo padrão, o servidor pode gerar uma página HTML listando o conteúdo do diretório. É configurável com autoindex on/off.

## Contexto no Webserv
O subject exige a opção de "ativar ou desativar a listagem de diretórios". ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
Config: `autoindex on;` ou `autoindex off;` (dentro de location).

- **autoindex on**: se o path for um diretório e não houver index, gera HTML com a lista de arquivos.
- **autoindex off**: se o path for um diretório e não houver index, retorna 403 Forbidden ou 404.

O HTML gerado deve conter links para cada arquivo/pasta, similar ao que NGINX ou Apache fazem.

> [!note]- 🔍 Aprofundando: formato da listagem
> Uma listagem simples de diretório:
> ```html
> <html>
> <head><title>Index of /</title></head>
> <body>
> <h1>Index of /</h1>
> <hr>
> <pre>
> <a href="../">../</a>
> <a href="style.css">style.css</a>           1234 B
> <a href="index.html">index.html</a>         5678 B
> <a href="images/">images/</a>
> </pre>
> <hr>
> </body>
> </html>
> ```
>
> O servidor usa `opendir()` + `readdir()` + `closedir()` para listar. ✅ `Fonte: subject Webserv 42` (funções autorizadas)
>
> Devem ser ignorados os entries `.` e `..` (exceto `..` no link de volta).
>
> **Conexões:**
> - `Complemento:` [[default-file]]
> - `Complemento:` [[routing]]
> - `Complemento:` [[static-file-serving]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica o formato HTML da listagem. ⚠️ não verificado
- NGINX permite personalizar o estilo da listagem.
- A listagem deve incluir informações como tamanho e data de modificação.

## Perguntas de autoavaliação
- O que fazer se autoindex está off e não há index file?
- Como listar o diretório de forma segura (sem expor caminhos absolutos)?
- Deve incluir arquivos ocultos (começando com `.`)?

## Fontes consultadas
- subject Webserv 42, páginas 11-12
- man opendir(3), man readdir(3)
