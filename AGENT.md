# AGENT.md

## Contexto do Projeto

Estou estudando o projeto **Webserv** da 42 — um servidor HTTP implementado em C++98. Este é um projeto clássico da 42 que cobre:

- Programação em C++98 (sem bibliotecas externas, sem Boost)
- Protocolo HTTP (RFCs 1945/2616)
- I/O não-bloqueante com `poll()` (ou `select()`/`kqueue()`/`epoll()`)
- Parse de arquivos de configuração (estilo NGINX)
- CGI (Common Gateway Interface)
- Sistema de arquivos e permissões

O objetivo final é transformar esse estudo em uma **árvore de conhecimento no Obsidian**: notas atômicas, interligadas por `[[wikilinks]]`, organizadas por tema, que eu possa navegar e revisar para entender profundamente o que um servidor HTTP faz e como implementá-lo.

**Aviso importante:** Este projeto é 100% meu. Não estou copiando código de ninguém — estou extraindo **conceitos e padrões de decisão** do subject, dos RFCs e da documentação.

---

## Regras Gerais (sempre válidas, qualquer tarefa)

1. **Nunca gere código-fonte em C++ completo.** As notas são sobre **conceitos, arquitetura e fluxo de dados**, não sobre implementação específica do meu código. O objetivo é entender *o que* fazer e *por que*, não gerar código pronto.

2. **Não presuma conteúdo que não foi lido de verdade.** Antes de descrever qualquer requisito do subject (ex: "o arquivo de configuração deve aceitar listen, root, etc."), verifique no PDF real. Se algo não estiver explicitamente no subject, marque como `⚠️ não verificado`.

3. **Cite fontes explicitamente:** toda afirmação técnica deve vir com uma das tags:
   - `Fonte: subject Webserv 42` — requisito explícito no PDF
   - `Fonte: RFC HTTP/1.1` — especificação oficial do protocolo
   - `Fonte: documentação C++98` — comportamento da linguagem/biblioteca padrão
   - `Fonte: prática de mercado` — quando NGINX ou servidores comerciais fazem X
   - `⚠️ não verificado` — se não puder ser verificado por nenhuma das anteriores

4. **Separe "o que o subject exige" de "como NGINX faz".** O subject explicitamente diz para comparar com NGINX, mas isso não significa que tudo que NGINX faz é obrigatório. Cada nota deve deixar claro:
   - **Obrigatório** (exigido pelo subject)
   - **Comparativo** (como NGINX faz, para referência)
   - **Opcional/Bônus** (se o subject menciona como extra)

5. **Marque incerteza explicitamente.** Se uma afirmação não puder ser verificada por leitura real do subject ou por documentação oficial, marque como `⚠️ não verificado`.

6. **Escreva em camadas.** Toda nota-conceito deve ter um resumo direto primeiro (o que é, pra que serve, sem jargão) e só depois o aprofundamento técnico.

7. **Sinalize o nível de confiança com moderação:**
   - `✅` — confirmado lendo o subject/PDF
   - `📚` — confirmado pela documentação oficial da tecnologia (RFC, cppreference)
   - `⚠️` — não verificado / suposição a validar

8. **Nada de emoji decorativo fora da legenda de confiança**, com uma única exceção: o emoji fixo `🔍` no título do callout de aprofundamento: `[!note]- 🔍 Aprofundando: ...`

9. **Use callout nativo do Obsidian (`> [!note]-`) para blocos recolhíveis**, nunca `<details>`/`<summary>` em HTML puro.

---

## Fontes de Verificação (nesta ordem de prioridade)

1. **O subject do Webserv (PDF fornecido)** — requisitos explícitos e obrigatórios
2. **RFCs do HTTP** (1.0 e 1.1) — comportamento do protocolo
   - RFC 1945 (HTTP/1.0)
   - RFC 2616 (HTTP/1.1)
   - RFC 7230-7235 (HTTP/1.1 revisado)
3. **Documentação oficial de C++98** — `poll()`, `socket()`, `fcntl()`, etc.
4. **Documentação do NGINX** — para comportamento de referência
5. **Man pages do Linux/macOS** — para syscalls específicos

---

## Estrutura do Vault Obsidian

Organizar a saída como uma vault, não como um único arquivo grande. Estrutura de pastas:

```
Estudo-Webserv/
├── 00-Index/
│   ├── MOC-Webserv.md                     ← Map of Content, ponto de entrada
│   └── request-lifecycle-diagram.md       ← ⭐ NOVO: Fluxo completo da requisição
├── 01-HTTP-Protocolo/
│   ├── http-request-format.md
│   ├── http-response-format.md
│   ├── http-methods.md                    ← GET, POST, DELETE
│   ├── http-status-codes.md
│   ├── http-headers.md
│   ├── http-versions.md                   ← HTTP/1.0 vs HTTP/1.1
│   ├── state-machine-parsing.md           ← ⭐ NOVO: Máquina de estados para parsing
│   └── chunked-transfer-encoding.md       ← ⭐ NOVO: Como lidar com chunked
├── 02-Servidor-HTTP/
│   ├── arquitetura-servidor-http.md       ← visão geral: o que faz um servidor
│   ├── non-blocking-io.md                 ← por que e como
│   ├── poll-vs-select-vs-kqueue.md        ← comparação das chamadas de sistema
│   ├── socket-programming.md              ← bind, listen, accept
│   ├── event-loop.md                      ← o loop principal do servidor
│   ├── event-loop-abstraction.md          ← ⭐ NOVO: Abstração para trocar poll/select/kqueue
│   └── multiple-ports.md                  ← ouvir em várias portas
├── 03-Arquivo-Configuracao/
│   ├── config-format.md                   ← estrutura geral
│   ├── listen-directive.md                ← interface:porta
│   ├── root-directive.md                  ← diretório base para arquivos
│   ├── error-pages.md                     ← páginas de erro personalizadas
│   ├── client-max-body-size.md            ← limite de tamanho do corpo
│   ├── location-blocks.md                 ← regras por URL/rota
│   └── config-examples.md                 ← exemplos de arquivos .conf
├── 04-Request-Handling/
│   ├── request-parsing.md                 ← parse da requisição HTTP
│   ├── routing.md                         ← como rotear para o local certo
│   ├── static-file-serving.md             ← servir arquivos HTML/CSS/JS
│   ├── directory-listing.md               ← listar diretórios (on/off)
│   ├── default-file.md                    ← index.html, etc.
│   ├── redirecionamento.md                ← HTTP redirect
│   ├── file-upload.md                     ← POST com envio de arquivo
│   └── multipart-form-data.md             ← ⭐ NOVO: Parse do boundary e campos
├── 05-CGI/
│   ├── cgi-overview.md                    ← o que é CGI e pra que serve
│   ├── cgi-environment-variables.md       ← variáveis de ambiente (visão geral)
│   ├── cgi-environment-checklist.md       ← ⭐ NOVO: Lista exata e obrigatória para a 42
│   ├── cgi-request-body.md                ← como enviar corpo para CGI
│   ├── cgi-response.md                    ← como ler saída do CGI
│   └── cgi-execution.md                   ← fork, pipes, diretório correto
├── 06-Erros-e-Resiliencia/
│   ├── error-handling.md                  ← códigos de erro e páginas
│   ├── timeouts.md                        ← evitar travamentos
│   └── keep-alive.md                      ← manter conexão ativa
├── 07-MacOS-vs-Linux/
│   ├── fcntl-macos.md                     ← diferenças no write()
│   └── non-blocking-macos.md              ← O_NONBLOCK e FD_CLOEXEC
├── 08-Checklist-Entrevista/
│   └── perguntas-provaveis-e-respostas.md
├── 09-Testes-e-Ferramentas/               ← ⭐ NOVA PASTA
│   └── test-strategies.md                 ← telnet, Python socket, stress tests
├── 10-CPP98-Especifico/                   ← ⭐ NOVA PASTA
│   └── memory-management.md               ← RAII, containers, evitar vazamentos
└── GLOSSARY.md
```

---

## Tarefa Atual: Extrair do Subject para o Vault

### Fase 1: Leitura e Mapeamento Base

Leia o subject do Webserv (PDF fornecido) e extraia:

1. **Requisitos obrigatórios**: tudo que está na seção "Parte obrigatória" (Capítulo IV)
2. **Regras gerais**: Capítulo II
3. **Bônus**: Capítulo VI
4. **Comportamentos especiais**: MacOS-specific (IV.2), arquivo de configuração (IV.3)

---

### Fase 2: Gerar Notas Base (Obrigatórias)

Com base na leitura, gere as seguintes notas. Cada uma deve seguir o formato padrão (TL;DR, Contexto no Webserv, Funcionamento, Callout de Aprofundamento, Perguntas de autoavaliação, Fontes).

**01-HTTP-Protocolo:**
- `http-request-format.md` — formato da requisição (linha de requisição, headers, corpo)
- `http-response-format.md` — formato da resposta (linha de status, headers, corpo)
- `http-methods.md` — GET, POST, DELETE (o que cada um faz e quando usar)
- `http-status-codes.md` — códigos mais importantes (200, 301, 400, 404, 405, 413, 500, etc.)
- `http-headers.md` — headers relevantes (Host, Content-Length, Content-Type, Connection, Transfer-Encoding)
- `http-versions.md` — diferenças entre HTTP/1.0 e 1.1 (Keep-Alive, Host obrigatório)

**02-Servidor-HTTP:**
- `arquitetura-servidor-http.md` — visão geral do que o servidor faz
- `non-blocking-io.md` — o que é, por que o subject exige, como funciona com poll()
- `poll-vs-select-vs-kqueue.md` — comparação das opções (subject permite qualquer uma)
- `socket-programming.md` — criação do socket, bind, listen, accept
- `event-loop.md` — o loop principal com poll() monitorando leitura e escrita
- `multiple-ports.md` — como ouvir em várias portas ao mesmo tempo

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

### Fase 3: Gerar Notas de Aprofundamento (⭐ NOVO - Essenciais para a Defesa)

O agente DEVE gerar as seguintes notas extras, que são os pontos onde os alunos mais sofrem na avaliação:

1. **`01-HTTP-Protocolo/state-machine-parsing.md`**
   - **O que deve conter:** Explicar que, como o servidor é não-bloqueante, o `read()` pode receber apenas parte da requisição. Descrever os estados (ex: `READING_START_LINE`, `READING_HEADERS`, `READING_BODY`, `READING_CHUNK_SIZE`, `READING_CHUNK_DATA`, `COMPLETE`). Explicar como armazenar buffers parciais (ex: `std::string` ou `std::vector<char>`) entre um ciclo e outro do `poll()` para não perder dados.
   - **Conexões:** [[request-parsing]], [[chunked-transfer-encoding]], [[event-loop]].

2. **`01-HTTP-Protocolo/chunked-transfer-encoding.md`**
   - **O que deve conter:** Explicar o formato `[tamanho em hexa]\r\n[dados]\r\n`. Mostrar como o navegador usa isso quando não sabe o `Content-Length` de antemão. Descrever como o parsing deve se integrar à máquina de estados (estados de chunk). Mencionar que o subject não exige explicitamente, mas navegadores modernos usam.
   - **Conexões:** [[state-machine-parsing]], [[http-headers]].

3. **`04-Request-Handling/multipart-form-data.md`**
   - **O que deve conter:** Explicar o `Content-Type: multipart/form-data; boundary=---`. Descrever a estrutura de partes (headers + corpo). Mostrar como extrair o `name` do campo e o `filename` do arquivo. Alertar sobre o perigo do boundary cair no meio de um buffer TCP — como lidar com isso (busca por string no buffer acumulado).
   - **Conexões:** [[file-upload]], [[state-machine-parsing]].

4. **`05-CGI/cgi-environment-checklist.md`**
   - **O que deve conter:** Lista exata e obrigatória de variáveis de ambiente que o CGI espera receber, com a fonte de cada uma:
     - `REQUEST_METHOD` (do verbo HTTP)
     - `QUERY_STRING` (tudo após `?` na URL)
     - `CONTENT_LENGTH` (do header)
     - `CONTENT_TYPE` (do header)
     - `PATH_INFO` (caminho extra após o script)
     - `PATH_TRANSLATED` (caminho físico no sistema)
     - `SCRIPT_NAME` (nome do script)
     - `SERVER_PROTOCOL` (HTTP/1.1 ou 1.0)
     - `SERVER_PORT` (porta ouvida)
     - `GATEWAY_INTERFACE` (CGI/1.1)
     - `REDIRECT_STATUS` (obrigatório para PHP-CGI funcionar)
   - **Conexões:** [[cgi-overview]], [[cgi-execution]].

5. **`02-Servidor-HTTP/event-loop-abstraction.md`**
   - **O que deve conter:** Descrever como criar uma camada de abstração (mental ou em código) para encapsular `poll()`, `select()`, `kqueue()` ou `epoll()`. Mostrar as diferenças de API: `poll()` usa `struct pollfd`, `epoll` usa `epoll_event`, `kqueue` usa `struct kevent`. Explicar como isso facilita a portabilidade (já que o subject aceita qualquer um).
   - **Conexões:** [[poll-vs-select-vs-kqueue]], [[event-loop]].

6. **`09-Testes-e-Ferramentas/test-strategies.md`**
   - **O que deve conter:** Estratégias práticas para testar o servidor sem interface gráfica:
     - Como usar `telnet` para enviar requisições HTTP brutas, linha por linha (simulando cliente lento).
     - Como usar `curl` para testar GET, POST, DELETE, uploads, headers.
     - Script simples em Python (usando `socket`) para abrir múltiplas conexões simultâneas e testar se o `poll()` aguenta (stress test).
     - Como testar o limite de `client_max_body_size` enviando um arquivo grande.
   - **Conexões:** [[timeouts]], [[error-handling]].

7. **`10-CPP98-Especifico/memory-management.md`**
   - **O que deve conter:** Em C++98 não temos smart pointers modernos. Abordar como gerenciar a memória dos clientes, buffers e arquivos sem vazar:
     - Uso de `std::vector` como buffer dinâmico.
     - Uso de `std::auto_ptr` (cuidado com a cópia) ou containers da STL para armazenar objetos clientes.
     - Importância de `delete` no fechamento da conexão (destrutor da classe Client).
     - Evitar `new`/`delete` explícitos sempre que possível, delegando à STL.
   - **Conexões:** [[socket-programming]], [[non-blocking-io]].

8. **`00-Index/request-lifecycle-diagram.md`**
   - **O que deve conter:** Descrever em texto o fluxo completo de uma requisição HTTP, do socket à resposta, em etapas numeradas:
     1. `socket()` → `bind()` → `listen()` (inicialização).
     2. `poll()` aguarda eventos.
     3. Evento no `listen_fd` → `accept()` → adiciona novo `client_fd` ao `poll()`.
     4. Evento `POLLIN` no `client_fd` → `read()` → alimenta a máquina de estados ([[state-machine-parsing]]).
     5. Requisição completa → roteamento ([[routing]]).
     6. Se GET para arquivo estático → `open()` + `send()` (usando `poll()` para `POLLOUT`).
     7. Se POST com upload → lida com [[multipart-form-data]] e salva arquivo.
     8. Se CGI → `fork()` + `pipe()` + `execve()` + lê saída do CGI ([[cgi-execution]]).
     9. Resposta montada → envia com `send()` (monitorando `POLLOUT`).
     10. Decide se mantém conexão (Keep-Alive) ou fecha.
   - **Conexões:** TODAS as notas anteriores. Este arquivo serve como o "fio condutor" do vault.

---

### Fase 4: Gerar MOC e Glossário

- `MOC-Webserv.md` — índice com links para todas as notas (incluindo as novas), organizado por etapas do pipeline.
- `GLOSSARY.md` — definições curtas de termos técnicos (ex: CGI, poll, socket, chunked, multipart, boundary, non-blocking, etc.).

---

### Fase 5: Gerar Flashcards (Checklist para Entrevista)

- `08-Checklist-Entrevista/perguntas-provaveis-e-respostas.md` com perguntas baseadas nos gaps de conhecimento.
- **Importante:** As perguntas devem cobrir tanto o básico (ex: "Qual a diferença entre GET e POST?") quanto os tópicos avançados (ex: "Como você lidaria com uma requisição chunked em um servidor não-bloqueante?" ou "O que acontece se o CGI demorar 10 segundos para responder?").

---

## Definição de "Pronto"

A pasta `Estudo-Webserv/` contém:

- `00-Index/MOC-Webserv.md` — navegável, com todos os links funcionando.
- **Pelo menos 35 notas-conceito** distribuídas pelas pastas (incluindo as 8 novas obrigatórias).
- `GLOSSARY.md` com pelo menos 20 termos.
- `08-Checklist-Entrevista/perguntas-provaveis-e-respostas.md` com pelo menos 25 flashcards.
- Cada nota segue o formato especificado (TL;DR, fonte, callout, autoavaliação).
- Nenhum código C++ completo foi gerado (apenas conceitos e, no máximo, pseudocódigo ilustrativo em markdown).
- Todas as afirmações técnicas têm fonte explicitamente citada (subject, RFC, man pages).

**GO.**