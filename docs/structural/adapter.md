# 🔌 Adapter

## 🧭 Guia rápido

- [📌 Visão geral](#-visão-geral)
- [🎯 Caso de uso](#-caso-de-uso)
- [💡 Solução](#-solução)
- [🧱 Implementação](#-implementação)
- [🧪 Uso](#-uso)
- [🎯 Benefícios](#-benefícios)
- [⚠️ Pontos de atenção](#-pontos-de-atenção)
- [📝 Conclusão](#-conclusão)

---

## 📌 Visão geral

O **Adapter** é um padrão de projeto estrutural que permite que objetos com interfaces incompatíveis **trabalhem juntos**. 

Ele atua como um verdadeiro “tradutor” (como um adaptador de tomada ou um cabo conversor) entre classes que, de outra forma, não poderiam se comunicar diretamente.

---

## 🎯 Caso de uso

### O Cenário
Imagine que você tem uma classe que gera mensagens formatadas em **HTML**, mas o processador principal do seu sistema espera receber apenas **texto simples**.

### Implementação inicial

```csharp
public class HtmlMessage
{
    public string GetHtml() => "<h1>Bem-vindo!</h1>";
}

public class TextProcessor
{
    public void PrintText(string text) => Console.WriteLine(text);
}

// Uso direto:
var htmlMessage = new HtmlMessage();
var processor = new TextProcessor();

// ❌ Não funciona: PrintText espera texto simples, não HTML
processor.PrintText(htmlMessage.GetHtml());
```

### Por que isso é um problema?
* **Incompatibilidade:** O `TextProcessor` não entende HTML e espera receber uma string limpa.
* **Código fechado:** Muitas vezes, a classe original (`HtmlMessage`) não pode ser alterada por fazer parte de uma biblioteca de terceiros ou código legado.
* Precisamos de uma adaptação que converta o HTML para o formato exato que o processador aceita, sem reescrever a lógica existente.

---

## 💡 Solução

Criamos uma classe intermediária, o **Adapter**, que converte a saída HTML da classe original em texto simples. O sistema passa a se comunicar apenas com o Adapter, que traduz os dados por baixo dos panos.

### Estrutura conceitual
* **Serviço Incompatível (Adaptee):** A classe existente que possui a lógica útil, mas em um formato não reconhecido (ex: `HtmlMessage`).
* **O Cliente / Alvo (Target):** O sistema que precisa consumir a informação (ex: `TextProcessor`).
* **Adaptador (Adapter):** A classe que faz a ponte, recebendo dados do Adaptee e entregando ao Cliente no formato correto.

---

## 🧱 Implementação

### 1. As Classes Incompatíveis

```csharp
// A classe que precisamos usar (Gera HTML)
public class HtmlMessage
{
    public string GetHtml() => "<h1>Bem-vindo!</h1>";
}

// Onde precisamos injetar a informação (Espera Texto)
public class TextProcessor
{
    public void PrintText(string text) => Console.WriteLine(text);
}
```

### 2. O Adaptador

```csharp
public class HtmlToTextAdapter
{
    private readonly HtmlMessage _htmlMessage;

    public HtmlToTextAdapter(HtmlMessage htmlMessage) => _htmlMessage = htmlMessage;

     // Simples exemplo de "tradução" do formato HTML para texto simples
    public string GetText() => _htmlMessage.GetHtml()
                                           .Replace("<h1>", "")
                                           .Replace("</h1>", "");
    }
}
```

---

## 🧪 Uso

Ao utilizar o sistema, o cliente instancia o adaptador passando a classe incompatível para ele. O fluxo ocorre de forma transparente:

```csharp
// 1. Instanciamos o gerador de HTML (Incompatível)
var htmlMessage = new HtmlMessage();

// 2. Envolvemos a classe no Adaptador
var adapter = new HtmlToTextAdapter(htmlMessage);

// 3. O processador recebe o texto traduzido
var processor = new TextProcessor();
processor.PrintText(adapter.GetText()); 

// Saída no console: Bem-vindo!
```

---

## 🎯 Benefícios

* ✅ **Reutilização:** Permite reusar classes e bibliotecas existentes sem modificar o código antigo.
* ✅ **Integração:** Facilita a comunicação entre sistemas diferentes ou a integração com APIs que possuem interfaces incompatíveis.
* ✅ **Tradução de dados:** Excelente para traduzir formatos (como de XML para JSON, ou HTML para Texto).
* ✅ **Baixo Acoplamento:** Isola a lógica de conversão da lógica de negócio principal do sistema.

---

## ⚠️ Pontos de atenção

* **Volume de arquivos:** Aumenta a complexidade geral do projeto ao introduzir novas classes e interfaces.
* **Curativo:** Se você tiver acesso total ao código-fonte de ambas as partes e for fácil refatorar, às vezes é melhor corrigir a interface incompatível diretamente do que criar um Adapter.

---

## 📝 Conclusão

O padrão **Adapter** é um "salva-vidas" na engenharia de software, especialmente ao lidar com códigos legados ou bibliotecas externas. Ele garante que sistemas modernos consigam conversar com módulos antigos sem forçar modificações perigosas no código existente.

> **Dica de ouro:** Use o Adapter sempre que precisar integrar duas partes do sistema, mas as interfaces delas "falarem idiomas diferentes". Tal qual no mundo real: você não quebra a parede para trocar a fiação, você apenas usa um adaptador de tomada.