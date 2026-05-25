### Abstract Factory

#### Visão geral
É um padrão criacional que permite produzir famílias de objetos relacionados sem especificar suas classes concretas, garantindo que os produtos de uma mesma família combinem entre si.

#### Caso de uso
**O Cenário**
Uma interface gráfica (GUI) que precisa renderizar botões e checkboxes nativos para Windows e macOS. A abordagem ingênua enche o código de condicionais:

```csharp
if (so == "Windows") new BotaoWindows().Renderizar();
else if (so == "Mac") new BotaoMac().Renderizar();
```

Desvantagens:
* **Alto Acoplamento:** Código amarrado a classes concretas.
* **Risco de Inconsistência:** É fácil misturar acidentalmente um botão do Windows com um checkbox do Mac.
* **Manutenção Complexa:** Adicionar um novo sistema operacional exige alterar dezenas de if/else.

#### Solução
Crie interfaces para os produtos (ex: IBotao) e uma interface de Fábrica Abstrata (IGUIFactory) com métodos de criação. Implemente fábricas concretas (ex: WindowsFactory) que só retornam produtos daquela família específica.

#### Implementação
1. Os Produtos

```
public interface IBotao { void Renderizar(); }

public class BotaoWindows : IBotao { public void Renderizar() => Console.WriteLine("Botão Windows"); }
public class BotaoMac : IBotao { public void Renderizar() => Console.WriteLine("Botão Mac"); }
```
2. As Fábricas

```
public interface IGUIFactory { IBotao CriarBotao(); }

public class WindowsFactory : IGUIFactory { public IBotao CriarBotao() => new BotaoWindows(); }
public class MacFactory : IGUIFactory { public IBotao CriarBotao() => new BotaoMac(); }
```
#### Uso
Você define a fábrica uma única vez. O resto do código trabalha apenas com interfaces:

```
IGUIFactory fabrica = (soAtual == "Windows") ? new WindowsFactory() : new MacFactory();

var botao = fabrica.CriarBotao();
botao.Renderizar(); // O cliente não sabe qual classe concreta foi instanciada
```
#### Benefícios
* **Compatibilidade:** Produtos da mesma fábrica sempre funcionam juntos.
* **Baixo Acoplamento:** O código cliente interage apenas com abstrações.
* **Open/Closed Principle:** Fácil adicionar novas famílias (ex: LinuxFactory) sem quebrar o código existente.

#### Pontos de atenção
* **Rigidez:** Adicionar um novo tipo de produto (ex: CaixaDeTexto) exige alterar a interface IGUIFactory e todas as fábricas concretas.
* **Complexidade:** Gera uma grande quantidade de classes e interfaces iniciais.

#### Conclusão
Use o Abstract Factory quando seu sistema precisar operar com diferentes famílias de produtos consistentes entre si, isolando completamente a lógica de criação do código cliente.
