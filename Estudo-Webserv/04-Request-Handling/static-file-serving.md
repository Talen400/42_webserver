---
tags: [webserv, static, arquivos, servindo]
fonte-principal: subject-webserv
status: revisado
---

# Static File Serving

## TL;DR
Servir arquivos estáticos é a função mais básica do servidor: ler um arquivo do disco e enviar o conteúdo na resposta HTTP com o tipo MIME correto.

## Contexto no Webserv
O subject exige ser "capaz de servir um site totalmente estático". ✅ `Fonte: subject Webserv 42, IV.1`

## Como funciona
```
GET /css/style.css HTTP/1.1
           ↓
Caminho real: /var/www/html/css/style.css
           ↓
Abrir arquivo → ler conteúdo → montar resposta
           ↓
HTTP/1.1 200 OK
Content-Type: text/css
Content-Length: 1234
           ↓
(conteúdo do arquivo)
```

Passos:
1. Determinar caminho real (root + URI)
2. Verificar se arquivo existe e é legível (`access()`, `stat()`)
3. Determinar `Content-Type` pela extensão
4. Ler o arquivo (`open()` + `read()`)
5. Montar resposta com headers e corpo

> [!note]- 🔍 Aprofundando: I/O de arquivo em servidor non-blocking
> A leitura de arquivos do disco é bloqueante por natureza. O subject permite usar `read()` e `open()` sem poll() para arquivos de configuração, mas para arquivos estáticos:
>
> - Uma abordagem é abrir o arquivo, ler tudo para um buffer na memória (em modo non-blocking ou usando read() tradicional), e depois escrever no socket via poll().
> - Outra abordagem é usar um buffer interno e escrever em partes quando o socket estiver pronto.
>
> **stat()** é útil para verificar:
> - Se o path existe (`stat()` retorna 0)
> - Se é arquivo regular ou diretório (`S_ISREG()`, `S_ISDIR()`)
> - Tamanho do arquivo (`st_size`) para Content-Length
> - Permissões (indiretamente via `access()`)
>
> **Conexões:**
> - `Complemento:` [[routing]]
> - `Complemento:` [[http-headers]]
> - `Complemento:` [[default-file]]

## O que o subject NÃO exige (mas é bom saber)
- Suporte a byte ranges (Range header) não é exigido.
- Cache de arquivos em memória não é mencionado.
- O subject não especifica como lidar com arquivos muito grandes. ⚠️ não verificado

## Perguntas de autoavaliação
- Como o servidor determina o Content-Type de um arquivo?
- O que fazer se o arquivo existe mas não tem permissão de leitura?
- Como servir um arquivo grande sem ocupar toda a memória?

## Fontes consultadas
- subject Webserv 42, páginas 9-10
- man stat(2), man open(2), man read(2)
