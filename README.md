# Estudo-Webserv

Vault de estudo sobre o projeto **Webserv** da 42 — um servidor HTTP em C++98.

## O que é isso

Notas atômicas interligadas sobre protocolo HTTP, arquitetura de servidores non-blocking, CGI, configuração estilo NGINX, e tudo mais que o subject da 42 exige.

## Como usar

Abra a pasta `Estudo-Webserv/` como um vault no [Obsidian](https://obsidian.md). Comece pelo `00-Index/MOC-Webserv.md`.

## Estrutura

```
00-Index/    → Ponto de entrada, diagrama do ciclo de vida
01-HTTP/     → Protocolo HTTP (request, response, métodos, códigos)
02-Servidor/ → Arquitetura, event loop, poll/kqueue/epoll
03-Config/   → Arquivo de configuração estilo NGINX
04-Request/  → Parsing, roteamento, static files, CGI
05-CGI/      → Common Gateway Interface
06-Erros/    → Tratamento de erros, timeouts
07-MacOS/    → Diferenças de plataforma
08-Entrevista/ → Flashcards para revisão
09-Testes/   → Estratégias de teste
10-CPP98/    → Gerenciamento de memória em C++98
```

## Fontes

- Subject Webserv 42 (versão 24.0)
- RFC 2616 (HTTP/1.1), RFC 3875 (CGI/1.1)
- man pages Linux/macOS
