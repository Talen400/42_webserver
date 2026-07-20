---
tags: [webserv, cgi, execucao, fork, execve]
fonte-principal: subject-webserv
status: revisado
---

# Execução do CGI

## TL;DR
O servidor cria um processo filho com `fork()` e executa o script CGI com `execve()`. A comunicação entre servidor e CGI é feita via pipes.

## Contexto no Webserv
O subject permite `fork()` apenas para CGI, não para outras finalidades. ✅ `Fonte: subject Webserv 42, IV.1` O CGI deve ser executado no diretório correto para resolver caminhos relativos. ✅ `Fonte: subject Webserv 42, IV.3`

## Como funciona
```c
// 1. Criar pipes antes do fork
pipe(pipe_stdin);   // servidor → CGI
pipe(pipe_stdout);  // CGI → servidor

// 2. Fork
pid = fork();

if (pid == 0) {
    // Processo filho (CGI)
    // Configurar stdin/stdout/stderr
    dup2(pipe_stdin[0], STDIN_FILENO);
    dup2(pipe_stdout[1], STDOUT_FILENO);

    // Fechar pontas não usadas
    close(pipe_stdin[1]);
    close(pipe_stdout[0]);

    // Mudar para diretório correto (chdir)
    chdir(cgi_dir);

    // Executar CGI
    execve(cgi_path, args, envp);
    exit(1);  // se execve falhar
}

// 3. Processo pai (servidor)
close(pipe_stdin[0]);   // fecha leitura do stdin pipe
close(pipe_stdout[1]);  // fecha escrita do stdout pipe

// servidor escreve corpo no pipe_stdin[1]
// servidor lê resposta do pipe_stdout[0]
// servidor espera filho com waitpid()
```

> [!note]- 🔍 Aprofundando: considerações importantes
> - **Diretório correto:** O subject exige que o CGI seja executado no diretório correto. Use `chdir()` antes do `execve()`.
> - **envp:** Array de variáveis de ambiente CGI (veja [[cgi-environment-variables]]).
> - **Non-blocking pipes:** Os pipes de comunicação devem ser configurados como non-blocking para evitar deadlock.
> - **Timeout:** CGI que demora muito deve ser morto (`kill()`) para evitar conexões pendentes.
> - **waitpid():** Use `WNOHANG` para não bloquear. Ou espere em outro momento.
>
> **Conexões:**
> - `Complemento:` [[cgi-overview]]
> - `Complemento:` [[cgi-environment-variables]]
> - `Complemento:` [[cgi-response]]
> - `Complemento:` [[timeouts]]

## O que o subject NÃO exige (mas é bom saber)
- O subject não especifica timeout para CGI. ⚠️ não verificado
- `execve()` requer o caminho completo do executável.
- `chdir()` é necessário para scripts que usam caminhos relativos (ex: includes do PHP).
- CGIs mal comportados que não leem stdin podem causar deadlock (pipe enche).

## Perguntas de autoavaliação
- Por que os pipes precisam ser criados antes do fork()?
- O que acontece se execve() falhar?
- Como o servidor sabe que o CGI terminou?

## Fontes consultadas
- subject Webserv 42, páginas 7-13
- man fork(2), man execve(2), man pipe(2), man waitpid(2)
