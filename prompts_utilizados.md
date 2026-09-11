# Registro de Prompts Utilizados

Este arquivo registra as instruções que orientaram a reconstrução do trabalho. O tema foi limitado ao setor de prospecção e à função de registrar uma tentativa de contato.

## Prompt 1 — Definição do recorte

```text
Refaça o trabalho de estratégias e níveis de teste usando algo que já exista no
Sistema de Atendimento e Prospecção. Escolha somente um setor e mantenha tudo em
português. Use funções, termos, fluxos e diagramas fáceis de explicar. Não vincule
o trabalho a uma organização específica. Faça um diagrama para cada tipo de teste e mantenha o
mesmo exemplo em toda a apresentação.
```

**Decisão adotada:** setor de prospecção; função de registrar o resultado de uma tentativa de contato.

## Prompt 2 — Padronização dos diagramas

```text
Para cada tipo de teste, crie um diagrama UML simples em Mermaid e uma versão
equivalente em PlantUML. Use sempre os mesmos componentes: Tela de Atendimento,
API de Atendimentos, Serviço de Atendimentos, Repositório de Atendimentos e Banco
de Dados. Use nomes e relações em português. Mostre somente os componentes que
participam do teste explicado.
```

**Decisão adotada:** os 13 diagramas usam o mesmo fluxo e variam apenas o ponto observado por cada estratégia.

## Prompt 3 — Revisão para apresentação

```text
Revise o material como um professor da disciplina de Verificação, Validação e
Teste de Software. Confira se unidade não foi confundida com integração; se o
simulador e o acionador estão em papéis corretos; se fumaça e regressão são
diferentes; se Alfa e Beta são fáceis de distinguir; e se estresse e desempenho
não foram tratados como sinônimos. Simplifique qualquer termo desnecessário.
```

**Resultado da revisão:**

- unidade ficou isolada na classificação do resultado;
- o simulador substitui o repositório chamado pelo serviço;
- o acionador chama diretamente o repositório e o banco;
- fumaça verifica o básico e regressão repete proteções antigas;
- Alfa ocorre internamente e Beta com convidados;
- estresse procura o limite e desempenho mede a carga normal.

## Ferramentas de conferência

- [Editor do Mermaid](https://mermaid.live/)
- [PlantText](https://www.planttext.com/)
- [Documentação do PlantUML](https://plantuml.com/)
