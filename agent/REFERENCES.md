# REFERENCES.md

Fontes de verificação, nesta ordem de prioridade. Toda afirmação técnica nas notas deve rastrear até uma destas (ver tags de fonte em `AGENT.md`, regra 3).

1. **O subject do Webserv (PDF fornecido, versão 24.0)** — requisitos explícitos e obrigatórios. Estrutura confirmada por leitura real:
   - Cap. I — Introdução (contexto geral do HTTP, não normativo)
   - Cap. II — Regras gerais (Makefile, flags de compilação, C++98, proibição de libs externas/Boost)
   - Cap. III — Instruções para IA (não é requisito técnico do servidor; é sobre uso ético de IA no projeto — relevante para a seção "Recursos" do README, não para as notas técnicas)
   - Cap. IV — Parte obrigatória (núcleo técnico)
     - IV.1 Requisitos (poll único, não-bloqueante, métodos GET/POST/DELETE, múltiplas portas)
     - IV.2 Apenas para MacOS (fcntl com flags restritas)
     - IV.3 Arquivo de configuração (listen, error pages, client_max_body_size, location blocks, CGI)
   - Cap. V — Requisitos do Readme (entregável do projeto, não das notas de estudo)
   - Cap. VI — Parte bônus (cookies/sessão, múltiplos CGIs — só avaliada se a obrigatória estiver 100%)
   - Cap. VII — Entrega e avaliação por pares (pode haver modificação ao vivo do código na defesa)

2. **RFCs do HTTP** — comportamento do protocolo.
   - **RFC 9110 — HTTP Semantics** (fonte atual e consolidada; obsoleta o RFC 7230-7235/7231/7232/7233/7235) — https://www.rfc-editor.org/rfc/rfc9110.html
   - **RFC 9112 — HTTP/1.1** (sintaxe da mensagem, substitui a parte de "wire format" do RFC 7230 — é a mais relevante pro parser) — https://www.rfc-editor.org/rfc/rfc9112.html
   - RFC 1945 (HTTP/1.0) — histórico, ainda vale para comparar com HTTP/1.1
   - RFC 7230-7235 — mantidos como referência histórica; nas notas, citar o RFC 9110/9112 como fonte primária, não os obsoletos
   - MDN (Mozilla) como leitura de apoio/didática do protocolo — não substitui o RFC como fonte primária, usar tag `Fonte: RFC HTTP/1.1` mesmo quando a redação partiu da MDN
     - https://developer.mozilla.org/pt-BR/docs/Web/HTTP/Guides/Overview

3. **RFC do CGI** — fonte primária para tudo que for `05-CGI/`, principalmente `cgi-environment-checklist.md`.
   - **RFC 3875 — The Common Gateway Interface (CGI) Version 1.1** — https://www.rfc-editor.org/rfc/rfc3875

4. **RFC do multipart/form-data** — fonte primária para `multipart-form-data.md` e `file-upload.md`.
   - **RFC 7578 — Returning Values from Forms: multipart/form-data** (obsoleta o RFC 2388 — usar sempre o 7578) — https://www.rfc-editor.org/rfc/rfc7578.html

5. **Documentação oficial de C++98** — containers, `std::string`, `std::vector`, etc.
   - cppreference.com — atenção: o site documenta todas as versões do C++; ao usar como fonte, verificar a tag "(since C++98)"/"(until C++11)" em cada página antes de citar, senão vira `⚠️ não verificado`

6. **Documentação do NGINX** — para comportamento de referência (comparativo, não obrigatório — ver `AGENT.md` regra 4).
   - https://github.com/nginx/nginx

7. **Man pages do Linux/macOS** — para syscalls específicos.
   - `poll(2)`, `select(2)`, `accept(2)`, `bind(2)`, `listen(2)`, `fcntl(2)` — https://man7.org (Linux) e `man` local no macOS para `kqueue(2)`/`kevent(2)`, que não existem no man7
   - **Beej's Guide to Network Programming** — não é fonte normativa, mas é a referência didática mais usada da comunidade C para sockets/poll; tag `Fonte: prática de mercado` — https://beej.us/guide/bgnet/

8. **Referências de design de parser** — usadas apenas para decidir *como estruturar* o parser (máquina de estados, camadas), nunca para copiar código. Tag de fonte: `Fonte: prática de mercado`.
   - **llhttp** (parser HTTP usado pelo Node.js, baseado em máquina de estados) — https://llhttp.org/
   - **Apache httpd** (para comparar abordagem de parsing com o NGINX) — https://github.com/apache/httpd
   - **Finite-state machine (conceito geral)** — https://en.wikipedia.org/wiki/Finite-state_machine
   - Nota de design já decidida: **não usar o padrão Composite** para o parser. Se quiser justificar/comparar padrões alternativos (State, Strategy) nas notas, marcar como `Fonte: prática de mercado` e deixar claro que é decisão de design, não exigência do subject.

Se uma afirmação não se sustenta em nenhuma dessas fontes, ela é `⚠️ não verificado` — não usar fontes 6, 7 ou 8 para "inventar" obrigatoriedade que só a fonte 1 pode confirmar.
