---
tags: [webserv, funcoes, syscalls, autorizadas]
fonte-principal: subject-webserv
status: revisado
---

# Funções Externas Autorizadas

## TL;DR
Lista fechada de funções permitidas pelo subject (Cap. IV). Nenhuma outra função externa pode ser usada — sem exceções. Agrupadas por categoria de uso no servidor.

## Contexto no Webserv
O subject define exatamente quais funções externas podem ser chamadas. ✅ `Fonte: subject Webserv 42, IV` Usar qualquer função fora desta lista é desclassificatório.

---

## Lista Completa (por categoria)

### Gerenciamento de Processos
| Função | Onde entra no servidor |
|--------|----------------------|
| `fork()` | Apenas para CGI (criar processo filho) |
| `execve()` | Executar o script CGI |
| `waitpid()` | Coletar status do processo filho (CGI) |
| `kill()` | Enviar sinal para o CGI (timeout/forçar fim) |
| `exit()` | Chamado no filho se `execve()` falhar |

### Pipe e Dup
| Função | Onde entra |
|--------|-----------|
| `pipe()` | Criar canal de comunicação entre servidor e CGI |
| `dup()` / `dup2()` | Redirecionar stdin/stdout do processo filho para os pipes |
| `socketpair()` | Alternativa aos pipes (criar par de sockets conectados) |

### Socket e Rede
| Função | Onde entra |
|--------|-----------|
| `socket()` | Criar socket TCP |
| `bind()` | Associar socket a endereço:porta |
| `listen()` | Marcar socket como passivo |
| `accept()` | Aceitar conexão de cliente |
| `connect()` | **Não usado** no servidor (é para clientes) |
| `send()` | Enviar dados para o cliente (socket já conectado) |
| `recv()` | Receber dados do cliente |
| `setsockopt()` | Configurar opções do socket (ex: `SO_REUSEADDR`) |
| `getsockname()` | Obter endereço local do socket (útil para porta dinâmica) |
| `getprotobyname()` | Obter número do protocolo (ex: "tcp") |

### Resolução de Endereço
| Função | Onde entra |
|--------|-----------|
| `getaddrinfo()` | Resolver hostname:porta para `struct sockaddr` |
| `freeaddrinfo()` | Liberar memória alocada por `getaddrinfo()` |
| `gai_strerror()` | Converter erro de `getaddrinfo()` em string legível |
| `htons()` / `htonl()` | Converter short/long para network byte order (big-endian) |
| `ntohs()` / `ntohl()` | Converter short/long de network byte order para host |

### I/O de Arquivos
| Função | Onde entra |
|--------|-----------|
| `open()` | Abrir arquivo estático para servir |
| `close()` | Fechar fd de cliente, arquivo, pipe |
| `read()` | Ler do socket (requisição) ou de arquivo |
| `write()` | Escrever no socket (resposta) ou em arquivo |
| `access()` | Verificar se o arquivo existe e tem permissão de leitura |
| `stat()` | Obter metadados do arquivo (tamanho, tipo, permissões) |
| `opendir()` / `readdir()` / `closedir()` | Listar conteúdo de diretório (autoindex) |
| `chdir()` | Mudar diretório de trabalho antes de executar CGI |

### Controle de FD
| Função | Onde entra |
|--------|-----------|
| `fcntl()` | Configurar `O_NONBLOCK` e `FD_CLOEXEC` nos sockets/pipes |

### Monitoramento de I/O (poll)
| Função | Onde entra |
|--------|-----------|
| `poll()` | Monitorar múltiplos FDs para leitura/escrita |
| `select()` | Alternativa ao `poll()` |
| `epoll_create()` / `epoll_ctl()` / `epoll_wait()` | Alternativa Linux ao `poll()` |
| `kqueue()` / `kevent()` | Alternativa macOS/BSD ao `poll()` |

### Strings e Erro
| Função | Onde entra |
|--------|-----------|
| `strerror()` | Converter errno em string para logging |
| `errno` | Variável global com código do último erro |
| `signal()` | Tratar sinais (ex: evitar processo zumbi com `SIGCHLD`) |

> [!note]- 🔍 Aprofundando: regras de uso
> - **fork() exclusivo para CGI:** Usar `fork()` para qualquer outra finalidade é proibido. ✅ `Fonte: subject Webserv 42, IV.1`
> - **fcntl() no macOS só com F_SETFL, O_NONBLOCK, FD_CLOEXEC:** Nenhuma outra flag é permitida no macOS. ✅ `Fonte: subject Webserv 42, IV.2`
> - **sem verificar errno após read/write:** É proibido consultar `errno` para ajustar o comportamento do servidor depois de `read()`/`recv()`/`write()`/`send()`. ✅ `Fonte: subject Webserv 42, IV.1`
> - **Não pode execve outro servidor web:** `execve()` pode ser usado apenas para CGI. ✅ `Fonte: subject Webserv 42, IV.1`
>
> **Conexões:**
> - `Complemento:` [[socket-programming]]
> - `Complemento:` [[cgi-execution]]
> - `Complemento:` [[fcntl-macos]]
> - `Complemento:` [[non-blocking-io]]

## O que o subject NÃO exige (mas é bom saber)
- `socketpair()` é uma alternativa menos comum a `pipe()`, mas está na lista.
- `connect()` está autorizado mas não tem uso típico num servidor HTTP (servidores não iniciam conexões).
- O subject não proíbe `bzero()`/`memset()` — são funções C padrão, não aparecem na lista porque não são "externas" (vêm da libc padrão).

## Perguntas de autoavaliação
- Quais funções são obrigatórias para configurar um socket não-bloqueante?
- O que acontece se `execve()` for chamado com um caminho que não é um CGI?
- Por que `pipe()` precisa ser chamado antes de `fork()`?

## Fontes consultadas
- subject Webserv 42, página 7 (lista de funções autorizadas)
- subject Webserv 42, páginas 9-12 (regras de uso: fork, fcntl, errno)
