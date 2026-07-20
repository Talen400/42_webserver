---
tags: [webserv, parsing, state-machine, non-blocking]
fonte-principal: subject-webserv
status: revisado
---

# Máquina de Estados para Parsing

## TL;DR
Como o servidor é não bloqueante, `read()` pode receber a requisição em partes. O parser opera como uma máquina de estados: cada pedaço lido avança a máquina até a requisição estar completa.

## Contexto no Webserv
O subject exige que o servidor nunca leia sem passar pelo `poll()`. Como cada `read()` pode trazer 1 byte ou 1KB, o parser precisa lembrar onde parou entre chamadas. ✅ `Fonte: subject Webserv 42, IV.1`

## Como funciona
A máquina de estados percorre:

```
REQUEST_LINE → HEADERS → BODY (se houver) → COMPLETE
```

**Estados detalhados:**
| Estado | O que faz | Saída |
|--------|-----------|-------|
| `REQUEST_LINE` | Acumula bytes até `\r\n` | Method, URI, Version |
| `HEADERS` | Acumula linhas até `\r\n\r\n` | Mapa header → valor |
| `BODY` | Lê N bytes (Content-Length) | Body bruto |
| `COMPLETE` | Requisição pronta para processar | — |

Cada estado pode acumular dados parciais em buffers internos (ex: `std::string` ou `std::vector<char>`) entre ciclos do `poll()`.

> [!note]- 🔍 Aprofundando: buffers parciais e retomada
> **Problema:** O `read()` retorna, por exemplo, apenas `"GET /index"`. Falta o resto da request-line.
>
> **Solução:** Um buffer parcial armazena o que já foi lido. Quando `read()` for chamado de novo (no próximo ciclo do `poll()`), o parser continua de onde parou:
>
> ```
> Buffer atual:  "GET /index"
> Novos bytes:   ".html HTTP/1.1\r\nHost: "
> Buffer após:   "GET /index.html HTTP/1.1\r\nHost: "
>                 ^-- agora encontra \r\n, processa request-line
> ```
>
> **Implementação:**
> - Usar `std::string` ou `std::vector<char>` como buffer de acumulação.
> - Cada estado tem sua lógica de busca (`find("\r\n")`, `substr()`, etc.).
> - A máquina não avança para o próximo estado até que o estado atual esteja completo.
>
> **O parser deve ser stateless nos dados mas stateful no progresso:**
> - O código do parser não mantém estado global.
> - Cada conexão tem sua própria instância do parser (com seu buffer e estado atual).
>
> **Conexões:**
> - `Complemento:` [[request-parsing]]
> - `Complemento:` [[chunked-transfer-encoding]]
> - `Complemento:` [[event-loop]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica como implementar o parser. Máquina de estados é a abordagem mais comum.
- Um bug comum: esquecer de tratar o caso onde `\r\n` fica dividido entre dois `read()` (`\r` em um, `\n` no outro).
- A máquina de estados para chunked encoding adiciona os estados `CHUNK_SIZE`, `CHUNK_DATA`, `CHUNK_END`.

## Perguntas de autoavaliação
- O que acontece se o `read()` retornar apenas `\r` sem o `\n`?
- Como evitar que um cliente malicioso envie headers infinitos?
- A máquina de estados deve ser resetada para keep-alive? Como?

## Fontes consultadas
- subject Webserv 42, páginas 7-12
- RFC 2616, seções 4-5
