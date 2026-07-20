# DIR.md

> Esta árvore descreve a pasta `Estudo-Webserv/`, que fica na **raiz do vault**, ao lado de `_agent/` (onde estão este arquivo e os outros 4). Não gerar notas dentro de `_agent/` — é só plano de controle.

Estrutura de pastas do vault `Estudo-Webserv/`. Organizar a saída como um vault, não como um único arquivo grande.

**Layout geral do repositório/vault:**
```
raiz/
├── _agent/                  ← AGENT.md, DIR.md, REFERENCES.md, TASKS.md, PROGRESS.md (arquivos de controle, não são notas de estudo)
├── Estudo-Webserv/          ← o vault em si, descrito abaixo
└── pt_br_subject.pdf        ← o subject original, para consulta
```

Árvore de `Estudo-Webserv/`:

```
Estudo-Webserv/
├── 00-Index/
│   ├── MOC-Webserv.md                     ← Map of Content, ponto de entrada
│   └── request-lifecycle-diagram.md       ← Fluxo completo da requisição
├── 01-HTTP-Protocolo/
│   ├── http-request-format.md
│   ├── http-response-format.md
│   ├── http-methods.md                    ← GET, POST, DELETE
│   ├── http-status-codes.md
│   ├── http-headers.md
│   ├── http-versions.md                   ← HTTP/1.0 vs HTTP/1.1
│   ├── state-machine-parsing.md           ← Máquina de estados para parsing
│   └── chunked-transfer-encoding.md       ← Como lidar com chunked
├── 02-Servidor-HTTP/
│   ├── arquitetura-servidor-http.md       ← visão geral: o que faz um servidor
│   ├── non-blocking-io.md                 ← por que e como
│   ├── poll-vs-select-vs-kqueue.md        ← comparação das chamadas de sistema
│   ├── socket-programming.md              ← bind, listen, accept
│   ├── event-loop.md                      ← o loop principal do servidor
│   ├── event-loop-abstraction.md          ← Abstração para trocar poll/select/kqueue
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
│   └── multipart-form-data.md             ← Parse do boundary e campos
├── 05-CGI/
│   ├── cgi-overview.md                    ← o que é CGI e pra que serve
│   ├── cgi-environment-variables.md       ← variáveis de ambiente (visão geral)
│   ├── cgi-environment-checklist.md       ← Lista exata e obrigatória para a 42
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
├── 02-Servidor-HTTP/ (adicionar)
│   └── funcoes-autorizadas.md             ← Lista fechada de funções (Cap. IV), o que cada uma faz
├── 08-Checklist-Entrevista/
│   └── perguntas-provaveis-e-respostas.md
├── 09-Testes-e-Ferramentas/
│   └── test-strategies.md                 ← telnet, Python socket, stress tests
├── 10-CPP98-Especifico/
│   └── memory-management.md               ← RAII, containers, evitar vazamentos
├── 11-Entrega-Projeto/
│   ├── readme-checklist.md                ← Requisitos do README.md do projeto (Cap. V)
│   └── avaliacao-por-pares.md             ← O que esperar na defesa (Cap. VII): modificação ao vivo
└── GLOSSARY.md
```

Se uma nova nota for necessária e não couber em nenhuma pasta existente, proponha a nova pasta/arquivo aqui antes de criar — não crie estrutura ad-hoc sem registrar.
