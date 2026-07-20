---
tags: [webserv, cgi, gerenciamento]
fonte-principal: subject-webserv
status: revisado
---

# CGI — Visão Geral

## TL;DR
CGI (Common Gateway Interface) permite que o servidor execute programas externos (PHP, Python) para gerar conteúdo dinâmico. O servidor passa a requisição via variáveis de ambiente e stdin, e lê a resposta do stdout do programa.

## Contexto no Webserv
O subject exige suporte a CGI, com execução baseada na extensão do arquivo (ex: .php). ✅ `Fonte: subject Webserv 42, IV.3` O servidor deve suportar pelo menos um CGI (php-CGI, Python, etc.). ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
Fluxo de uma requisição CGI:

```
Cliente → Servidor → fork() + execve() → Script CGI
                         ↓                       ↓
                   env vars + stdin         stdout → servidor
                         ↓                       ↓
                   servidor lê saída ← ← ← ← ← ←
                         ↓
                   resposta para cliente
```

1. Servidor identifica que a URL corresponde a um CGI (pela extensão).
2. Prepara variáveis de ambiente.
3. `fork()` + `execve()` o CGI.
4. Envia corpo da requisição via stdin do CGI.
5. Lê stdout do CGI como resposta.
6. Encaminha resposta ao cliente (possivelmente ajustando headers).

> [!note]- 🔍 Aprofundando: regras de comunicação
> - **Requisições multipart:** o servidor precisa desagrupar antes de enviar ao CGI. ✅ `Fonte: subject Webserv 42, IV.3`
> - **EOF como final:** CGI espera EOF para saber que o corpo terminou. O servidor fecha o pipe de escrita. ✅ `Fonte: subject Webserv 42, IV.3`
> - **Saída do CGI:** Se não houver Content-Length, EOF marca o fim. ✅ `Fonte: subject Webserv 42, IV.3`
> - **Diretório correto:** O CGI deve ser executado no diretório correto para acesso a caminhos relativos. ✅ `Fonte: subject Webserv 42, IV.3`
>
> **Conexões:**
> - `Complemento:` [[cgi-environment-variables]]
> - `Complemento:` [[cgi-request-body]]
> - `Complemento:` [[cgi-response]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica qual versão de CGI (RFC 3875).
- FastCGI (versão melhorada do CGI) não é mencionado.
- Apenas um CGI é exigido, mas suportar múltiplos é uma extensão natural.

## Perguntas de autoavaliação
- Como o servidor sabe que um arquivo deve ser executado como CGI vs servido estaticamente?
- O que acontece se o CGI não existir ou não for executável?
- Como lidar com CGI que demora muito para responder?

## Fontes consultadas
- subject Webserv 42, páginas 11-13
- RFC 3875 (CGI 1.1)
