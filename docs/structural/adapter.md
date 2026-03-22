# 🧩 Adapter

## 📌 O que é
O **Adapter** é um padrão estrutural que permite que duas interfaces incompatíveis **trabalhem juntas**.  
Ele atua como um “tradutor” entre classes que não poderiam se comunicar diretamente.

---

## 🧩 Problema
Suponha que você tem uma classe que gera mensagens em **HTML**, mas seu sistema espera **texto simples**:

```csharp
public class HtmlMessage
{
    public string GetHtml() => "<h1>Bem-vindo!</h1>";
}

public class TextProcessor
{
    public void PrintText(string text) => Console.WriteLine(text);
}

// Uso direto
var htmlMessage = new HtmlMessage();
var processor = new TextProcessor();

// Não funciona: PrintText espera texto simples
processor.PrintText(htmlMessage.GetHtml());
```

**Problema:**
O TextProcessor não entende HTML e espera receber um texto, desta forma, precisamos de uma adaptação que converta o HTML para o formato do processor.

## Como o Adapter resolveria isso?

Criamos um Adapter que converte HTML em texto simples:

```
public class HtmlToTextAdapter
{
    private HtmlMessage _htmlMessage;

    public HtmlToTextAdapter(HtmlMessage htmlMessage)
    {
        _htmlMessage = htmlMessage;
    }

    public string GetText()
    {
        // Simples exemplo de “tradução” HTML → texto
        return _htmlMessage.GetHtml()
                           .Replace("<h1>", "")
                           .Replace("</h1>", "");
    }
}


Uso:
var htmlMessage = new HtmlMessage();
var adapter = new HtmlToTextAdapter(htmlMessage);

var processor = new TextProcessor();
processor.PrintText(adapter.GetText()); // Bem-vindo!
```

🎯 **Vantagens**
- Permite reusar classes existentes sem modificar código antigo
- Facilita integração entre sistemas diferentes
- Reduz acoplamento

👍 **Quando dever usar Adapter?**
- Integrar APIs com interfaces incompatíveis
- Traduzir formatos de dados
- Adaptar bibliotecas legadas

👎 **Desvantagens**
- Aumenta número de classes
- Pode tornar o fluxo mais complexo se usado em excesso