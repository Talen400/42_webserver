---
tags: [webserv, timeout, resiliencia, conexao]
fonte-principal: subject-webserv
status: revisado
---

# Timeouts

## TL;DR
Timeouts evitam que conexões lentas ou abandonadas consumam recursos do servidor indefinidamente. O servidor fecha conexões que excedem o tempo limite.

## Contexto no Webserv
O subject exige que "uma solicitação ao seu servidor nunca deve travar indefinidamente". ✅ `Fonte: subject Webserv 42, IV.1`

## Como funciona
O servidor deve rastrear, para cada conexão, quando foi a última atividade. Em cada iteração do event loop:

```
para cada conexão:
    se (agora - ultima_atividade > timeout_configurado)
        fechar conexão (com timeout ou erro apropriado)
```

**Timeouts relevantes:**
- **Recebimento da requisição**: cliente demora para enviar dados.
- **Processamento CGI**: CGI demora para responder.
- **Envio da resposta**: cliente demora para ler.

> [!note]- 🔍 Aprofundando: implementação prática
> Uma abordagem simples:
> - Armazenar `time_t last_activity` por conexão.
> - Em cada loop, verificar se `difftime(now, last_activity) > TIMEOUT_SECONDS`.
> - Se o timeout expirar enquanto o cliente ainda está enviando a requisição, retornar 408 Request Timeout.
> - Se o timeout expirar durante a resposta, apenas fechar a conexão.
>
> **Conexões:**
> - `Complemento:` [[event-loop]]
> - `Complemento:` [[error-handling]]
> - `Complemento:` [[keep-alive]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica valores de timeout. ⚠️ não verificado
- NGINX usa `keepalive_timeout` (65s), `send_timeout` (60s), `client_body_timeout` (60s).
- Timeout de CGI é opcional mas recomendado.
- Timeout do `poll()` pode ser usado para acordar periodicamente e verificar timeouts.

## Perguntas de autoavaliação
- Como o servidor verifica timeouts sem chamar time() a cada iteração?
- O que fazer se o timeout expirar enquanto o CGI ainda está executando?
- Deve-se diferenciar timeout de leitura vs timeout de escrita?

## Fontes consultadas
- subject Webserv 42, página 9
