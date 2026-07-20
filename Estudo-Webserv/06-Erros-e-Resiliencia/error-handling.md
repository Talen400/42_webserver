---
tags: [webserv, erros, resiliencia, status-codes]
fonte-principal: subject-webserv
status: revisado
---

# Error Handling

## TL;DR
O servidor deve lidar graciosamente com erros: recursos não encontrados, métodos não permitidos, requisições mal formatadas, etc. Cada erro retorna o código HTTP apropriado e uma página de erro.

## Contexto no Webserv
O subject exige que "seu programa não deve travar sob nenhuma circunstância" e que o servidor tenha páginas de erro padrão. ✅ `Fonte: subject Webserv 42, II, IV.1`

## Como funciona
O servidor encontra erros em vários pontos:

| Ponto | Erro | Ação |
|-------|------|------|
| Parsing | Request-line inválida | 400 Bad Request |
| Parsing | Header mal formatado | 400 Bad Request |
| Roteamento | Método não permitido | 405 Method Not Allowed |
| Roteamento | Recurso não encontrado | 404 Not Found |
| Sistema | Arquivo sem permissão | 403 Forbidden |
| Sistema | Corpo muito grande | 413 Payload Too Large |
| CGI | Execução falhou | 500 Internal Server Error |
| Sistema | Erro interno inesperado | 500 Internal Server Error |
| Método | Método não implementado | 501 Not Implemented |

> [!note]- 🔍 Aprofundando: resiliência
> O subject enfatiza: "A resiliência é fundamental. Seu servidor deve permanecer operacional em todos os momentos." ✅ `Fonte: subject Webserv 42, IV.3`
>
> Isso significa:
> - Erro em uma conexão não pode derrubar outras.
> - Exceções em C++ devem ser capturadas (se usadas).
> - FDs devem ser fechados em caso de erro.
> - Memória deve ser liberada.
> - O servidor continua rodando mesmo após erros.
>
> **Conexões:**
> - `Complemento:` [[http-status-codes]]
> - `Complemento:` [[error-pages]]
> - `Complemento:` [[timeouts]]

## O que o subject NÃO exige (mas é bom saber)
- Páginas de erro podem ser arquivos HTML completos ou HTML inline simples.
- O subject não especifica mensagens de erro detalhadas.
- Logs de erro não são exigidos, mas são úteis para debugging.

## Perguntas de autoavaliação
- O que fazer se uma exceção for lançada durante o processamento de uma requisição?
- Como garantir que uma conexão com erro não afete as demais?
- Qual a diferença entre 500 e 501?

## Fontes consultadas
- subject Webserv 42, páginas 3, 9, 12
