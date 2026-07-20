---
tags: [webserv, cgi, ambiente, env-vars, checklist]
fonte-principal: rfc-3875
status: revisado
---

# CGI Environment Checklist

## TL;DR
Lista exata de variáveis de ambiente que o CGI espera receber. Faltar alguma dessas quebra o funcionamento de scripts PHP, Python, etc.

## Contexto no Webserv
O subject diz: "Observe atentamente as variáveis de ambiente envolvidas na comunicação servidor web-CGI. A solicitação completa e os argumentos fornecidos pelo cliente devem estar disponíveis para o CGI." ✅ `Fonte: subject Webserv 42, IV.3`

## Lista Obrigatória

| Variável | Fonte | Obrigatória? | Exemplo |
|----------|-------|-------------|---------|
| `GATEWAY_INTERFACE` | RFC 3875 | Sim | `CGI/1.1` |
| `SERVER_PROTOCOL` | RFC 3875 | Sim | `HTTP/1.1` |
| `SERVER_SOFTWARE` | RFC 3875 | Boa prática | `webserv/1.0` |
| `SERVER_NAME` | RFC 3875 | Sim | `localhost` |
| `SERVER_PORT` | RFC 3875 | Sim | `8080` |
| `REQUEST_METHOD` | RFC 3875 | Sim | `GET`, `POST` |
| `PATH_INFO` | RFC 3875 | Sim | `/subdir/file` |
| `PATH_TRANSLATED` | RFC 3875 | Sim | `/var/www/subdir/file` |
| `SCRIPT_NAME` | RFC 3875 | Sim | `/cgi-bin/script.py` |
| `QUERY_STRING` | RFC 3875 | Sim | `name=value&key=val` |
| `CONTENT_TYPE` | RFC 3875 | Se POST | `text/plain` |
| `CONTENT_LENGTH` | RFC 3875 | Se POST | `1234` |
| `REDIRECT_STATUS` | PHP-CGI | ✅ ESSENCIAL | `200` |
| `HTTP_*` | RFC 3875 | Opcional | `HTTP_USER_AGENT` |
| `SCRIPT_FILENAME` | Prática mercado | Para PHP | `/caminho/absoluto/script.php` |

> [!note]- 🔍 Aprofundando: por que cada variável existe
> **`REDIRECT_STATUS`** — **Essencial para PHP-CGI.** Sem ela, o PHP-CGI interpreta a requisição como um redirect interno não processado e se recusa a executar. Deve ser setada para `200` antes do `execve()`.
>
> **`PATH_INFO` vs `PATH_TRANSLATED`:**
> - Exemplo: URL `/cgi-bin/script.py/extra/path` com root `/var/www`
> - `SCRIPT_NAME` = `/cgi-bin/script.py`
> - `PATH_INFO` = `/extra/path`
> - `PATH_TRANSLATED` = `/var/www/extra/path`
>
> **`QUERY_STRING`:** Tudo após `?` na URL. Se não houver, deve ser string vazia (não ausente).
>
> **`CONTENT_LENGTH`:** Deve ser `0` para GET. Ausente apenas se não houver corpo.
>
> **`SERVER_PROTOCOL`:** A versão HTTP que o servidor fala (`HTTP/1.1`), não a versão da requisição. 📚 `Fonte: RFC 3875`
>
> **Conexões:**
> - `Complemento:` [[cgi-overview]]
> - `Complemento:` [[cgi-environment-variables]]
> - `Complemento:` [[cgi-execution]]

## O que o subject NÃO exige (mas é bom saber)
- A lista completa de variáveis vem da RFC 3875, não do subject.
- `SCRIPT_FILENAME` não é parte da RFC 3875, mas é exigida por php-fpm e php-cgi.
- `REDIRECT_STATUS` também não é RFC, mas é exigida pelo PHP-CGI. ⚠️ `Fonte: prática de mercado`
- Variáveis como `REMOTE_ADDR` (IP do cliente) e `REMOTE_HOST` são opcionais.

## Perguntas de autoavaliação
- Qual variável de ambiente é indispensável para PHP-CGI funcionar?
- Qual a diferença entre PATH_INFO e PATH_TRANSLATED?
- O que acontece se CONTENT_TYPE for definido para um GET request?

## Fontes consultadas
- RFC 3875 (CGI 1.1), seção 4.1
- subject Webserv 42, página 12
- Documentação do PHP-CGI
