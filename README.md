# Serviço Auditor de Falhas — DLQ

Este projeto implementa o serviço responsável por consumir mensagens da DLQ do exercício anterior, aplicar uma classificação de severidade e gravar o resultado em uma tabela de auditoria.

A seguir explico como cheguei na arquitetura escolhida e como o projeto está dividido.

## Arquitetura Hexagonal
Escolhida para isolar o núcleo do negócio de tecnologias externas (banco, fila, frameworks).

* **Vantagens:** Permite trocar a entrada ou saída sem mexer nas regras de negócio; facilita testes rápidos e organiza as dependências.

### Estrutura do Projeto
* `domain/`: Modelos puros Java e regras de negócio (sem frameworks).
* `application/`: Casos de uso e portas (interfaces/contratos).
* `adapter/`: Integrações reais com o mundo externo (Spring, SQS, JPA).

## Pontos de Destaque
* **Modelo vs Entidade:** Classes separadas para o domínio e para a persistência para evitar acoplamento.
* **Domínio Limpo:** Regras de negócio usam código Java puro, sem anotações do Spring.
* **Garantia de Processamento:** A mensagem só sai da fila após ser gravada com sucesso no banco (via `@SqsListener` e `@Transactional`).
* **Payload Bruto:** A mensagem original é salva como texto (`String`) para simplificar e tolerar mudanças futuras.
* **Banco H2:** Usado para rodar o projeto localmente de forma simples e sem configurações externas.