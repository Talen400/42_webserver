---
tags: [webserv, cpp98, memoria, raii, containers]
fonte-principal: documentacao-cpp98
status: revisado
---

# Gerenciamento de Memória em C++98

## TL;DR
Em C++98 não há smart pointers modernos (unique_ptr, shared_ptr). O gerenciamento de memória depende de containers STL, `std::auto_ptr` (com cuidado) e destruição explícita no fechamento das conexões.

## Contexto no Webserv
O subject exige C++98 e proíbe bibliotecas externas (inclusive Boost). ✅ `Fonte: subject Webserv 42, II` Isso significa sem `std::unique_ptr`, `std::shared_ptr`, `std::unordered_map`, etc.

## Como funciona
### Containers STL como gerenciadores de memória
A regra de ouro: **prefira containers STL a `new[]`/`delete[]` explícitos.**

| Container | Uso no Webserv |
|-----------|---------------|
| `std::string` | Buffer de parsing, headers, paths |
| `std::vector<char>` | Buffer binário para corpo de requisição/resposta |
| `std::map<std::string, std::string>` | Headers (chave → valor) |
| `std::list` / `std::vector<Client>` | Pool de clientes conectados |
| `std::pair` | Pares de dados diversos |

### auto_ptr — use com extrema cautela
`std::auto_ptr` transfere posse na cópia (não é cópia real). Isso causa bugs sutis:

```cpp
std::auto_ptr<Foo> p1(new Foo);
std::auto_ptr<Foo> p2 = p1; // p1 agora é NULL!
```

Prefira armazenar objetos por valor em containers ou usar ponteiros crus gerenciados manualmente com `new`/`delete` bem definidos.

### RAII com classes C++
Cada recurso deve ser liberado no destrutor:

```cpp
class ClientConnection {
    int socket_fd;
    std::string buffer;
public:
    ClientConnection(int fd) : socket_fd(fd) {}
    ~ClientConnection() {
        if (socket_fd >= 0) close(socket_fd);
    }
};
```

> [!note]- 🔍 Aprofundando: armadilhas comuns de memória
> **1. Vazamento de FDs:**
> Socket não fechado → "Too many open files". Use RAII: o destrutor da classe `Client` fecha o fd.
>
> **2. Vazamento de buffers:**
> Corpo de requisição acumulado em buffer. Se o cliente desconectar, limpe o buffer imediatamente.
>
> **3. Uso de `new` sem `delete`:**
> Ao invés de `new Client()`, armazene `Client` por valor em um `std::vector<Client>`. Se for estritamente necessário usar ponteiros, use `std::list<Client*>` e delete ao remover.
>
> **4. Exceções e vazamentos:**
> C++98 não tem `noexcept`. Se uma exceção for lançada durante o processamento, objetos na pilha são destruídos (stack unwinding), mas ponteiros crus vazam. Use RAII para garantir liberação mesmo com exceções.
>
> **5. Uso de memset/memcpy:**
> O subject permite funções C, mas prefira versões C++ (`std::fill`, `std::copy` com iterators) quando possível.
>
> **Conexões:**
> - `Complemento:` [[socket-programming]]
> - `Complemento:` [[non-blocking-io]]
> - `Complemento:` [[event-loop]]

## O que o subject NÃO exige (mas é bom saber)
- O subject permite funções C, mas "sempre prefira suas versões C++ se possível". ✅ `Fonte: subject Webserv 42, II`
- `std::auto_ptr` foi deprecado em C++11 e removido em C++17. Use com cautela.
- `std::vector` garante memória contígua — útil para enviar buffers com `send()` e `write()`.
- `std::map` tem busca O(log n). Para headers, que são poucos, é suficiente.

## Perguntas de autoavaliação
- Como garantir que um socket seja fechado mesmo se uma exceção for lançada?
- Por que `std::vector` é preferível a `new char[]` para buffers?
- Qual o problema de usar `std::auto_ptr` em um container STL?

## Fontes consultadas
- subject Webserv 42, páginas 3, 7
- Documentação C++98: containers STL, auto_ptr
