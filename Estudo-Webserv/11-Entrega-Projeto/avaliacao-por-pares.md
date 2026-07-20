---
tags: [webserv, entrega, avaliacao, defesa]
fonte-principal: subject-webserv
status: revisado
---

# Avaliação por Pares

## TL;DR
Na defesa, o avaliador pode pedir uma modificação ao vivo no código — pequena mudança de comportamento, função nova, ajuste de estrutura de dados. O objetivo é verificar se você realmente entende o que escreveu.

## Contexto no Webserv
O Capítulo VII do subject descreve o processo de avaliação. Uma modificação rápida pode ser solicitada para validar compreensão real. ✅ `Fonte: subject Webserv 42, VII`

---

## O Que Esperar

### Modificação ao Vivo
- Pode ser uma mudança pequena de comportamento
- Algumas linhas de código para escrever ou reescrever
- Um recurso fácil de adicionar
- Deve ser viável em poucos minutos

✅ `Fonte: subject Webserv 42, VII`

### Exemplos Possíveis (não exaustivo)
| Tipo | Exemplo |
|------|---------|
| Comportamento | Adicionar um header HTTP personalizado na resposta |
| Função | Implementar um novo método HTTP ou modificar roteamento |
| Estrutura | Ajustar uma estrutura de dados para armazenar nova informação |
| Config | Adicionar suporte a uma nova diretiva no parser de config |

### O Que o Avaliador Busca
- Você entende o fluxo do código, não apenas copiou
- Sabe onde as coisas estão e como se conectam
- Consegue fazer a mudança sem quebrar o resto

> [!note]- 🔍 Aprofundando: como se preparar
> - **Conheça o fluxo completo:** Do `poll()` ao `send()`, passando por parsing, roteamento, CGI. Saber o caminho de uma requisição ([[request-lifecycle-diagram]]) é o mínimo.
> - **Entenda as estruturas de dados:** Onde ficam os clientes, os buffers, os arquivos de configuração. Se o avaliador pedir "adicione um campo X à struct Client", você deve saber onde editá-lo.
> - **Saiba compilar e testar rápido:** Um Makefile limpo e testes manuais com `curl`/`telnet` ajudam a demonstrar que a modificação funciona.
> - **Explique em voz alta:** Enquanto faz a modificação, narre o raciocínio. O avaliador quer ver processo, não só resultado.
> - **Leia o código antigo antes da defesa:** Se usou IA em partes do código, releia e entenda cada linha — o avaliador pode focar exatamente nessas partes (ver [[readme-checklist]] sobre documentação de uso de IA).
>
> **Conexões:**
> - `Complemento:` [[readme-checklist]]
> - `Complemento:` [[request-lifecycle-diagram]]
> - `Complemento:` [[perguntas-provaveis-e-respostas]]

## O que o subject NÃO exige (mas é bom saber)
- A modificação pode ser em qualquer ambiente de desenvolvimento que você escolher (sua configuração usual).
- O escopo e alvo da modificação são especificados nas diretrizes de avaliação e podem variar de uma avaliação para outra.
- Esta etapa pode não ser aplicável a todos os projetos, mas para o Webserv é esperada.

## Perguntas de autoavaliação
- Você consegue explicar o fluxo de uma requisição HTTP sem consultar o código?
- Se o avaliador pedir para adicionar um novo método HTTP (ex: PUT), você sabe quais arquivos precisam ser alterados?
- Você entende cada função que seu código chama, inclusive as que foram sugeridas por IA?

## Fontes consultadas
- subject Webserv 42, Capítulo VII (páginas 16-17)
