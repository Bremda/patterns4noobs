# 🔄 Strategy

## 📌 O que é
O **Strategy** é um padrão comportamental que permite **definir uma família de algoritmos** e torná-los intercambiáveis.  
Ele evita grandes blocos de `if/else` ou `switch` no código.

---

## 🧩 Problema
Suponha que você tenha um sistema de pagamento que decide o método com `if/else`:

```csharp
public class PaymentProcessor
{
    public void Pay(string method, decimal amount)
    {
        if (method == "CreditCard")
        {
            Console.WriteLine($"Pago com cartão: {amount}");
        }
        else if (method == "Pix")
        {
            Console.WriteLine($"Pago com PIX: {amount}");
        }
    }
}

var processor = new PaymentProcessor();
processor.Pay("Pix", 100);
processor.Pay("CreditCard", 50);
```

**Problema:**
Adicionar novos métodos de pagamento exige alterar a classe toda. Código difícil de manter e testar.

## Como o Strategy resolveria isso?

Criamos uma interface para o algoritmo de pagamento e implementações separadas:

```
public interface IPaymentStrategy
{
    void Pay(decimal amount);
}

public class CreditCardPayment : IPaymentStrategy
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Pago com cartão: {amount}");
    }
}

public class PixPayment : IPaymentStrategy
{
    public void Pay(decimal amount)
    {
        Console.WriteLine($"Pago com PIX: {amount}");
    }
}
````

Assim, é possível consumir o padrão Strategy na classe PaymentContext:

```
public class PaymentContext
{
    private IPaymentStrategy _strategy;

    public PaymentContext(IPaymentStrategy strategy) => _strategy = strategy;

    public void Execute(decimal amount) => _strategy.Pay(amount);
}

var creditPayment = new PaymentContext(new CreditCardPayment());
creditPayment.Execute(100);

var pixPayment = new PaymentContext(new PixPayment());
pixPayment.Execute(50);
```

🎯 **Vantagens**
- Evita if/else espalhados
- Facilita adicionar novos comportamentos
- Código mais limpo e testável

👍 **Quando usar Strategy?**
- Sistemas com múltiplos algoritmos para a mesma operação
- Regras de negócio que mudam dinamicamente
- Pagamentos, cálculos, filtros, validações

👎 **Desvantagens**
Aumenta número de classes
Pode parecer “complexo demais” para cenários simples