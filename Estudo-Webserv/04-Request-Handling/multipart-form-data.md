---
tags: [webserv, multipart, upload, form-data, boundary]
fonte-principal: rfc-1867
status: revisado
---

# Multipart Form Data

## TL;DR
`multipart/form-data` é o formato usado por navegadores para enviar formulários que incluem arquivos. O corpo da requisição é dividido em partes separadas por um `boundary`, cada parte podendo ter seus próprios headers e conteúdo.

## Contexto no Webserv
O subject afirma que "para solicitações em partes, seu servidor precisa desagrupá-las". ✅ `Fonte: subject Webserv 42, IV.3` Isso se aplica ao receber uploads de arquivos via POST e ao encaminhar dados para CGI.

## Como funciona
Header da requisição:
```
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary
```

Corpo:
```
------WebKitFormBoundary\r\n
Content-Disposition: form-data; name="username"\r\n
\r\n
joao\r\n
------WebKitFormBoundary\r\n
Content-Disposition: form-data; name="file"; filename="foto.jpg"\r\n
Content-Type: image/jpeg\r\n
\r\n
...dados binários da foto...\r\n
------WebKitFormBoundary--\r\n
```

Cada parte é delimitada por `--boundary`. A última parte termina com `--boundary--`.

> [!note]- 🔍 Aprofundando: parsing do multipart
> **Desafio principal:** O `boundary` pode aparecer no meio de um buffer TCP. A solução é acumular dados e buscar pelo delimitador.
>
> **Etapas do parsing:**
> 1. Extrair o `boundary` do header `Content-Type`.
> 2. Escanear o buffer acumulado procurando pelo `--boundary`.
> 3. Para cada parte, ler os headers da parte (ex: `Content-Disposition`, `Content-Type`).
> 4. Extrair `name` (nome do campo) e `filename` (nome do arquivo, se presente) do header `Content-Disposition`.
> 5. Ler o corpo da parte até encontrar o próximo `--boundary`.
> 6. Se for `--boundary--` (último), o parsing terminou.
>
> **⚠️ Atenção ao spoofing de boundary:** Um cliente malicioso pode injetar `--boundary` dentro dos dados. O parser deve garantir que o boundary esteja no início da linha (após `\r\n`).
>
> **Conexões:**
> - `Complemento:` [[file-upload]]
> - `Complemento:` [[state-machine-parsing]]
> - `Complemento:` [[cgi-request-body]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica o formato multipart em detalhes. A documentação vem de RFC 1867.
- Nem toda requisição POST é multipart — pode ser `application/x-www-form-urlencoded` ou `application/octet-stream`.
- O boundary é escolhido pelo cliente (navegador). O servidor apenas usa o valor declarado no header.

## Perguntas de autoavaliação
- Como extrair o nome do arquivo de uma parte multipart?
- O que fazer se o boundary aparecer dentro dos dados binários de um arquivo?
- Como o parser diferencia entre partes de campos de texto vs. partes de arquivos?

## Fontes consultadas
- RFC 1867 (Form-based File Upload in HTML)
- RFC 2616, seção 19.3
- subject Webserv 42, página 12
