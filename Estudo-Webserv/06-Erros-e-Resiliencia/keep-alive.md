---
tags: [webserv, keep-alive, conexao, http]
fonte-principal: rfc-2616
status: revisado
---

# Keep-Alive (Conexão Persistente)

## TL;DR
Keep-alive permite reutilizar a mesma conexão TCP para múltiplas requisições, evitando o custo de abrir e fechar conexões repetidamente. HTTP/1.1 usa keep-alive por padrão.

## Contexto no Webserv
O subject não menciona explicitamente keep-alive. É um recurso opcional, mas vantajoso para performance. O comportamento padrão HTTP/1.1 é conexão persistente. 📚 `Fonte: RFC 2616`

## Como funciona
- Cliente envia `Connection: keep-alive` (ou HTTP/1.1 usa por padrão).
- Servidor não fecha a conexão após enviar a resposta.
- Cliente pode enviar outra requisição no mesmo socket.
- Servidor fecha a conexão após um timeout de inatividade ou se receber `Connection: close`.

```
[Abre conexão] → [Req 1] → [Resp 1] → [Req 2] → [Resp 2] → [Fecha]
```

> [!note]- 🔍 Aprofundando: implicações
> Para suportar keep-alive:
> - O servidor não pode fechar a conexão imediatamente após enviar a resposta.
> - Deve ler novamente do socket para a próxima requisição.
> - O timeout é necessário para evitar conexões abertas indefinidamente.
> - O servidor deve detectar `Connection: close` para não fazer keep-alive.
>
> No Webserv, implementar keep-alive adiciona complexidade porque o servidor precisa:
> 1. Saber quando uma resposta foi completamente enviada.
> 2. Resetar o estado do parser para ler a próxima requisição.
> 3. Gerenciar timeout para fechar conexões inativas.
>
> **Conexões:**
> - `Complemento:` [[timeouts]]
> - `Complemento:` [[event-loop]]
> - `Complemento:` [[http-versions]]

## O que o subject NÃO exige (mas é bom saber)
- Keep-alive não é exigido pelo subject. É opcional.
- HTTP/1.0 usa `Connection: keep-alive` explicitamente para persistir.
- NGINX gerencia keep-alive com `keepalive_timeout` e `keepalive_requests`.

## Perguntas de autoavaliação
- Como o servidor sabe que a resposta foi completamente enviada?
- O que fazer com o parser entre requisições em uma conexão keep-alive?
- O servidor deve implementar keep-alive para HTTP/1.1?

## Fontes consultadas
- RFC 2616, seção 8.1
- subject Webserv 42 (não menciona keep-alive) ⚠️ não verificado
