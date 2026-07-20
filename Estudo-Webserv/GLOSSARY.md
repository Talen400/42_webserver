---
tags: [webserv, glossario, definicoes]
---

# Glossário

**CGI (Common Gateway Interface)**
: Interface que permite ao servidor web executar programas externos e passar a requisição do cliente via variáveis de ambiente e stdin. O programa escreve a resposta no stdout.

**chunked transfer encoding**
: Mecanismo HTTP/1.1 que permite enviar corpo em partes (chunks) sem saber o tamanho total antecipadamente.

**Content-Length**
: Header HTTP que indica o tamanho exato do corpo da mensagem em bytes.

**Content-Type**
: Header HTTP que indica o tipo MIME do recurso (ex: text/html, image/png).

**event loop**
: Estrutura de programação que aguarda e despacha eventos em um loop infinito. No Webserv, o event loop chama poll() repetidamente para processar I/O.

**fd (file descriptor)**
: Número inteiro que o sistema operacional usa para identificar arquivos abertos, sockets, pipes e outros recursos de I/O.

**fork()**
: Chamada de sistema que cria um novo processo (filho) duplicando o processo atual. No Webserv, usada apenas para executar CGI.

**HTTP (Hypertext Transfer Protocol)**
: Protocolo de aplicação para comunicação entre clientes (navegadores) e servidores web.

**I/O não bloqueante (non-blocking I/O)**
: Modo de operação onde read() e write() retornam imediatamente, mesmo sem dados disponíveis, usando EAGAIN/EWOULDBLOCK para指示 que a operação não pôde completar.

**keep-alive**
: Recurso HTTP/1.1 que mantém a conexão TCP aberta para múltiplas requisições, reduzindo overhead.

**MIME type**
: Padrão para classificar tipos de arquivo na internet (ex: text/html, application/json).

**multipart/form-data**
: Formato de codificação de formulários HTML que permite enviar arquivos binários junto com campos de texto.

**path traversal**
: Ataque de segurança onde o cliente usa `../` no URL para acessar arquivos fora do diretório raiz do servidor.

**poll()**
: Chamada de sistema que monitora múltiplos file descriptors para verificar se estão prontos para I/O.

**query string**
: Parte da URL após o `?`, contendo parâmetros no formato `chave=valor&chave2=valor2`.

**redirect**
: Resposta HTTP (301, 302) que instrui o cliente a buscar o recurso em outra URL.

**RFC (Request for Comments)**
: Documento técnico que define padrões da internet, incluindo HTTP (RFC 2616, 7230-7235) e CGI (RFC 3875).

**socket**
: Interface de comunicação bidirecional entre processos, usada para comunicação TCP/IP.

**Status-Line**
: Primeira linha da resposta HTTP contendo a versão, o código de status e a frase (ex: HTTP/1.1 200 OK).

**TCP (Transmission Control Protocol)**
: Protocolo de transporte orientado a conexão que garante entrega confiável e ordenada de dados.

**URI (Uniform Resource Identifier)**
: Identificador de recurso (ex: `/index.html`, `/cgi-bin/script.php?name=value`).

**virtual host**
: Técnica de servir múltiplos sites no mesmo servidor usando o header `Host` para diferenciá-los. Fora do escopo do subject.

**auto_ptr**
: Smart pointer de C++98 que transfere posse na cópia. Propenso a bugs sutis; prefira containers STL.

**boundary**
: Delimitador usado em `multipart/form-data` para separar as partes do formulário. O valor é definido no header `Content-Type`.

**FD_CLOEXEC**
: Flag do `fcntl()` que faz o sistema fechar o file descriptor automaticamente ao chamar `execve()`. Essencial para evitar vazamento de FDs para CGI.

**máquina de estados (state machine)**
: Padrão de parser que percorre estados sequenciais (ex: REQUEST_LINE → HEADERS → BODY) e acumula dados parciais entre chamadas de `read()`.

**O_NONBLOCK**
: Flag que coloca um file descriptor em modo não bloqueante: `read()`/`write()` retornam `EAGAIN` em vez de travar.

**RAII (Resource Acquisition Is Initialization)**
: Técnica C++ onde recursos (memória, FDs) são adquiridos no construtor e liberados no destrutor. Garante liberação mesmo com exceções.

**REDIRECT_STATUS**
: Variável de ambiente obrigatória para PHP-CGI. Deve ser setada para `200`. Sem ela, o PHP se recusa a executar.

**SOMAXCONN**
: Constante do sistema que define o tamanho máximo da fila de conexões pendentes do `listen()`. Valor padrão tipicamente 128.
