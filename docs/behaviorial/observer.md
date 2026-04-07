## Observer

### Visão geral
O Observer é um padrão de projeto comportamental que permite definir um mecanismo de assinatura para notificar múltiplos objetos sobre quaisquer eventos que aconteçam com o objeto que eles estão observando.

A ideia principal é criar uma relação de "um-para-muitos" entre objetos, garantindo que, quando um objeto alterar seu estado, todos os seus dependentes sejam notificados e atualizados automaticamente sem criar um forte acoplamento entre eles.

### Caso de uso

#### O Cenário
Uma loja virtual possui a funcionalidade de "Avise-me quando chegar". Quando um produto muito aguardado volta ao estoque, o sistema precisa notificar os clientes interessados via E-mail, SMS e Notificação Push.

Inicialmente, a classe do produto concentra todas as chamadas para os diferentes serviços de notificação diretamente em seu método.

#### Implementação inicial
```
public class Product
{
    private readonly EmailService _emailService = new();
    private readonly SmsService _smsService = new();
    private readonly PushService _pushService = new();

    public string Name { get; set; }
    public bool IsInStock { get; private set; }

    public void SetInStock()
    {
        IsInStock = true;
        Console.WriteLine($"O produto {Name} voltou ao estoque!");

        // Notificações fortemente acopladas
        _emailService.SendEmail(Name);
        _smsService.SendSms(Name);
        _pushService.SendPush(Name);
    }
}

// Uso:
var product = new Product { Name = "PlayStation 5" };
product.SetInStock();
```

**Problemas**
- **Código engessado:** Se quisermos adicionar um novo canal de notificação (ex: WhatsApp), precisaremos modificar a classe Product.
- **Alto acoplamento:** A classe Product precisa conhecer os detalhes de todos os serviços de envio de mensagens, o que não deveria ser sua responsabilidade.
- **Falta de dinamismo:** Não é possível adicionar ou remover canais de notificação em tempo de execução (por exemplo, se um usuário quiser desativar o SMS).
- **Violação do Princípio Aberto/Fechado (OCP) e Responsabilidade Única (SRP):** Fere os princípios do SOLID, pois a classe tem múltiplos motivos para mudar e não está fechada para modificação.

### Solução
A classe principal (chamada de Subject ou Sujeito) passa a manter uma lista de referências para os objetos interessados (os Observers ou Observadores). Ela não sabe quem eles são concretamente, apenas que implementam uma interface comum de notificação.

#### Estrutura conceitual
- **Sujeito (Subject / Publisher):** Gerencia a lista de observadores e emite as notificações quando seu estado muda.
- **Contrato do Observador (Interface):** Define o método de atualização que os observadores devem implementar.
- **Observadores Concretos (Subscribers):** Implementam a lógica específica do que fazer quando recebem a notificação do Sujeito.

### Implementação
1. O Contrato do Observador
```
public interface IObserver
{
    void Update(string productName);
}
```
2. Os Observadores Concretos
```
public class EmailNotifier : IObserver
{
    public void Update(string productName) => Console.WriteLine($"[E-mail] O produto {productName} está disponível!");
}

public class SmsNotifier : IObserver
{
    public void Update(string productName) => Console.WriteLine($"[SMS] O produto {productName} está disponível!");
}
```
3. O Sujeito (O Produto)
```
public class ProductSubject
{
    private readonly List<IObserver> _observers = new();
    public string Name { get; set; }
    private bool _isInStock;

    public void Attach(IObserver observer) => _observers.Add(observer);
    
    public void Detach(IObserver observer) => _observers.Remove(observer);

    public void SetInStock()
    {
        _isInStock = true;
        Console.WriteLine($"\n--- Estoque atualizado: {Name} ---");
        Notify();
    }

    private void Notify()
    {
        foreach (var observer in _observers)
                 observer.Update(Name);
    }
}
```
### Uso
A utilização separa completamente quem gera o evento de quem reage a ele, permitindo assinaturas dinâmicas:
```
// 1. Criamos o produto (Sujeito)
var ps5 = new ProductSubject { Name = "PlayStation 5" };

// 2. Criamos os observadores
var emailNotifier = new EmailNotifier();
var smsNotifier = new SmsNotifier();

// 3. Inscrevemos os observadores interessados
ps5.Attach(emailNotifier);
ps5.Attach(smsNotifier);

// 4. Mudamos o estado que irá disparar notificações automaticamente
ps5.SetInStock();

// Podemos remover um observador em tempo de execução sem quebrar nada
ps5.Detach(smsNotifier);
```
### Benefícios
- **Desacoplamento:** O emissor do evento não precisa saber nada sobre as classes que estão recebendo a notificação.
- **Extensibilidade:** Adicionar novos tipos de observadores não altera o código do Sujeito (respeita o OCP).
- **Relações dinâmicas:** Permite estabelecer ou desfazer conexões entre objetos em tempo de execução.
- **Base para eventos:** É a fundação arquitetural para a programação reativa e sistemas de mensageria modernos.

### Pontos de atenção
- **Vazamento de memória (Memory Leaks):** Conhecido como o problema do Lapsed Listener, ocorre se você esquecer de desinscrever (chamar o Detach) observadores que não são mais necessários, impedindo que o Garbage Collector os limpe.
- **Ordem de execução:** Geralmente, a ordem na qual os observadores são notificados é aleatória ou não garantida. O código não deve depender da ordem em que o Update é chamado.
- **Atualizações em cascata:** Se um observador também for um sujeito, isso pode gerar correntes de atualização complexas e difíceis de debugar.

### Conclusão
O padrão Observer é a solução definitiva para cenários onde uma alteração em um objeto precisa refletir em vários outros. Ao transformar a comunicação direta em um modelo de assinatura, o sistema se torna modular e altamente preparado para crescer de forma sustentável.

É um padrão onipresente no desenvolvimento de software, sendo o coração de sistemas de eventos de UI (como o clique de um botão) e arquiteturas baseadas em eventos (Event-Driven).
