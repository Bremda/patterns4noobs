### Builder

#### Visão geral
O Builder é um padrão de projeto criacional que permite a construção de objetos complexos passo a passo. Ele permite que você produza diferentes tipos e representações de um objeto usando o mesmo código de construção, separando a forma como o objeto é criado da sua representação final.

#### Caso de uso

**O Cenário**
Imagine um sistema para uma lanchonete onde o cliente monta o próprio sanduíche, escolhendo pão, recheio principal, queijo, vegetais e molhos.

A forma mais direta de resolver isso é criar um construtor na classe `Sanduiche` com todos os ingredientes possíveis.

```csharp
public class Sanduiche
{
    public Sanduiche(string pao, string recheio, string queijo, bool temAlface, bool temTomate, bool temMaionese)
    {
        // Atribuição de variáveis...
    }
}

// Uso direto:
var sanduicheCompleto = new Sanduiche("Italiano", "Frango", "Cheddar", true, true, true);
```

Desvantagens:
* **Código Ilegível:** É impossível saber o que `(true, true, true)` significa sem olhar a definição da classe.
* **Excesso de Parâmetros:** Para criar um sanduíche simples, você é obrigado a passar dezenas de parâmetros `false`.
* **Manutenção Difícil:** Adicionar novos ingredientes quebra todos os lugares do sistema que já instanciam um sanduíche.

#### Solução
Extraia o código de construção do objeto para fora da sua própria classe e o mova para objetos separados chamados Builders. Em vez de passar todos os parâmetros de uma vez no construtor, você chama métodos específicos no builder de forma sequencial. No final, chama um método `Construir()` para obter o objeto pronto.

#### Implementação

**1. O Produto**
```csharp
public class Sanduiche
{
    public string Pao { get; set; }
    public string Recheio { get; set; }
    public string Queijo { get; set; }
    public List<string> Vegetais { get; set; } = new List<string>();
    public List<string> Molhos { get; set; } = new List<string>();
}
```

**2. O Builder**
```csharp
public class SanduicheBuilder
{
    private Sanduiche _sanduiche = new Sanduiche();

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

    public SanduicheBuilder AdicionarAlface()
    {
        _sanduiche.Vegetais.Add("Alface");
        return this;
    }

    public Sanduiche Construir() => _sanduiche;
}
```

#### Uso
O uso se torna legível e você só chama os métodos dos ingredientes escolhidos:

```csharp
var pedido = new SanduicheBuilder()
    .ComPao("Italiano Integral")
    .ComRecheio("Frango Teriyaki")
    .AdicionarAlface()
    .Construir();
```

#### Benefícios
* **Construção Passo a Passo:** Permite adiar ou condicionar etapas de construção.
* **Código Limpo:** Resolve o problema de construtores com muitos parâmetros.
* **Encapsulamento:** Esconde a complexidade de criação do código cliente.

#### Pontos de atenção
* **Volume de classes:** Aumenta a complexidade geral do código, sendo um exagero para objetos simples.
* **Duplicação de código:** Pode ser necessário espelhar propriedades no Builder que já existem na classe principal.

#### Conclusão
O Builder é a ferramenta definitiva quando você lida com objetos que exigem muita configuração ou têm muitas partes opcionais.
