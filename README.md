# 📚 patterns4noobs

> **Design Patterns explicados de forma simples, prática e sem complicação.**

[![C#](https://img.shields.io/badge/C%23-239120?style=flat-square&logo=c-sharp&logoColor=white)](#)
[![.NET](https://img.shields.io/badge/.NET-512BD4?style=flat-square&logo=dotnet&logoColor=white)](#)
[![Status: Em Construção](https://img.shields.io/badge/Status-Em_Construção-orange?style=flat-square)](#)

Este repositório foi criado para desmistificar os Padrões de Projeto (Design Patterns) para desenvolvedores iniciantes, trazendo explicações diretas, casos de uso reais e exemplos de código em **C#**.

---

## 🎯 Objetivo

Ajudar desenvolvedores a entender:
* **O que são** Design Patterns e que problemas eles resolvem;
* **Quando usar** (e, mais importante, quando *não* usar) cada padrão;
* **Como implementar** na prática, fugindo de explicações puramente acadêmicas.

---

## 🧠 Padrões 

Os padrões de projeto são divididos em três categorias principais. Clique no padrão para ler o guia completo:

### 🏗️ Criacionais
Lidam com os mecanismos de criação de objetos, buscando instanciá-los da forma mais adequada para a situação.

* [**Singleton**](./docs/creational/singleton.md) — Garante uma única instância de uma classe e um ponto global de acesso a ela.

### 🧩 Estruturais
Explicam como montar objetos e classes em estruturas maiores, sem perder a eficiência e a flexibilidade.

* [**Adapter**](./docs/structural/adapter.md) — Permite que interfaces incompatíveis trabalhem juntas trabalhando como um "tradutor".

### 🔄 Comportamentais
Cuidam da comunicação e da atribuição de responsabilidades entre os objetos.

* [**Strategy**](./docs/behavioral/strategy.md) — Permite definir uma família de algoritmos e torná-los intercambiáveis em tempo de execução.

---

## 💻 Tecnologias e Pré-requisitos

Para rodar e testar os exemplos deste repositório, você vai precisar de:
* **Linguagem:** C#
* **Plataforma:** .NET (Recomendado .NET 6 ou superior)
* **IDE Recomendada:** Visual Studio, VS Code ou Rider

---

## 📖 Referências

Este material foi construído com base nas melhores literaturas sobre o tema:
* **Design Patterns: Elements of Reusable Object-Oriented Software** (Gang of Four / GoF)
* [Refactoring.Guru](https://refactoring.guru/pt-br/design-patterns) - Uma excelente referência moderna e visual para padrões de projeto.

---

## 🤝 Como Contribuir

Esse repositório está sendo construído aos poucos e toda contribuição é muito bem-vinda! 

Se você notou algum erro, tem uma ideia para melhorar um exemplo ou quer escrever sobre um padrão que ainda não está na lista:
1. Faça um **Fork** do projeto.
2. Crie uma nova branch (`git checkout -b feature/novo-padrao`).
3. Faça o commit das suas alterações (use os *Conventional Commits*, ex: `docs: adicionar padrão Factory Method`).
4. Abra um **Pull Request**.

---
*Feito com ☕ e código por [Brenda Valadares/@Bremda].*