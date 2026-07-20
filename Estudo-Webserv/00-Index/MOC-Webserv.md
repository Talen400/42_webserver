# MOC — Webserv: Servidor HTTP em C++98

> Mapa de Conteúdo — ponto de entrada do vault de estudo.

---

## ▶️ O Ciclo de Vida da Requisição

- [[request-lifecycle-diagram]] — ⭐ Fluxo completo: socket → parsing → roteamento → resposta (fio condutor do vault)

---

## 01 — Protocolo HTTP

Fundamentos do protocolo que o servidor implementa.

- [[http-request-format]] — Formato da requisição HTTP
- [[http-response-format]] — Formato da resposta HTTP
- [[http-methods]] — GET, POST, DELETE
- [[http-status-codes]] — 200, 301, 400, 404, 500, etc.
- [[http-headers]] — Content-Type, Content-Length, Host, etc.
- [[http-versions]] — HTTP/1.0 vs HTTP/1.1
- [[state-machine-parsing]] — ⭐ Máquina de estados para parsing non-blocking
- [[chunked-transfer-encoding]] — ⭐ Parsing de chunked transfer

## 02 — Arquitetura do Servidor

Como o servidor é estruturado.

- [[arquitetura-servidor-http]] — Visão geral do servidor
- [[non-blocking-io]] — I/O não bloqueante com poll()
- [[poll-vs-select-vs-kqueue]] — Comparação das APIs de monitoramento
- [[socket-programming]] — socket(), bind(), listen(), accept()
- [[event-loop]] — O loop principal do servidor
- [[multiple-ports]] — Escutar em várias portas
- [[event-loop-abstraction]] — ⭐ Abstração para trocar poll/select/kqueue

## 03 — Arquivo de Configuração

Formato e diretivas do arquivo .conf (inspirado no NGINX).

- [[config-format]] — Estrutura geral do config
- [[listen-directive]] — interface:porta
- [[root-directive]] — Mapeamento URL → diretório
- [[error-pages]] — Páginas de erro personalizadas
- [[client-max-body-size]] — Limite de tamanho do corpo
- [[location-blocks]] — Regras por URL/rota
- [[config-examples]] — ⭐ Exemplos de arquivos .conf

## 04 — Request Handling

Processamento de requisições HTTP.

- [[request-parsing]] — Parse da requisição
- [[routing]] — Roteamento para o location correto
- [[static-file-serving]] — Servir arquivos estáticos
- [[directory-listing]] — Listagem de diretórios
- [[default-file]] — Arquivo padrão (index.html)
- [[redirecionamento]] — HTTP redirect (301/302)
- [[file-upload]] — Upload de arquivos via POST
- [[multipart-form-data]] — ⭐ Parse de multipart/form-data (boundary)

## 05 — CGI

Common Gateway Interface — executar scripts dinâmicos.

- [[cgi-overview]] — O que é CGI
- [[cgi-environment-variables]] — Variáveis de ambiente (visão geral)
- [[cgi-environment-checklist]] — ⭐ Lista exata e obrigatória para a 42
- [[cgi-request-body]] — Envio do corpo para o CGI
- [[cgi-response]] — Leitura da saída do CGI
- [[cgi-execution]] — fork(), execve(), pipes

## 06 — Erros e Resiliência

Lidando com falhas e mantendo o servidor operacional.

- [[error-handling]] — Códigos de erro e páginas
- [[timeouts]] — Evitar travamentos
- [[keep-alive]] — Conexão persistente

## 07 — macOS vs Linux

Diferenças de plataforma relevantes.

- [[fcntl-macos]] — fcntl(), O_NONBLOCK, FD_CLOEXEC
- [[non-blocking-macos]] — ⭐ Non-blocking no macOS

## 09 — Testes e Ferramentas

- [[test-strategies]] — ⭐ Telnet, curl, Python, stress test

## 10 — C++98 Específico

- [[memory-management]] — ⭐ RAII, containers, evitar vazamentos

---

## Glossário

- [[GLOSSARY]] — Definições de termos técnicos

---

## Flashcards

- [[perguntas-provaveis-e-respostas]] — Autoavaliação no formato spaced repetition

---

> **Status:** vault gerado a partir do subject Webserv 42 (versão 24.0) e RFCs do HTTP.
> Cada nota tem fonte explicitamente citada (✅ subject, 📚 RFC, ⚠️ não verificado).
