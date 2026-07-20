# TASKS.md

Plano de execução. Este arquivo é o que muda entre sessões — ao concluir uma fase ou trocar o foco, edite aqui, não em `AGENT.md`.

Antes de começar qualquer fase, consulte `PROGRESS.md` para não regenerar o que já existe.

---

## Fase 1: Leitura e Mapeamento Base — ✅ CONCLUÍDA

Subject lido na íntegra (versão 24.0). Estrutura completa registrada em `REFERENCES.md`. Restrições técnicas fixas (funções autorizadas, regra do poll único, proibição de checar errno, fork só para CGI, escopo do host virtual) já foram movidas para `AGENT.md`, seção "Restrições Técnicas Fixas do Subject".

---

## Fase 2: Gerar Notas Base (Obrigatórias)

Com base na leitura, gere as notas abaixo — cada uma seguindo o formato padrão definido em `AGENT.md`. Caminhos completos em `DIR.md`.

**01-HTTP-Protocolo:**
- `http-request-format.md` — formato da requisição (linha de requisição, headers, corpo)
- `http-response-format.md` — formato da resposta (linha de status, headers, corpo)
- `http-methods.md` — GET, POST, DELETE (o que cada um faz e quando usar)
- `http-status-codes.md` — códigos mais importantes (200, 301, 400, 404, 405, 413, 500, etc.)
- `http-headers.md` — headers relevantes (Host, Content-Length, Content-Type, Connection, Transfer-Encoding)
- `http-versions.md` — diferenças entre HTTP/1.0 e 1.1 (Keep-Alive, Host obrigatório). Nota: o subject sugere HTTP/1.0 como ponto de referência, não como requisito — e oferece deliberadamente só um subconjunto do RFC (host virtual, por exemplo, está fora do escopo)

**02-Servidor-HTTP:**
- `arquitetura-servidor-http.md` — visão geral do que o servidor faz
- `non-blocking-io.md` — o que é, por que o subject exige, como funciona com poll()
- `poll-vs-select-vs-kqueue.md` — comparação das 4 opções permitidas (`poll`, `select`, `epoll`, `kqueue`) e a regra crítica: só pode existir **uma instância** monitorando leitura+escrita de tudo, incluindo o listen socket
- `socket-programming.md` — criação do socket, bind, listen, accept
- `event-loop.md` — o loop principal com poll() monitorando leitura e escrita
- `multiple-ports.md` — como ouvir em várias portas ao mesmo tempo
- `funcoes-autorizadas.md` — a lista fechada de funções do Cap. IV, uma a uma: o que cada uma faz e em que parte do servidor ela normalmente entra (ex: `getaddrinfo`/`freeaddrinfo` na resolução de endereço, `setsockopt` para `SO_REUSEADDR`, `waitpid`/`kill`/`signal` no controle do processo filho do CGI)

**03-Arquivo-Configuracao:**
- `config-format.md` — formato geral, inspirado no NGINX
- `listen-directive.md` — interface:porta para ouvir
- `root-directive.md` — mapeamento de URL para diretório do sistema
- `error-pages.md` — configuração de páginas de erro padrão
- `client-max-body-size.md` — limite de tamanho do corpo da requisição (413)
- `location-blocks.md` — regras por URL: métodos aceitos, redirect, root, directory listing, default file, upload, CGI

**04-Request-Handling:**
- `request-parsing.md` — parse da requisição HTTP (linha, headers, body)
- `routing.md` — como mapear URL para arquivo ou regra
- `static-file-serving.md` — servir arquivos estáticos do sistema de arquivos
- `directory-listing.md` — ativar/desativar listagem de diretórios
- `default-file.md` — arquivo padrão para diretórios (ex: index.html)
- `redirecionamento.md` — configurar redirects HTTP (301, 302)
- `file-upload.md` — receber arquivos enviados pelos clientes (POST)

**05-CGI:**
- `cgi-overview.md` — o que é CGI, como funciona a comunicação web server-CGI
- `cgi-environment-variables.md` — variáveis de ambiente passadas para o CGI (visão geral)
- `cgi-request-body.md` — como enviar o corpo da requisição (incluindo EOF)
- `cgi-response.md` — como ler a saída do CGI e encaminhar ao cliente
- `cgi-execution.md` — execução com fork(), diretório correto para path relativo

**06-Erros-e-Resiliencia:**
- `error-handling.md` — códigos de erro e páginas padrão
- `timeouts.md` — evitar travamentos (timeout no poll())
- `keep-alive.md` — manter conexão ativa (Connection: keep-alive)

**07-MacOS-vs-Linux:**
- `fcntl-macos.md` — diferenças no write() e uso de fcntl() com O_NONBLOCK e FD_CLOEXEC

---

## Fase 3: Gerar Notas de Aprofundamento (Essenciais para a Defesa)

Notas extras, nos pontos onde os alunos mais sofrem na avaliação:

1. **`01-HTTP-Protocolo/state-machine-parsing.md`**
   - **Conteúdo:** Como o servidor é não-bloqueante, o `read()` pode receber apenas parte da requisição. Descrever os estados (ex: `READING_START_LINE`, `READING_HEADERS`, `READING_BODY`, `READING_CHUNK_SIZE`, `READING_CHUNK_DATA`, `COMPLETE`). Como armazenar buffers parciais (ex: `std::string` ou `std::vector<char>`) entre ciclos do `poll()` sem perder dados.
   - **Conexões:** [[request-parsing]], [[chunked-transfer-encoding]], [[event-loop]]

2. **`01-HTTP-Protocolo/chunked-transfer-encoding.md`**
   - **Conteúdo:** Formato `[tamanho em hexa]\r\n[dados]\r\n`. Quando o navegador usa isso (sem `Content-Length` prévio). Como o parsing se integra à máquina de estados. Mencionar que o subject não exige explicitamente, mas navegadores modernos usam.
   - **Conexões:** [[state-machine-parsing]], [[http-headers]]

3. **`04-Request-Handling/multipart-form-data.md`**
   - **Conteúdo:** `Content-Type: multipart/form-data; boundary=---`. Estrutura de partes (headers + corpo). Como extrair `name` do campo e `filename` do arquivo. Perigo do boundary cair no meio de um buffer TCP e como lidar (busca por string no buffer acumulado).
   - **Conexões:** [[file-upload]], [[state-machine-parsing]]

4. **`05-CGI/cgi-environment-checklist.md`**
   - **Conteúdo:** Lista exata e obrigatória de variáveis de ambiente que o CGI espera, com fonte de cada uma: `REQUEST_METHOD`, `QUERY_STRING`, `CONTENT_LENGTH`, `CONTENT_TYPE`, `PATH_INFO`, `PATH_TRANSLATED`, `SCRIPT_NAME`, `SERVER_PROTOCOL`, `SERVER_PORT`, `GATEWAY_INTERFACE`, `REDIRECT_STATUS` (obrigatório para PHP-CGI funcionar).
   - **Conexões:** [[cgi-overview]], [[cgi-execution]]

5. **`02-Servidor-HTTP/event-loop-abstraction.md`**
   - **Conteúdo:** Camada de abstração (mental ou em código) para encapsular `poll()`, `select()`, `kqueue()` ou `epoll()`. Diferenças de API: `poll()` usa `struct pollfd`, `epoll` usa `epoll_event`, `kqueue` usa `struct kevent`. Como isso facilita portabilidade.
   - **Conexões:** [[poll-vs-select-vs-kqueue]], [[event-loop]]

6. **`09-Testes-e-Ferramentas/test-strategies.md`**
   - **Conteúdo:** Como usar `telnet` para enviar requisições HTTP brutas (simulando cliente lento). Como usar `curl` para testar GET, POST, DELETE, uploads, headers. Script simples em Python (`socket`) para abrir múltiplas conexões e testar se o `poll()` aguenta. Como testar o limite de `client_max_body_size`.
   - **Conexões:** [[timeouts]], [[error-handling]]

7. **`10-CPP98-Especifico/memory-management.md`**
   - **Conteúdo:** Sem smart pointers modernos em C++98. Uso de `std::vector` como buffer dinâmico. Uso de `std::auto_ptr` (cuidado com cópia) ou containers da STL para objetos clientes. Importância de `delete` no fechamento da conexão. Evitar `new`/`delete` explícitos, delegando à STL.
   - **Conexões:** [[socket-programming]], [[non-blocking-io]]

8. **`00-Index/request-lifecycle-diagram.md`**
   - **Conteúdo:** Fluxo completo de uma requisição HTTP, do socket à resposta, em etapas numeradas:
     1. `socket()` → `bind()` → `listen()` (inicialização)
     2. `poll()` aguarda eventos
     3. Evento no `listen_fd` → `accept()` → adiciona novo `client_fd` ao `poll()`
     4. Evento `POLLIN` no `client_fd` → `read()` → alimenta a máquina de estados ([[state-machine-parsing]])
     5. Requisição completa → roteamento ([[routing]])
     6. GET para arquivo estático → `open()` + `send()` (monitorando `POLLOUT`)
     7. POST com upload → lida com [[multipart-form-data]] e salva arquivo
     8. CGI → `fork()` + `pipe()` + `execve()` + lê saída do CGI ([[cgi-execution]])
     9. Resposta montada → envia com `send()` (monitorando `POLLOUT`)
     10. Decide se mantém conexão (Keep-Alive) ou fecha
   - **Conexões:** TODAS as notas anteriores. Este arquivo é o "fio condutor" do vault.

---

## Fase 4: Gerar MOC e Glossário

- `00-Index/MOC-Webserv.md` — índice com links para todas as notas (incluindo as de aprofundamento), organizado por etapas do pipeline.
- `GLOSSARY.md` — definições curtas de termos técnicos (CGI, poll, socket, chunked, multipart, boundary, non-blocking, etc.).

---

## Fase 5: Gerar Flashcards (Checklist para Entrevista)

- `08-Checklist-Entrevista/perguntas-provaveis-e-respostas.md` com perguntas baseadas nos gaps de conhecimento.
- As perguntas devem cobrir tanto o básico (ex: "Qual a diferença entre GET e POST?") quanto tópicos avançados (ex: "Como você lidaria com uma requisição chunked em um servidor não-bloqueante?" ou "O que acontece se o CGI demorar 10 segundos para responder?").

---

## Fase 6: Notas de Entrega do Projeto (não é conteúdo técnico, é checklist de submissão)

- **`11-Entrega-Projeto/readme-checklist.md`** — Requisitos exatos do Cap. V:
  - Primeira linha em itálico: "Este projeto foi criado como parte do currículo da 42 por [login1], [login2]..."
  - Seção "Descrição" — objetivo e visão geral
  - Seção "Instruções" — compilação, instalação, execução
  - Seção "Recursos" — referências clássicas do tópico **+ descrição de como a IA foi usada** (para quais tarefas, quais partes do projeto — isso é exigido explicitamente, não opcional)
  - Idioma: inglês recomendado, ou idioma principal do campus
- **`11-Entrega-Projeto/avaliacao-por-pares.md`** — Cap. VII: durante a defesa pode ser pedida uma modificação rápida e ao vivo do projeto (pequena mudança de comportamento, função nova, ajuste de estrutura de dados) para validar compreensão real. Reforça a regra 1 do `AGENT.md`: nunca aceitar código que você não entende, porque pode ser testado na hora.

## Definição de "Pronto"

A pasta `Estudo-Webserv/` contém:

- `00-Index/MOC-Webserv.md` — navegável, com todos os links funcionando.
- **Pelo menos 35 notas-conceito** distribuídas pelas pastas (incluindo as 8 de aprofundamento).
- `GLOSSARY.md` com pelo menos 20 termos.
- `08-Checklist-Entrevista/perguntas-provaveis-e-respostas.md` com pelo menos 25 flashcards.
- Cada nota segue o formato especificado (TL;DR, fonte, callout, autoavaliação).
- Nenhum código C++ completo foi gerado (apenas conceitos e, no máximo, pseudocódigo ilustrativo).
- Todas as afirmações técnicas têm fonte explicitamente citada (subject, RFC, man pages).

**GO.**
