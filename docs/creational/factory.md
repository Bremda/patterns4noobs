# 🏭 Factory Method

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

O **Factory Method** é um padrão de projeto criacional que fornece uma interface para criar objetos em uma superclasse, mas permite que as **subclasses decidam qual classe instanciar**.

Ele delega a responsabilidade de inicialização (o uso do `new`) para subclasses específicas, isolando a lógica de criação da lógica de uso.

## 🎯 Caso de uso

### O Cenário
Imagine um sistema de pagamentos onde diferentes métodos podem ser utilizados, como **Cartão de Crédito**, **PIX** e **Boleto**. Cada tipo possui suas próprias regras de validação e processamento.

### Implementação inicial (O Problema)
Inicialmente, decidimos tratar todas as criações e processamentos diretamente em um único serviço usando blocos condicionais:

```csharp
public class PagamentoService
{
    public void Processar(string tipo)
    {
        if (tipo == "cartao")
        {
            // Lógica complexa de criação e processamento do cartão
            Console.WriteLine("Processando pagamento com Cartão...");
        }
        else if (tipo == "pix")
        {
            // Lógica complexa de criação e processamento do PIX
            Console.WriteLine("Processando pagamento com PIX...");
        }
        else if (tipo == "boleto")
        {
            // Lógica complexa de geração de boleto
            Console.WriteLine("Gerando e processando Boleto...");
        }
    }
}
```

### Por que isso é um problema?
* **Forte acoplamento:** A classe `PagamentoService` conhece as regras de criação de todos os métodos de pagamento.
* **Violação do OCP (Open/Closed Principle):** Se adicionarmos suporte a Criptomoedas amanhã, seremos obrigados a modificar essa classe, correndo o risco de quebrar o que já funciona.
* **Complexidade:** Com o tempo, o método `Processar` se tornará um bloco gigante e insustentável de `if/else` ou `switch`.

## 💡 Solução

A solução proposta pelo Factory Method é substituir as chamadas diretas de construção de objetos por chamadas a um método *fábrica* especial. 

Criamos uma abstração para os pagamentos (os *Produtos*) e uma abstração para quem os cria (os *Criadores* ou *Factories*).

### Estrutura conceitual
* **Produto (Interface):** O contrato comum para todos os objetos que a fábrica pode criar.
* **Produtos Concretos:** As implementações específicas do contrato (Cartão, PIX, Boleto).
* **Criador (Factory Base):** Declara o método fábrica que retorna o Produto.
* **Criadores Concretos:** Sobrescrevem o método fábrica para retornar uma instância do Produto Concreto específico.

## 🧱 Implementação

### 1. O Produto (Interface e Implementações)

```csharp
// O Contrato
public interface IPagamento
{
    void Processar();
}

// Produtos Concretos
public class PagamentoCartao : IPagamento
{
    public void Processar() => Console.WriteLine("Pagamento com cartão processado com sucesso.");
}

public class PagamentoPix : IPagamento
{
    public void Processar() => Console.WriteLine("Pagamento via PIX confirmado.");
}

public class PagamentoBoleto : IPagamento
{
    public void Processar() => Console.WriteLine("Boleto gerado e aguardando pagamento.");
}
```

### 2. A Fábrica (Creator e Implementações)

```csharp
// Classe Base Criadora
public abstract class PagamentoFactory
{
    // O Factory Method em si
    public abstract IPagamento CriarPagamento();
    
    // Opcional: A fábrica base pode ter alguma lógica centralizada
    public void ExecutarPagamento()
    {
        var pagamento = CriarPagamento();
        pagamento.Processar();
    }
}

// Criadores Concretos
public class CartaoFactory : PagamentoFactory
{
    public override IPagamento CriarPagamento() => new PagamentoCartao();
}

public class PixFactory : PagamentoFactory
{
    public override IPagamento CriarPagamento() => new PagamentoPix();
}

public class BoletoFactory : PagamentoFactory
{
    public override IPagamento CriarPagamento() => new PagamentoBoleto();
}
```

## 🧪 Uso

O código cliente (quem vai usar o sistema) interage apenas com a Factory correta, sem se preocupar em como o objeto final é construído:

```csharp
// O sistema ou o usuário decide qual fábrica usar (por injeção de dependência ou configuração)
PagamentoFactory factory = new PixFactory();

// A fábrica se encarrega de criar o objeto correto
IPagamento pagamento = factory.CriarPagamento();

// O processamento ocorre de forma transparente
pagamento.Processar();

// Saída: Pagamento via PIX confirmado.
```

## 🎯 Benefícios

* ✅ **Remove condicionais:** Elimina as cadeias de `if/else` atreladas à criação de objetos.
* ✅ **Princípio Aberto/Fechado (OCP):** Você pode introduzir novos tipos de pagamentos no sistema sem quebrar o código cliente existente (basta criar um novo `Produto` e uma nova `Factory`).
* ✅ **Princípio de Responsabilidade Única (SRP):** O código de criação do produto fica isolado em um único lugar no programa, facilitando a manutenção.
* ✅ **Desacoplamento:** Separa rigidamente a lógica de *criação* da lógica de *uso* do objeto.

## ⚠️ Pontos de atenção

* **Explosão de Classes:** O código pode se tornar mais complicado do que deveria, pois exige a criação de novas subclasses de "Fábrica" para cada nova classe de "Produto" que você adicionar.
* **Curva de aprendizado:** Pode introduzir uma complexidade desnecessária para cenários muito simples ou para iniciantes que ainda não dominam polimorfismo e herança.

## 📝 Conclusão

O **Factory Method** é o seu melhor amigo quando o exato tipo de objeto e suas dependências não são conhecidos até o momento da execução, ou quando você quer fornecer uma biblioteca em que os usuários possam estender seus componentes internos.

> **Dica de ouro:** Se o seu código tem blocos `switch` ou `if/else` gigantescos apenas para decidir qual objeto dar um `new`, é o momento ideal para utilizar o Factory Method e tornar seu código mais limpo, flexível e aderente aos princípios SOLID.