## Strategy

### Visão geral

O **Strategy** é um padrão de projeto comportamental que permite **definir uma família de algoritmos**, encapsulá-los e torná-los **intercambiáveis**.

A ideia principal é separar diferentes comportamentos em classes independentes, permitindo que a lógica seja alterada em tempo de execução **sem alterar o código do cliente**.

### Caso de uso

#### O Cenário
Um sistema de pagamento precisa suportar múltiplos métodos, como **Cartão de Crédito** e **PIX**.  
Inicialmente, um único serviço concentra toda a lógica e decide qual método usar através de estruturas condicionais (`if/else`).

#### Implementação inicial 

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

// Uso:
var processor = new PaymentProcessor();
processor.Pay("Pix", 100);
processor.Pay("CreditCard", 50);
```

#### Por que isso não escala?
* **Código inflado:** O bloco de `if/else` cresce indefinidamente a cada novo método de pagamento adicionado.
* **Manutenção complexa:** É difícil de manter e testar, pois qualquer alteração exige mexer na classe principal.
* **Alto acoplamento:** A classe central precisa conhecer as regras de negócio de todas as variações de pagamento.
* **Violação do Princípio Aberto/Fechado (OCP):** Fere os princípios do SOLID, pois a classe precisa ser modificada para ser estendida.

### Solução

Cada tipo de pagamento se torna uma estratégia independente que implementa um contrato comum (neste caso, `IPaymentStrategy`). O contexto (`PaymentContext`) apenas utiliza a estratégia injetada, ignorando os detalhes técnicos de sua implementação.

#### Estrutura conceitual
* **Contrato (Interface):** Define o comportamento comum que todas as estratégias devem ter.
* **Estratégias concretas:** Implementam as variações reais do comportamento.
* **Contexto:** Delega a execução para a estratégia escolhida no momento.

### Implementação

#### 1. O Contrato

```csharp
public interface IPaymentStrategy
{
    void Pay(decimal amount);
}
```

#### 2. As Estratégias Concretas

```csharp
public class CreditCardPayment : IPaymentStrategy
{
    public void Pay(decimal amount) => Console.WriteLine($"Pago com cartão: {amount:C}");
}

public class PixPayment : IPaymentStrategy
{
    public void Pay(decimal amount) => Console.WriteLine($"Pago com PIX: {amount:C}");
}
```

#### 3. O Contexto

```csharp
public class PaymentContext
{
    private readonly IPaymentStrategy _strategy;

    public PaymentContext(IPaymentStrategy strategy) => _strategy = strategy;

    public void Execute(decimal amount) => _strategy.Pay(amount);
}
```

### Uso

A utilização se torna limpa e direta, injetando a dependência correta para cada fluxo:

```csharp
// Pagamento via Cartão de Crédito
var creditPayment = new PaymentContext(new CreditCardPayment());
creditPayment.Execute(100);

// Pagamento via PIX
var pixPayment = new PaymentContext(new PixPayment());
pixPayment.Execute(50);
```

### Benefícios

* **Remove condicionais:** Elimina `if/else` ou `switch` espalhados pelo código.
* **Extensibilidade:** Facilita adicionar novas estratégias sem alterar o código existente.
* **Qualidade de código:** Mantém o código mais limpo, organizado e altamente testável.
* **Flexibilidade:** Permite trocar o comportamento do sistema em tempo de execução.

### Pontos de atenção

* **Volume de arquivos:** Aumenta naturalmente o número de classes no projeto.
* **Overengineering:** Pode ser uma solução exagerada para cenários muito simples, onde a lógica dificilmente vai mudar.
* **Curva de aprendizado:** Exige que a equipe tenha entendimento sólido de abstrações e interfaces.

### Conclusão

O padrão **Strategy** resolve um problema muito comum no desenvolvimento de software: quando uma mesma operação possui múltiplas formas de ser executada. Ao separar cada comportamento em sua própria classe, o sistema ganha extrema flexibilidade e organização.

Novas regras podem ser adicionadas sem alterar o código existente, reduzindo riscos de *bugs* e facilitando a manutenção a longo prazo.

> **Dica de ouro:** Use o Strategy sempre que você perceber muitos `if/else` decidindo *como* executar uma ação — isso é um forte indício de que esses comportamentos deveriam estar isolados.
