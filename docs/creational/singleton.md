# 🧍 Singleton

## 📌 O que é
O **Singleton** é um padrão que garante que uma classe tenha **apenas uma instância** em todo o sistema e fornece um **ponto global de acesso** a ela.

---
## 🧩 Problema

Imagine um **logger** usado em várias partes da aplicação:

```csharp
public class Logger
{
    public void Log(string message) => Console.WriteLine(message);
}

// várias instâncias diferentes
var logger1 = new Logger();
var logger2 = new Logger();
````

**Problema**
Cada instância é independente. Logs podem ficar espalhados, configurações duplicadas e difícil controle de comportamento.

---
## Como o Singleton resolveria isso?

Com o Singleton, todas as partes do sistema usam a mesma instância:

```
public class Logger
{
    private static Logger _instance;

    private Logger() {}

    public static Logger Instance => _instance ??= new Logger();

    public void Log(string message) => Console.WriteLine(message);
}

```
🧪 **Uso**
```
Logger.Instance.Log("Iniciando sistema");
Logger.Instance.Log("Erro ao conectar");
```

🎯 **Por que isso é útil?**
- Centraliza comportamento
- Evita duplicação de instâncias
- Facilita manutenção

👍 **Quando usar Singleton?**
- Logger
- Configuração central
- Serviços que devem existir apenas uma vez

👎 **Desvantagens**
- Difícil de testar
- Pode virar variável global disfarçada