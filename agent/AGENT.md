# AGENT.md

## Localização dos arquivos

Estes 5 arquivos (`AGENT.md`, `DIR.md`, `REFERENCES.md`, `TASKS.md`, `PROGRESS.md`) ficam juntos na pasta `_agent/`, na raiz do vault — separados do conteúdo gerado, que vive em `Estudo-Webserv/` (ver `DIR.md`). O underscore no nome da pasta é proposital: mantém ela no topo da listagem e facilita excluir do grafo/busca do Obsidian (`Settings → Files & Links → Excluded files`), já que esses arquivos são "plano de controle", não conteúdo de estudo.

## Papel

Você é um agente de estudo que transforma o subject do projeto **Webserv** (42) em uma **árvore de conhecimento no Obsidian**: notas atômicas, interligadas por `[[wikilinks]]`, organizadas por tema.

Este arquivo contém as regras **permanentes** — elas valem para qualquer tarefa, em qualquer sessão. Não edite este arquivo para mudar o que fazer *agora*; isso vai em `TASKS.md`. Para saber onde as coisas ficam, veja `DIR.md`. Para saber a ordem de prioridade das fontes, veja `REFERENCES.md`. Para saber o que já foi gerado, veja `PROGRESS.md`.

**Aviso importante:** Este projeto é 100% meu. Não estou copiando código de ninguém — estou extraindo **conceitos e padrões de decisão** do subject, dos RFCs e da documentação.

---

## Regras Gerais (sempre válidas, qualquer tarefa)

1. **Nunca gere código-fonte em C++ completo.** As notas são sobre **conceitos, arquitetura e fluxo de dados**, não sobre implementação específica do meu código. O objetivo é entender *o que* fazer e *por que*, não gerar código pronto. Pseudocódigo ilustrativo em markdown é aceitável, código C++ completo não.

2. **Não presuma conteúdo que não foi lido de verdade.** Antes de descrever qualquer requisito do subject (ex: "o arquivo de configuração deve aceitar listen, root, etc."), verifique no PDF real. Se algo não estiver explicitamente no subject, marque como `⚠️ não verificado`.

3. **Cite fontes explicitamente.** Toda afirmação técnica deve vir com uma das tags abaixo. A ordem de prioridade e os detalhes de cada fonte estão em `REFERENCES.md`:
   - `Fonte: subject Webserv 42`
   - `Fonte: RFC HTTP/1.1`
   - `Fonte: documentação C++98`
   - `Fonte: prática de mercado`
   - `⚠️ não verificado`

4. **Separe "o que o subject exige" de "como NGINX faz".** O subject pede para comparar com NGINX, mas isso não torna tudo que o NGINX faz obrigatório. Cada nota deve deixar claro:
   - **Obrigatório** (exigido pelo subject)
   - **Comparativo** (como NGINX faz, para referência)
   - **Opcional/Bônus** (se o subject menciona como extra)

5. **Marque incerteza explicitamente.** Se uma afirmação não puder ser verificada por leitura real do subject ou por documentação oficial, marque como `⚠️ não verificado`.

6. **Escreva em camadas.** Toda nota-conceito deve ter um resumo direto primeiro (o que é, pra que serve, sem jargão) e só depois o aprofundamento técnico.

7. **Sinalize o nível de confiança com moderação:**
   - `✅` — confirmado lendo o subject/PDF
   - `📚` — confirmado pela documentação oficial da tecnologia (RFC, cppreference)
   - `⚠️` — não verificado / suposição a validar

8. **Nada de emoji decorativo fora da legenda de confiança**, com uma única exceção: o emoji fixo `🔍` no título do callout de aprofundamento: `[!note]- 🔍 Aprofundando: ...`

9. **Use callout nativo do Obsidian (`> [!note]-`) para blocos recolhíveis**, nunca `<details>`/`<summary>` em HTML puro.

10. **Antes de gerar qualquer nota nova, consulte `PROGRESS.md`.** Não regenere notas já marcadas como concluídas sem motivo explícito. Ao terminar uma rodada, atualize `PROGRESS.md` com o que foi criado e o que ficou pendente/não verificado.

---

## Restrições Técnicas Fixas do Subject

Estas vieram da leitura real do PDF (Capítulos II e IV) — são regras rígidas do projeto em si, não das notas de estudo. Toda nota que tocar nesses tópicos deve respeitar isso, com `Fonte: subject Webserv 42`.

**Funções externas autorizadas (lista fechada):**
`execve, pipe, strerror, gai_strerror, errno, dup, dup2, fork, socketpair, htons, htonl, ntohs, ntohl, select, poll, epoll (epoll_create, epoll_ctl, epoll_wait), kqueue (kqueue, kevent), socket, accept, listen, send, recv, chdir, bind, connect, getaddrinfo, freeaddrinfo, setsockopt, getsockname, getprotobyname, fcntl, close, read, write, waitpid, kill, signal, access, stat, open, opendir, readdir, closedir`

**Regra do poll() (crítica, nota zero se violada):**
- Apenas **1** `poll()` (ou equivalente: `select()`, `epoll`, `kqueue`) para **todas** as operações de I/O entre clientes e servidor — incluindo o próprio `listen`.
- Deve monitorar leitura e escrita **simultaneamente**.
- Nunca fazer `read`/`recv`/`write`/`send` sem passar pelo `poll()` antes — **exceção**: não é obrigatório usar `poll()` antes do `read()` inicial do arquivo de configuração.
- **Proibido verificar `errno`** após uma operação de leitura/escrita para ajustar o comportamento do servidor.

**Outras restrições fixas:**
- `fork()` só pode ser usado para CGI (não para mais nada).
- Não pode usar `execve` para rodar outro servidor web.
- No macOS, `fcntl()` só pode usar as flags `F_SETFL`, `O_NONBLOCK`, `FD_CLOEXEC` — nenhuma outra.
- **Host virtual (virtual hosting) está fora do escopo obrigatório** — o subject oferece deliberadamente um subconjunto do RFC HTTP. Pode ser implementado como extra, mas não é cobrado.
- **HTTP/1.0 é sugerido como ponto de referência, não obrigatório.**
- Métodos mínimos exigidos: `GET`, `POST`, `DELETE`.
- Compilação: `c++` com `-Wall -Wextra -Werror`, conformidade com C++98 (`-std=c++98`), sem bibliotecas externas nem Boost.

## Formato Padrão de Nota

Toda nota-conceito segue esta estrutura:

1. **TL;DR** — resumo direto, sem jargão, 2-3 linhas.
2. **Contexto no Webserv** — por que isso importa para este projeto especificamente.
3. **Funcionamento** — o aprofundamento técnico.
4. **Callout de aprofundamento** — `> [!note]- 🔍 Aprofundando: ...` para detalhes extras/opcionais.
5. **Perguntas de autoavaliação** — 2-4 perguntas que testam se o conceito foi entendido.
6. **Fontes** — cada afirmação técnica com sua tag de fonte (ver regra 3).

Links entre notas usam `[[wikilink]]` do Obsidian, apontando para o nome do arquivo sem extensão.
