---
tags: [webserv, entrega, readme, checklist]
fonte-principal: subject-webserv
status: revisado
---

# README Checklist

## TL;DR
O README.md na raiz do repositório não é opcional — o subject exige um formato específico com 3 seções obrigatórias, primeira linha em itálico, e menção obrigatória do uso de IA.

## Contexto no Webserv
O Capítulo V do subject define exatamente o que o README deve conter. É um entregável obrigatório, avaliado na defesa. ✅ `Fonte: subject Webserv 42, V`

---

## Requisitos Obrigatórios

### 1. Primeira linha (itálico)
```
*Este projeto foi criado como parte do currículo da 42 por <login1>[, <login2>[, <login3>[...]]].*
```
✅ `Fonte: subject Webserv 42, V`

### 2. Seção "Descrição"
Apresentar claramente:
- Objetivo do projeto
- Breve visão geral do que o servidor faz

✅ `Fonte: subject Webserv 42, V`

### 3. Seção "Instruções"
Informações sobre:
- Compilação (Makefile)
- Instalação (se aplicável)
- Execução (`./webserv [arquivo de configuração]`)

✅ `Fonte: subject Webserv 42, V`

### 4. Seção "Recursos"
- Referências clássicas relacionadas ao tópico (documentação, artigos, tutoriais)
- **Descrição obrigatória de como a IA foi usada** — especificar para quais tarefas e quais partes do projeto

✅ `Fonte: subject Webserv 42, V`

### 5. Idioma
Inglês é recomendado; alternativamente, o idioma principal do campus.

✅ `Fonte: subject Webserv 42, V`

---

## Template Mínimo

```markdown
*Este projeto foi criado como parte do currículo da 42 por [login].*

## Descrição
[objetivo e visão geral do servidor HTTP]

## Instruções
Compilação: `make`
Execução: `./webserv [config.conf]`

## Recursos
- RFC 9110/9112 — HTTP Semantics e HTTP/1.1
- Beej's Guide to Network Programming
- [outras referências]

### Uso de IA
A IA foi utilizada para: [listar tarefas, ex: revisão de conceitos, estruturação de notas de estudo, debugging de [tópico específico]].
```

> [!note]- 🔍 Aprofundando: por que a IA é obrigatória no README
> O Capítulo III (Instruções para IA) e o Capítulo V (Requisitos do Readme) se conectam: o subject quer que você documente **como** usou IA — não para punir, mas para demonstrar que você entende o que foi gerado e consegue explicar. É também uma forma de o avaliador saber o que perguntar na defesa.
>
> **Boas práticas:**
> - Seja específico: "usei IA para estruturar o parser como máquina de estados" > "usei IA para me ajudar"
> - Nunca copie código gerado sem entender — o avaliador pode pedir uma modificação ao vivo (ver [[avaliacao-por-pares]])
>
> **Conexões:**
> - `Complemento:` [[avaliacao-por-pares]]
> - `Complemento:` [[perguntas-provaveis-e-respostas]]

## O que o subject NÃO exige (mas é bom saber)
- Seções adicionais podem ser necessárias dependendo do projeto (ex: exemplos de uso, lista de recursos, escolhas técnicas). ✅ `Fonte: subject Webserv 42, V`
- O README não precisa ser longo, mas deve ser informativo para alguém não familiarizado com o projeto.

## Perguntas de autoavaliação
- O README do seu repositório atende a todos os 4 requisitos obrigatórios?
- Você documentou como usou IA? Sabe explicar o que cada parte gerada faz?
- Seu README está em inglês (ou no idioma do seu campus)?

## Fontes consultadas
- subject Webserv 42, Capítulo V (páginas 14-15)
