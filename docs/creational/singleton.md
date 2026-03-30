# 🧍 Singleton

## 🧭 Guia rápido

- [📌 Visão geral](#-visão-geral)
- [🎯 Caso de uso](#-caso-de-uso)
- [💡 Solução](#-solução)
- [🧱 Implementação](#-implementação)
- [🧪 Uso](#-uso)
- [🎯 Benefícios](#-benefícios)
- [⚠️ Pontos de atenção](#-pontos-de-atenção)
- [📝 Conclusão](#-conclusão)

## 📌 Visão geral

O **Singleton** é um padrão de projeto criacional que garante que uma classe tenha **apenas uma única instância** durante todo o ciclo de vida da aplicação, fornecendo um **ponto global de acesso** a ela.

Em vez de criar novos objetos toda vez que precisar de um serviço, você reutiliza a mesma instância que já foi criada na primeira vez.

## 🎯 Caso de uso

### O Cenário
Imagine que você precisa de um sistema de **Logger** (registro de logs) que será chamado em dezenas de arquivos diferentes da sua aplicação para registrar erros e informações.

### Implementação inicial

```csharp
public class Logger
{
    public void Log(string message) => Console.WriteLine($"[LOG] {message}");
}

// Uso espalhado pelo sistema:
var logger1 = new Logger();
logger1.Log("Iniciando módulo de vendas...");

var logger2 = new Logger();
logger2.Log("Erro ao conectar no banco de dados.");
```

### Por que isso é um problema?
* **Desperdício de memória:** Cada vez que você dá um `new Logger()`, o sistema aloca memória para um novo objeto, o que é totalmente desnecessário para um serviço que faz sempre a mesma coisa.
* **Falta de controle centralizado:** Cada instância é independente. Se o logger precisasse travar um arquivo físico de texto para escrever nele, múltiplas instâncias tentariam acessar o mesmo arquivo ao mesmo tempo, causando erros (concorrência).
* **Configurações duplicadas:** Se o Logger tiver configurações (ex: nível de log, destino), você teria que configurar cada nova instância separadamente.

## 💡 Solução

A solução do Singleton é bloquear a criação livre de novos objetos (escondendo o construtor) e forçar todas as partes do sistema a pedirem a instância diretamente para a própria classe. Se a instância já existir, ela é devolvida; se não, ela é criada na hora e depois devolvida.

### Estrutura conceitual
* **Construtor Privado:** Impede que outras classes usem o operador `new`.
* **Campo Estático (Private):** Armazena a única instância criada da classe.
* **Propriedade/Método Estático (Public):** O ponto de acesso global que retorna a instância armazenada.

## 🧱 Implementação

### A Classe Singleton

```csharp
public class Logger
{
    // 1. Campo estático e privado para guardar a instância única
    private static Logger _instance;

    // 2. Construtor privado para impedir o uso de 'new Logger()' fora desta classe
    private Logger() 
    {
        // Inicializações, abertura de arquivos, etc.
    }

    // 3. Propriedade estática pública que fornece acesso global
    public static Logger Instance 
    {
        get 
        {
            // Se a instância ainda não existe, cria. Se já existe, apenas retorna. (Lazy Initialization)
            return _instance ??= new Logger();
        }
    }

    // 4. Os métodos normais da classe
    public void Log(string message) => Console.WriteLine($"[LOG] {message}");
}
```

## 🧪 Uso

O uso se torna muito mais direto. Você não precisa instanciar ou passar a variável adiante, basta chamar a classe de qualquer lugar:

```csharp
// Em algum lugar no início do sistema
Logger.Instance.Log("Iniciando sistema...");

// Em uma classe completamente diferente, horas depois
Logger.Instance.Log("Erro de timeout na API.");

// Teste de prova: ambas as chamadas apontam para o MESMO objeto em memória
var logA = Logger.Instance;
var logB = Logger.Instance;
Console.WriteLine(ReferenceEquals(logA, logB)); // Retorna: True
```

## 🎯 Benefícios

* ✅ **Controle estrito:** Você tem certeza absoluta de que existe apenas uma instância dessa classe rodando no sistema.
* ✅ **Acesso Global:** Qualquer parte do código pode acessar o serviço sem precisar ficar injetando dependências complexas.
* ✅ **Economia de Recursos:** Objetos pesados (como conexões de banco de dados ou gerenciadores de arquivos) são criados apenas uma vez (*Lazy Initialization* garante que só será criado quando for realmente usado pela primeira vez).

## ⚠️ Pontos de atenção

O Singleton é um dos padrões mais criticados (às vezes chamado de *Anti-pattern* se usado sem critério).
* **Variável Global Disfarçada:** Ele introduz estado global no sistema, o que pode gerar acoplamento oculto e dificultar a manutenção se muitas classes começarem a depender dele.
* **Dificuldade de Testes Unitários:** Fazer *mock* de Singletons estáticos em testes unitários costuma ser bastante trabalhoso.
* **Multithreading (Concorrência):** A implementação simples mostrada acima pode falhar se duas *threads* tentarem acessar `Instance` ao mesmíssimo tempo pela primeira vez. Em sistemas críticos, é necessário usar `lock` para garantir o *Thread Safety*.

## 📝 Conclusão

O **Singleton** é incrivelmente útil para serviços de infraestrutura compartilhados, como Loggers, Caches, Gerenciadores de Configuração ou *Pools* de conexão de banco de dados.

> **Dica de ouro:** Use com moderação. O fato de você *poder* acessar uma classe de qualquer lugar não significa que você *deva* fazer isso para todas as regras de negócio do seu sistema.