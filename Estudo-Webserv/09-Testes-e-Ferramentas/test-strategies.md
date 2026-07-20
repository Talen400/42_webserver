---
tags: [webserv, testes, telnet, curl, stress, python]
fonte-principal: subject-webserv
status: revisado
---

# Estratégias de Teste

## TL;DR
Testar um servidor HTTP manualmente é inviável para todos os cenários. Use `telnet` para requisições brutas, `curl` para testes de alto nível, e scripts Python para stress test.

## Contexto no Webserv
O subject diz: "Por favor, leia os RFCs que definem o protocolo HTTP e execute testes com telnet e NGINX antes de começar este projeto." ✅ `Fonte: subject Webserv 42, página 8` E também: "Não teste com apenas um programa. Escreva seus testes em uma linguagem mais adequada, como Python ou Golang." ✅ `Fonte: subject Webserv 42, página 13`

## Como funciona
### 1. Telnet — Requisições HTTP brutas
Telnet permite enviar requisições HTTP manualmente, linha por linha, simulando cliente lento:

```bash
telnet 127.0.0.1 8080
GET / HTTP/1.1
Host: localhost

```

- Útil para testar parsing linha a linha.
- Pode-se digitar lentamente para testar buffers parciais.
- `Ctrl+]` + `quit` para sair.

### 2. Curl — Testes de alto nível
```bash
curl -v http://127.0.0.1:8080/           # GET verboso
curl -X POST -d "dados" http://...       # POST com corpo
curl -X DELETE http://...                 # DELETE
curl -F "file=@foto.jpg" http://...      # Upload multipart
curl --limit-rate 100 http://...         # Cliente lento
```

- `-v` mostra headers de requisição e resposta completos.
- `--limit-rate` simula banda limitada (testa non-blocking write).

### 3. Python — Stress test
```python
import socket, threading

def client():
    s = socket.socket()
    s.connect(("127.0.0.1", 8080))
    s.send(b"GET / HTTP/1.1\r\nHost: localhost\r\n\r\n")
    data = s.recv(4096)
    s.close()

threads = []
for _ in range(100):
    t = threading.Thread(target=client)
    t.start()
    threads.append(t)
for t in threads:
    t.join()
```

### 4. NGINX — Referência de comportamento
```bash
# Instale o NGINX, configure com as mesmas regras e compare:
curl -v http://localhost:8080/test
curl -v http://localhost:80/test      # NGINX na porta 80
```

Compare headers, códigos de status, corpo da resposta.

> [!note]- 🔍 Aprofundando: cenários críticos de teste
> **Testes de resiliência:**
> - Cliente que conecta e não envia nada (timeout).
> - Cliente que envia headers intermináveis (proteção contra DoS).
> - Requisição com `Content-Length` irreal (ex: 999999999).
> - Múltiplos `accept()` sem `read()` — socket de listen pode acumular.
>
> **Testes de CGI:**
> - CGI que não existe (`execve` falha → 500).
> - CGI que demora muito (timeout).
> - CGI que escreve resposta sem headers (deve rejeitar).
>
> **Conexões:**
> - `Complemento:` [[timeouts]]
> - `Complemento:` [[error-handling]]
> - `Complemento:` [[non-blocking-io]]

## O que o subject NÃO exige (mas é bom saber)
- O subject fornece um "pequeno testador" mas diz que usá-lo não é obrigatório. ✅ `Fonte: subject Webserv 42, página 12`
- Testar com navegador real (Firefox, Chrome) é uma forma de validação final.
- Ferramentas como `wrk` ou `ab` (Apache Bench) podem testar concorrência.

## Perguntas de autoavaliação
- Como testar se o servidor está tratando requisições parciais corretamente?
- Como simular um cliente que envia dados muito devagar?
- O que acontece se 100 clientes tentarem se conectar ao mesmo tempo?

## Fontes consultadas
- subject Webserv 42, páginas 8, 12-13
- man telnet(1), man curl(1)
