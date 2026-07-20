---
tags: [webserv, cgi, ambiente, env-vars]
fonte-principal: rfc-3875
status: revisado
---

# Variáveis de Ambiente CGI

## TL;DR
O servidor passa informações da requisição HTTP para o CGI através de variáveis de ambiente. O CGI usa essas variáveis para saber qual recurso foi solicitado, quem solicitou, etc.

## Contexto no Webserv
O subject exige que "a solicitação completa e os argumentos fornecidos pelo cliente devem estar disponíveis para o CGI". ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona (Visão Geral)

O servidor monta um array de strings `"NOME=valor"` a partir da requisição HTTP e passa para o CGI via `execve()`. Exemplos:

- O header `User-Agent` vira `HTTP_USER_AGENT`
- `QUERY_STRING` vem da parte após `?` na URL
- `CONTENT_LENGTH` e `CONTENT_TYPE` vêm dos headers da requisição

> [!note]- 🔍 Aprofundando: lista completa e obrigatória
> Consulte [[cgi-environment-checklist]] para a **lista exata e obrigatória** de variáveis que o CGI espera, incluindo:
> - `REDIRECT_STATUS` (essencial para PHP-CGI)
> - `SERVER_PORT`, `SCRIPT_NAME`, `PATH_INFO`, `PATH_TRANSLATED`
> - `GATEWAY_INTERFACE`, `SERVER_PROTOCOL`
> - E como cada uma deriva dos dados da requisição
>
> **Conexões:**
> - `Complemento:` [[cgi-environment-checklist]] — lista detalhada
> - `Complemento:` [[cgi-overview]]
> - `Complemento:` [[cgi-request-body]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica a lista exata de variáveis. 📚 A lista vem da RFC 3875.
- Variáveis como `REMOTE_ADDR` (IP do cliente) não são mencionadas no subject.
- `AUTH_TYPE` e `REMOTE_USER` (autenticação) não são necessárias.

## Perguntas de autoavaliação
- Como o servidor converte um header HTTP como `X-Custom-Header` para variável CGI?
- O que fazer se `QUERY_STRING` estiver vazia?
- Como garantir que todas as variáveis estejam disponíveis para o CGI?

## Fontes consultadas
- subject Webserv 42, página 12
- RFC 3875 (CGI 1.1), seção 4.1
