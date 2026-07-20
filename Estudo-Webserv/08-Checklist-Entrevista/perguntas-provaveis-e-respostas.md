#flashcards

O que significa um servidor HTTP ser "non-blocking" e por que o subject exige isso?
?
Ver [[non-blocking-io]] e [[event-loop]]. Escrever aqui minha própria resposta.

---

O que o subject exige sobre o uso de poll()? Quantos podem ser usados?
?
Ver [[event-loop]] e [[non-blocking-io]]. Escrever aqui minha própria resposta.

---

Qual a diferença entre HTTP/1.0 e HTTP/1.1 no contexto do Webserv?
?
Ver [[http-versions]]. Escrever aqui minha própria resposta.

---

Quais métodos HTTP o servidor Webserv deve implementar?
?
Ver [[http-methods]]. Escrever aqui minha própria resposta.

---

Explique o formato de uma requisição HTTP (request line, headers, body).
?
Ver [[http-request-format]]. Escrever aqui minha própria resposta.

---

O que cada código de status HTTP significa no contexto do projeto (200, 204, 301, 400, 403, 404, 405, 413, 500)?
?
Ver [[http-status-codes]]. Escrever aqui minha própria resposta.

---

Como funciona a comunicação entre servidor e CGI?
?
Ver [[cgi-overview]] e [[cgi-execution]]. Escrever aqui minha própria resposta.

---

Quais variáveis de ambiente o servidor deve passar para o CGI?
?
Ver [[cgi-environment-variables]]. Escrever aqui minha própria resposta.

---

Como o servidor lida com o corpo da requisição para CGI (stdin, EOF)?
?
Ver [[cgi-request-body]]. Escrever aqui minha própria resposta.

---

Como o servidor lê a saída do CGI e a encaminha ao cliente?
?
Ver [[cgi-response]]. Escrever aqui minha própria resposta.

---

Explique o fluxo do event loop com poll().
?
Ver [[event-loop]]. Escrever aqui minha própria resposta.

---

Qual a diferença entre poll(), select(), epoll() e kqueue()?
?
Ver [[poll-vs-select-vs-kqueue]]. Escrever aqui minha própria resposta.

---

Como o servidor determina qual location block usar para uma requisição?
?
Ver [[routing]] e [[location-blocks]]. Escrever aqui minha própria resposta.

---

O que é a diretiva client_max_body_size e como o servidor deve reagir quando o limite é excedido?
?
Ver [[client-max-body-size]]. Escrever aqui minha própria resposta.

---

Como funciona o redirecionamento HTTP no arquivo de configuração?
?
Ver [[redirecionamento]]. Escrever aqui minha própria resposta.

---

O que é directory listing (autoindex) e quando ele é usado?
?
Ver [[directory-listing]] e [[default-file]]. Escrever aqui minha própria resposta.

---

Quais funções de fcntl() são permitidas no macOS e por quê?
?
Ver [[fcntl-macos]]. Escrever aqui minha própria resposta.

---

Para que o subject permite o uso de fork()?
?
Ver [[cgi-execution]]. Escrever aqui minha própria resposta. (Resposta: apenas para CGI)

---

O que acontece se o servidor encontrar uma requisição com método não implementado?
?
Ver [[http-status-codes]] e [[http-methods]]. Escrever aqui minha própria resposta. (Resposta: 501 Not Implemented)

---

Como o servidor deve tratar páginas de erro?
?
Ver [[error-pages]] e [[error-handling]]. Escrever aqui minha própria resposta.

---

O que a diretiva "root" faz no arquivo de configuração?
?
Ver [[root-directive]]. Escrever aqui minha própria resposta.

---

O que significa "resiliência" no contexto do Webserv?
?
Ver [[error-handling]] e subject IV.3. Escrever aqui minha própria resposta.

---

Qual a importância do header Content-Type ao servir arquivos estáticos?
?
Ver [[http-headers]] e [[static-file-serving]]. Escrever aqui minha própria resposta.

---

Como o servidor deve lidar com timeouts?
?
Ver [[timeouts]]. Escrever aqui minha própria resposta.

---

O que o subject diz sobre verificar errno após read/write?
?
Ver [[non-blocking-io]]. Escrever aqui minha própria resposta. (Resposta: é proibido)

---

Explique como funciona a máquina de estados do parsing em um servidor non-blocking.
?
Ver [[state-machine-parsing]] e [[request-parsing]]. Escrever aqui minha própria resposta.

---

O que é chunked transfer encoding e como integrá-lo à máquina de estados?
?
Ver [[chunked-transfer-encoding]] e [[state-machine-parsing]]. Escrever aqui minha própria resposta.

---

Como fazer o parsing de uma requisição multipart/form-data? Como extrair o filename do upload?
?
Ver [[multipart-form-data]] e [[file-upload]]. Escrever aqui minha própria resposta.

---

Qual variável de ambiente é indispensável para PHP-CGI funcionar e por quê?
?
Ver [[cgi-environment-checklist]]. Escrever aqui minha própria resposta. (Resposta: REDIRECT_STATUS)

---

Qual a diferença entre PATH_INFO e PATH_TRANSLATED?
?
Ver [[cgi-environment-checklist]] e [[cgi-environment-variables]]. Escrever aqui minha própria resposta.

---

Como criar uma abstração de event loop que funcione com poll(), epoll() e kqueue()?
?
Ver [[event-loop-abstraction]] e [[poll-vs-select-vs-kqueue]]. Escrever aqui minha própria resposta.

---

Como testar o servidor sem um navegador? Quais ferramentas usar para simular cliente lento e stress test?
?
Ver [[test-strategies]]. Escrever aqui minha própria resposta.

---

Como gerenciar memória em C++98 sem smart pointers modernos?
?
Ver [[memory-management]]. Escrever aqui minha própria resposta.

---

Descreva o fluxo completo de uma requisição HTTP, do socket à resposta, em 5 etapas.
?
Ver [[request-lifecycle-diagram]]. Escrever aqui minha própria resposta.

---

O que é FD_CLOEXEC e por que é importante em um servidor que usa CGI?
?
Ver [[fcntl-macos]] e [[non-blocking-macos]]. Escrever aqui minha própria resposta.

---

O que é o padrão RAII e como ele se aplica ao gerenciamento de sockets no Webserv?
?
Ver [[memory-management]] e [[socket-programming]]. Escrever aqui minha própria resposta.

---

O que fazer se um buffer TCP trouxer apenas metade do \r\n (ex: só \r)?
?
Ver [[state-machine-parsing]]. Escrever aqui minha própria resposta.

---

Como proteger o servidor contra path traversal (../etc/passwd)?
?
Ver [[root-directive]] e [[static-file-serving]]. Escrever aqui minha própria resposta.

---

O que acontece se o CGI não retornar Content-Length?
?
Ver [[cgi-response]] e [[cgi-request-body]]. Escrever aqui minha própria resposta.
