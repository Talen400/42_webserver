---
tags: [webserv, upload, post, arquivo]
fonte-principal: subject-webserv
status: revisado
---

# File Upload

## TL;DR
O servidor pode receber arquivos enviados por clientes via POST. O arquivo é salvo no diretório configurado em `upload_store`.

## Contexto no Webserv
O subject exige que "os clientes devem ser capazes de enviar arquivos" e que o armazenamento seja configurado por location. ✅ `Fonte: subject Webserv 42, IV.1, IV.3`

## Como funciona
```
location /upload {
    methods      POST;
    upload_store /var/uploads;
}
```

Cliente envia:
```
POST /upload HTTP/1.1
Content-Type: application/octet-stream
Content-Length: 12345

(conteúdo binário do arquivo)
```

Ou multipart (formulário HTML):
```
POST /upload HTTP/1.1
Content-Type: multipart/form-data; boundary=----WebKitBoundary

------WebKitBoundary
Content-Disposition: form-data; name="file"; filename="foto.jpg"
Content-Type: image/jpeg

(conteúdo)
------WebKitBoundary--
```

> [!note]- 🔍 Aprofundando: salvando o arquivo
> O servidor deve:
> 1. Ler o corpo da requisição (Content-Length ou chunked).
> 2. Extrair o nome do arquivo (do header Content-Disposition em multipart, ou usar nome gerado).
> 3. Escrever o conteúdo em `upload_store + / + nome_arquivo`.
> 4. Retornar 201 Created ou 204 No Content.
>
> **Multipart:** o subject menciona que o servidor precisa desagrupar requisições em partes. ✅ `Fonte: subject Webserv 42, IV.3`
>
> **Conexões:**
> - `Complemento:` [[request-parsing]]
> - `Complemento:` [[http-methods]]
> - `Complemento:` [[client-max-body-size]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica como nomear arquivos (nome original vs gerado). ⚠️ não verificado
- Sobrescrita de arquivos existentes não é mencionada.
- Validação de tipo de arquivo (Content-Type) não é exigida.
- Multipart/form-data parsing é complexo e pode ser necessário para CGI.

## Perguntas de autoavaliação
- Como extrair o nome do arquivo de uma requisição multipart?
- O que fazer se o arquivo enviado exceder client_max_body_size?
- Como garantir que o nome do arquivo não cause path traversal (`../../etc/passwd`)?

## Fontes consultadas
- subject Webserv 42, páginas 9, 11-12
- RFC 1867 (form-based file upload)
