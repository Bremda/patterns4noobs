# 👷 Builder

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

O **Builder** é um padrão de projeto criacional que permite a construção de objetos complexos **passo a passo**. 

Ele permite que você produza diferentes tipos e representações de um objeto usando o mesmo código de construção, separando a forma como o objeto é criado da sua representação final.

## 🎯 Caso de uso

### O Cenário
Imagine que você está desenvolvendo um sistema para uma lanchonete onde o cliente monta o próprio **Sanduíche**. O cliente escolhe o pão, o recheio principal, o tipo de queijo, e decide quais vegetais e molhos quer adicionar.

### Implementação inicial (O Problema)
A forma mais direta de resolver isso é criar um construtor na classe `Sanduiche` com todos os ingredientes possíveis.

```csharp
public class Sanduiche
{
    public Sanduiche(string pao, string recheio, string queijo, bool temAlface, bool temTomate, bool temCebola, bool temMaionese, bool temMostarda)
    {
        // Atribuição de dezenas de variáveis...
    }
}

// Uso direto (O pesadelo do código limpo):
var sanduicheCompleto = new Sanduiche("Italiano", "Frango", "Cheddar", true, true, true, true, true);

var sanduicheSimples = new Sanduiche("Integral", "Atum", "Prato", false, false, false, false, false);
```

### Por que isso é um problema?
* **Código Ilegível:** Quando você olha para `(true, false, true, true)`, é impossível saber qual booleano refere-se à alface, tomate ou cebola sem olhar a definição da classe.
* **Excesso de Parâmetros:** Para criar um sanduíche simples (sem vegetais e sem molhos), você é obrigado a passar dezenas de parâmetros `false` que não fazem sentido para aquele pedido.
* **Manutenção Difícil:** Se a lanchonete passar a oferecer "Bacon", você terá que modificar o construtor principal, quebrando todos os lugares do sistema que já instanciam um sanduíche.

## 💡 Solução

O padrão Builder sugere que você extraia o código de construção do objeto para fora da sua própria classe e o mova para objetos separados chamados **Builders**.

Em vez de passar todos os parâmetros de uma vez no construtor, você chama métodos específicos no builder de forma sequencial (ex: `ComPao()`, `ComRecheio()`). No final, você chama um método `Construir()` para obter o objeto pronto.

### Estrutura conceitual
* **Produto:** O objeto complexo que está sendo construído (ex: `Sanduiche`).
* **Builder:** A classe responsável por construir o Produto passo a passo. Muitas vezes implementada de forma fluente (*Fluent Interface*), onde cada método retorna a própria instância do Builder para permitir o encadeamento.

## 🧱 Implementação

### 1. O Produto

```csharp
public class Sanduiche
{
    public string Pao { get; set; }
    public string Recheio { get; set; }
    public string Queijo { get; set; }
    public List<string> Vegetais { get; set; } = new List<string>();
    public List<string> Molhos { get; set; } = new List<string>();

    public void ExibirPedido()
    {
        Console.WriteLine($"Sanduíche no pão {Pao} com {Recheio} e {Queijo}.");
        if (Vegetais.Any()) Console.WriteLine($"Vegetais: {string.Join(", ", Vegetais)}");
        if (Molhos.Any()) Console.WriteLine($"Molhos: {string.Join(", ", Molhos)}");
    }
}
```

### 2. O Builder

```csharp
public class SanduicheBuilder
{
    // A instância que está sendo construída
    private Sanduiche _sanduiche = new Sanduiche();

    // Métodos que configuram as partes obrigatórias (retornam 'this' para encadeamento)
    public SanduicheBuilder ComPao(string pao)
    {
        _sanduiche.Pao = pao;
        return this;
    }

    public SanduicheBuilder ComRecheio(string recheio)
    {
        _sanduiche.Recheio = recheio;
        return this;
    }

    public SanduicheBuilder ComQueijo(string queijo)
    {
        _sanduiche.Queijo = queijo;
        return this;
    }

    // Métodos que adicionam partes opcionais
    public SanduicheBuilder AdicionarAlface()
    {
        _sanduiche.Vegetais.Add("Alface");
        return this;
    }

    public SanduicheBuilder AdicionarTomate()
    {
        _sanduiche.Vegetais.Add("Tomate");
        return this;
    }

    public SanduicheBuilder AdicionarMaionese()
    {
        _sanduiche.Molhos.Add("Maionese");
        return this;
    }

    // O método final que devolve o objeto pronto
    public Sanduiche Construir() => _sanduiche;
}
```

## 🧪 Uso

O uso se torna extremamente legível e você só chama os métodos dos ingredientes que o cliente realmente escolheu:

```csharp
// Sanduiche completo
var pedidoCompleto = new SanduicheBuilder()
    .ComPao("Italiano Integral")
    .ComRecheio("Frango Teriyaki")
    .ComQueijo("Cheddar")
    .AdicionarAlface()
    .AdicionarTomate()
    .AdicionarMaionese()
    .Construir();

pedidoCompleto.ExibirPedido();

// Sanduíche simples
var pedidoSimples = new SanduicheBuilder()
    .ComPao("Pão Três Queijos")
    .ComRecheio("Bife de Costela")
    .ComQueijo("Prato")
    .Construir();

pedidoSimples.ExibirPedido();
```

## 🎯 Benefícios

* ✅ **Construção Passo a Passo:** Você pode adiar algumas etapas de construção ou executar etapas de forma condicional.
* ✅ **Código Limpo e Legível:** Acaba com o problema do construtor com parâmetros incompreensíveis (ex: `true, false, true`).
* ✅ **Encapsulamento:** Esconde a complexidade de criação e montagem do objeto do código cliente.
* ✅ **Controle:** O objeto só é efetivamente retornado ao cliente quando estiver totalmente construído e pronto para uso (após o método `Construir()`).

## ⚠️ Pontos de atenção

* **Volume de classes:** Aumenta a complexidade geral do código ao adicionar novas classes e interfaces, o que pode ser um exagero se o seu objeto tem apenas 2 ou 3 propriedades simples.
* **Duplicação de código:** Você acaba tendo que espelhar algumas propriedades (ou criar campos novos) no Builder que já existem na classe principal.

## 📝 Conclusão

O **Builder** é a ferramenta definitiva quando você lida com objetos que exigem muita configuração ou têm muitas partes opcionais. 