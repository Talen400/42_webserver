# PROGRESS.md

Estado atual do vault. O agente deve ler este arquivo **antes** de gerar qualquer nota (para não duplicar/reprocessar) e **atualizá-lo** ao final de cada rodada.

Não edite manualmente a menos que precise corrigir algo — o agente mantém isso.

---

## Status por Fase

- [x] Fase 1 — Leitura e Mapeamento Base (subject lido, ver `REFERENCES.md`)
- [x] Fase 2 — Notas Base (35/35)
- [x] Fase 3 — Notas de Aprofundamento (8/8)
- [x] Fase 4 — MOC e Glossário (2/2)
- [x] Fase 5 — Flashcards (1/1)
- [x] Fase 6 — Notas de Entrega do Projeto (2/2)

## Notas Concluídas

### Fase 2 — Notas Base (34/35)

**01-HTTP-Protocolo:**
- [x] http-request-format.md
- [x] http-response-format.md
- [x] http-methods.md
- [x] http-status-codes.md
- [x] http-headers.md
- [x] http-versions.md

**02-Servidor-HTTP:**
- [x] arquitetura-servidor-http.md
- [x] non-blocking-io.md
- [x] poll-vs-select-vs-kqueue.md
- [x] socket-programming.md
- [x] event-loop.md
- [x] multiple-ports.md
- [x] funcoes-autorizadas.md

**03-Arquivo-Configuracao:**
- [x] config-format.md
- [x] listen-directive.md
- [x] root-directive.md
- [x] error-pages.md
- [x] client-max-body-size.md
- [x] location-blocks.md
- [x] config-examples.md (extra — não listado originalmente, mas útil)

**04-Request-Handling:**
- [x] request-parsing.md
- [x] routing.md
- [x] static-file-serving.md
- [x] directory-listing.md
- [x] default-file.md
- [x] redirecionamento.md
- [x] file-upload.md

**05-CGI:**
- [x] cgi-overview.md
- [x] cgi-environment-variables.md
- [x] cgi-request-body.md
- [x] cgi-response.md
- [x] cgi-execution.md

**06-Erros-e-Resiliencia:**
- [x] error-handling.md
- [x] timeouts.md
- [x] keep-alive.md

**07-MacOS-vs-Linux:**
- [x] fcntl-macos.md
- [x] non-blocking-macos.md (extra — útil para completude)

### Fase 3 — Notas de Aprofundamento (8/8)

- [x] state-machine-parsing.md
- [x] chunked-transfer-encoding.md
- [x] multipart-form-data.md
- [x] cgi-environment-checklist.md
- [x] event-loop-abstraction.md
- [x] test-strategies.md
- [x] memory-management.md
- [x] request-lifecycle-diagram.md

### Fase 4 — MOC e Glossário (2/2)

- [x] MOC-Webserv.md
- [x] GLOSSARY.md

### Fase 5 — Flashcards (1/1)

- [x] perguntas-provaveis-e-respostas.md

### Fase 6 — Notas de Entrega do Projeto (2/2)

- [x] readme-checklist.md
- [x] avaliacao-por-pares.md

## Marcadas como `⚠️ não verificado` (revisar depois)

_(registrar aqui qualquer afirmação duvidosa encontrada nas notas)_

## Notas Pendentes / Bloqueadas

_(nenhuma no momento — vault completo em 47 notas)_

## Observações da Última Rodada

- Estrutura do vault refatorada: `agent/` (5 arquivos separados) + `Estudo-Webserv/` (vault).
- Subject PDF (versão 24.0) lido e extraído para referência cruzada.
- Vault existente (~44 notas) mapeado contra o novo `TASKS.md`; 3 notas pendentes identificadas.
- 3 notas pendentes criadas: `funcoes-autorizadas.md`, `readme-checklist.md`, `avaliacao-por-pares.md`
- MOC atualizado: seções 08 (Funções Autorizadas) e 11 (Entrega do Projeto) adicionadas
- Correções conceituais aplicadas:
  - `cgi-request-body.md`: approfondimento corrigido — distinguido chunked (de-chunk obrigatório antes de enviar ao CGI) de multipart (CGI recebe já de-chunked, parseia o multipart internamente)
  - `cgi-overview.md`: "Requisições multipart" corrigido para "Requisições chunked"; regra do bônus (Cap. VI) adicionada — "só avaliado se obrigatório 100%"
  - Exceção do config file já estava coberta em `non-blocking-io.md` (linha 39)
  - Resiliência já estava coberta em `error-handling.md` (linha 31)
