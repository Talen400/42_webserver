---
tags: [webserv, config, exemplos, nginx]
fonte-principal: subject-webserv
status: revisado
---

# Exemplos de Arquivos de Configuração

## TL;DR
Exemplos reais de arquivos `.conf` para o Webserv, demonstrando diferentes cenários: servidor simples, múltiplas portas, CGI, upload, redirect.

## Contexto no Webserv
O subject exige: "Você deve fornecer arquivos de configuração e arquivos padrão para testar e demonstrar que todos os recursos funcionam durante a avaliação." ✅ `Fonte: subject Webserv 42, página 12`

## Exemplos

### 1. Servidor Mínimo
```nginx
server {
    listen 127.0.0.1:8080;
    root /var/www/html;
}
```

### 2. Múltiplas Portas
```nginx
server {
    listen 127.0.0.1:8080;
    root /var/www/site1;
}
server {
    listen 127.0.0.1:8081;
    root /var/www/site2;
}
```

### 3. Com Upload e CGI
```nginx
server {
    listen 0.0.0.0:80;
    root /var/www/html;
    client_max_body_size 100M;
    error_page 404 /error_pages/404.html;
    error_page 500 /error_pages/500.html;

    location / {
        methods GET;
        index index.html;
        autoindex off;
    }

    location /upload {
        methods POST;
        upload_store /var/www/uploads;
        client_max_body_size 200M;
    }

    location /cgi-bin {
        methods GET POST;
        cgi_ext .php;
        root /var/www/cgi;
        index index.php;
    }

    location /redirect-old {
        return 301 /new-path;
    }

    location /images {
        root /var/www/html;
        autoindex on;
    }
}
```

### 4. Servidor com Python CGI
```nginx
server {
    listen 8080;
    root /var/www;

    location / {
        methods GET;
        index index.html;
    }

    location /py {
        methods GET POST;
        cgi_ext .py;
        root /var/www/scripts;
    }
}
```

> [!note]- 🔍 Aprofundando: validação do arquivo de configuração
> **O parser deve validar:**
> - Todo `{` tem `}` correspondente.
> - Nomes de diretiva são conhecidos (ou ignorados se opcionais).
> - Valores têm tipo correto (porta é número, path é string, etc.).
> - `listen` está presente em cada server block.
>
> **Comportamento em caso de config inválido:** O subject não especifica. ⚠️ não verificado Boa prática: imprimir erro e sair (como NGINX faz).
>
> **Conexões:**
> - `Complemento:` [[config-format]]
> - `Complemento:` [[listen-directive]]
> - `Complemento:` [[location-blocks]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica sintaxe exata.
- NGINX permite valores default implícitos (ex: `index index.html`).
- O parser não precisa suportar herança de diretivas (location herdar valores do server), mas é desejável.

## Perguntas de autoavaliação
- O que acontece se o arquivo de configuração não existir?
- Um server block sem diretiva listen deve ser ignorado ou causar erro?
- Como o parser trata diretivas desconhecidas?

## Fontes consultadas
- subject Webserv 42, páginas 11-13
- Documentação do NGINX (para inspiração de formato)
