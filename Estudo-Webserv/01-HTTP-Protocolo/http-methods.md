---
tags: [webserv, http, protocolo, metodos]
fonte-principal: subject-webserv
status: revisado
---

# HTTP Methods

## TL;DR
O HTTP define métodos (verbos) que indicam a ação desejada sobre um recurso. O Webserv exige suporte a GET (ler), POST (criar/enviar) e DELETE (remover).

## Contexto no Webserv
O subject exige que o servidor implemente **pelo menos** GET, POST e DELETE. ✅ `Fonte: subject Webserv 42, IV.1`. O arquivo de configuração deve permitir restringir quais métodos são aceitos por rota. ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona

| Método | Função | Uso no Webserv |
|--------|--------|----------------|
| GET | Recuperar recurso | Servir arquivos estáticos, listar diretórios |
| POST | Enviar dados ao servidor | Upload de arquivos, envio de formulário para CGI |
| DELETE | Remover recurso | Remover arquivo do sistema de arquivos |

> [!note]- 🔍 Aprofundando: comportamento esperado
> **GET:**
> - Deve retornar o conteúdo do arquivo (200) ou erro (404, 403).
> - Se o path for um diretório, procurar arquivo padrão (index.html) ou listar diretório.
>
> **POST:**
> - O corpo da requisição contém os dados.
> - Salvar em arquivo no local configurado (upload).
> - Ou passar para CGI processar.
>
> **DELETE:**
> - Remover o arquivo do sistema de arquivos.
> - Retornar 204 No Content em caso de sucesso, 404 se não existir, 403 se sem permissão.
>
> **Conexões:**
> - `Complemento:` [[static-file-serving]]
> - `Complemento:` [[directory-listing]]
> - `Complemento:` [[file-upload]]
> - `Complemento:` [[cgi-overview]]

## O que o subject NÃO exige (mas é bom saber)
- HEAD, PUT, PATCH, OPTIONS não são exigidos.
- O comportamento de DELETE em diretórios não é especificado. ⚠️ não verificado
- Métodos não implementados devem retornar 405 Method Not Allowed. 📚 `Fonte: RFC 2616, seção 10.4.6`

## Perguntas de autoavaliação
- Qual a diferença prática entre GET e POST no contexto de CGI?
- O servidor deve processar um DELETE em um diretório não vazio?
- O que retornar se o método solicitado não estiver na lista de permitidos para a rota?

## Fontes consultadas
- subject Webserv 42, páginas 7-12
- RFC 2616, seções 9.3, 9.5, 9.7
