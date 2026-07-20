---
tags: [webserv, fluxo, lifecycle, diagrama]
fonte-principal: subject-webserv
status: revisado
---

# Fluxo Completo de uma Requisição (Diagrama)

## TL;DR
Do socket à resposta: as 10 etapas que toda requisição percorre no servidor Webserv. Este arquivo é o "fio condutor" que conecta todas as notas do vault.

## Contexto no Webserv
Todas as exigências do subject se conectam neste fluxo: non-blocking I/O, parsing stateful, roteamento, CGI, e resiliência.

## O Fluxo

### 1. Inicialização: socket → bind → listen
Para cada `listen` no config:
- `socket()` cria o socket TCP. ✅ `Fonte: subject Webserv 42`
- `bind()` associa à interface:porta.
- `listen()` marca como passivo.

**Notas relacionadas:** [[socket-programming]], [[listen-directive]], [[multiple-ports]]

### 2. Event Loop: poll() aguarda eventos
- `poll()` (ou equivalente) monitora todos os FDs (listen + clientes).
- Só prossegue quando há dados ou timeout.

**Notas relacionadas:** [[event-loop]], [[poll-vs-select-vs-kqueue]], [[non-blocking-io]]

### 3. accept(): nova conexão
- Evento `POLLIN` no listen fd → `accept()`.
- Novo fd de cliente é configurado como `O_NONBLOCK`.
- Adicionado ao `poll()`.

**Notas relacionadas:** [[socket-programming]], [[multiple-ports]]

### 4. read() + Máquina de Estados
- `POLLIN` no fd do cliente → `read()`.
- Dados alimentam a máquina de estados de parsing.
- Pode levar múltiplos ciclos até a requisição estar completa.

**Notas relacionadas:** [[state-machine-parsing]], [[request-parsing]], [[chunked-transfer-encoding]]

### 5. Roteamento: match com location
- Requisição completa: extrai método, URI, headers.
- Encontra o location block mais específico.
- Verifica se o método é permitido.

**Notas relacionadas:** [[routing]], [[location-blocks]], [[http-methods]]

### 6. Ação: decidir o que fazer

| Condição | Ação | Próximo passo |
|----------|------|---------------|
| Diretiva `return` no location | Redirect | Montar resposta 301/302 |
| Extensão `.php/.py` e `cgi_ext` | CGI | fork + execve |
| POST com `upload_store` | Upload | Salvar arquivo |
| DELETE | Remover | `unlink()` |
| GET em diretório + index file | Servir index | Ler e enviar |
| GET em diretório + autoindex on | Listar dir | Gerar HTML |
| GET em diretório + autoindex off | 403 | Página de erro |
| GET/POST em arquivo estático | Servir estático | Ler e enviar |

**Notas relacionadas:** [[redirecionamento]], [[cgi-overview]], [[file-upload]], [[static-file-serving]], [[directory-listing]], [[default-file]]

### 7. CGI (se aplicável)
- `fork()` + `pipe()` + `execve()`.
- Envia corpo da requisição via stdin.
- Lê resposta do stdout.
- `waitpid()` para coletar status.

**Notas relacionadas:** [[cgi-execution]], [[cgi-environment-checklist]], [[cgi-request-body]], [[cgi-response]]

### 8. Montar Resposta
- Headers: Content-Type, Content-Length, Location (se redirect), Connection.
- Código de status preciso.

**Notas relacionadas:** [[http-response-format]], [[http-status-codes]], [[http-headers]], [[error-pages]]

### 9. Enviar Resposta (monitorando POLLOUT)
- O fd do cliente é marcado para `POLLOUT`.
- Quando `poll()` sinaliza pronto, `send()` ou `write()`.
- Pode levar múltiplos ciclos (non-blocking write).

**Notas relacionadas:** [[event-loop]], [[non-blocking-io]]

### 10. Finalizar ou Keep-Alive
- Se `Connection: close` ou HTTP/1.0: fecha socket.
- Se keep-alive: reseta parser, aguarda próxima requisição.
- Timeout fecha conexões inativas.

**Notas relacionadas:** [[keep-alive]], [[timeouts]]

> [!note]- 🔍 Aprofundando: onde as coisas podem dar errado
> **Parsing:** Buffer parcial nunca é limpo entre requisições (keep-alive) → dados residuais corrompem a próxima requisição.
>
> **poll():** Se o fd não for removido do poll() após `close()`, o `poll()` retorna `POLLNVAL` e pode causar loop infinito.
>
> **CGI:** Pipe de saída do CGI não configurado como non-blocking → servidor trava em `read()`.
>
> **Memory:** `Client` não destruído ao fechar → vazamento de fd e memória.
>
> **Conexões:** Esta nota conecta **todas** as notas do vault. É o ponto central de navegação.

## Perguntas de autoavaliação
- Em que etapa do fluxo o timeout deve ser verificado?
- O que acontece se o `write()` da resposta retornar EAGAIN?
- Como o fluxo muda se a requisição for chunked?
- O que acontece se o CGI não existir (etapa 7)?

## Fontes consultadas
- subject Webserv 42, páginas 7-13
- RFC 2616, seções 4-10
