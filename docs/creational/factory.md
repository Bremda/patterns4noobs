### Factory Method

#### Visão geral
O Factory Method é um padrão de projeto criacional que fornece uma interface para criar objetos em uma superclasse, mas permite que as subclasses decidam qual classe instanciar. Ele delega a responsabilidade de inicialização para subclasses específicas, isolando a lógica de criação da lógica de uso.

#### Caso de uso

Imagine um sistema de pagamentos onde diferentes métodos podem ser utilizados, como Cartão de Crédito, PIX e Boleto. Cada tipo possui suas próprias regras de validação e processamento.

Tratar todas as criações e processamentos diretamente em um único serviço usando blocos condicionais:

```csharp
public class PagamentoService
{
    public void Processar(string tipo)
    {
        if (tipo == "cartao") { /* Lógica do cartão */ }
        else if (tipo == "pix") { /* Lógica do PIX */ }
        else if (tipo == "boleto") { /* Lógica do boleto */ }
    }
}
```

**Desvantagens:**
* **Forte acoplamento:** A classe conhece as regras de criação de todos os métodos de pagamento.
* **Violação do OCP:** Adicionar novos métodos (ex: Criptomoedas) exige modificar essa classe, correndo o risco de quebrar o que já funciona.
* **Complexidade:** O método se torna um bloco gigante e insustentável de `if/else`.

#### Solução
Substitua as chamadas diretas de construção de objetos por chamadas a um método fábrica. Criamos uma abstração para os pagamentos (Produtos) e uma abstração para quem os cria (Fábricas).

#### Implementação

**1. O Produto (Interface e Implementações)**
```csharp
public interface IPagamento
{
    void Processar();
}

public class PagamentoCartao : IPagamento { public void Processar() => Console.WriteLine("Cartão."); }
public class PagamentoPix : IPagamento { public void Processar() => Console.WriteLine("PIX."); }
public class PagamentoBoleto : IPagamento { public void Processar() => Console.WriteLine("Boleto."); }
```

**2. A Fábrica (Creator e Implementações)**
```csharp
public abstract class PagamentoFactory
{
    public abstract IPagamento CriarPagamento();
    
    public void ExecutarPagamento()
    {
        var pagamento = CriarPagamento();
        pagamento.Processar();
    }
}

public class CartaoFactory : PagamentoFactory { public override IPagamento CriarPagamento() => new PagamentoCartao(); }
public class PixFactory : PagamentoFactory { public override IPagamento CriarPagamento() => new PagamentoPix(); }
public class BoletoFactory : PagamentoFactory { public override IPagamento CriarPagamento() => new PagamentoBoleto(); }
```

#### Uso
O código cliente interage apenas com a Factory correta, sem se preocupar em como o objeto final é construído:

```csharp
PagamentoFactory factory = new PixFactory();
IPagamento pagamento = factory.CriarPagamento();
pagamento.Processar();
```

#### Benefícios
* **Remove condicionais:** Elimina as cadeias de `if/else` atreladas à criação de objetos.
* **Princípio Aberto/Fechado (OCP):** Introduza novos tipos de pagamentos sem quebrar o código existente.
* **Princípio de Responsabilidade Única (SRP):** O código de criação fica isolado em um único lugar.
* **Desacoplamento:** Separa rigidamente a criação do uso do objeto.

#### Pontos de atenção
* **Explosão de Classes:** Exige a criação de novas subclasses de Fábrica para cada nova classe de Produto adicionada, o que pode inflar o projeto.
* **Curva de aprendizado:** Pode introduzir complexidade desnecessária para cenários muito simples.

#### Conclusão
O Factory Method é ideal quando o exato tipo de objeto não é conhecido até a execução ou para fornecer uma biblioteca extensível.

> **Dica de ouro:** Se o seu código tem blocos `switch` ou `if/else` gigantescos apenas para decidir qual objeto instanciar, é o momento ideal para utilizar o Factory Method.
