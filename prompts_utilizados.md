# Registro de Prompts Utilizados

Laboratório 02 — StreamPulse  
Os três prompts-base abaixo são os da atividade (guia do estudante), preenchidos com o domínio StreamPulse. Em seguida, a autoarguição usada para revisar os diagramas.

---

## Metodologia

Cada prompt traz **persona**, **contexto** (StreamPulse + nível de teste), **instrução** e **critério** (UML com `+`/`-`, papéis de stub/driver, texto amarrado ao diagrama). A IA gerou rascunhos; a versão publicada foi revisada para ficar no padrão enxuto do exemplo Pix (classe real, interface, stub/driver, relacionamentos `utiliza` / `implementa`).

---

## Prompt 1 — Brainstorming e definição da arquitetura

Usado para o nível de integração, abordagem **Bottom-Up com Drivers** (item 2.3), no formato pedido pelo professor:

```text
Atue como um arquiteto de software e engenheiro de testes sênior.
Estou desenvolvendo um estudo de caso sobre estratégias de teste para um sistema fictício
de streaming de vídeo sob demanda (StreamPulse: catálogo, playback HLS, CDN e transcodificação).
Para o nível de "Teste de Integração", especificamente a abordagem
"Integração Incremental Bottom-Up com Drivers", proponha:
1. Uma hierarquia de 3 classes/módulos atômicos da base e um módulo superior que consome esses serviços.
2. Como um 'Driver de Teste' deve ser projetado para exercitar esses módulos antes do
   controlador principal existir.
3. Quais defeitos de interface essa abordagem detecta com facilidade.
```

**Resultado adotado:** base `VideoSegmentDecoder.decode(bytes, codec, pts)`; driver `DecoderTestDriver` (segmento válido, NAL corrompido, codec incompatível); módulo superior ainda inexistente `PlayerStreamer.pumpBuffer()`. Defeitos: PTS invertido, crash em NAL malformado, codec que não corresponde aos bytes.

Variação do mesmo prompt para o **Top-Down (2.2):** topo `PlaybackOrchestrator.startPlayback`, contrato `ICdnEdgeRouter.resolveManifest`, stub `CdnEdgeRouterStub` com `simular404()` e `simularTimeout()` — espelhando o exemplo Pix (`TransferenciaPixService` / `IGatewayBacen` / `GatewayBacenStub`).

---

## Prompt 2 — Geração de diagramas em sintaxe Mermaid

Usado para o **Teste de Recuperação (4.1)**, no formato pedido pelo professor:

```text
Com base no cenário de Teste de Recuperação em Sistema de Streaming (StreamPulse),
gere um diagrama de sequência em sintaxe Mermaid válido.
O diagrama deve ilustrar:
- A aplicação cliente fazendo requisição.
- O nó primário do banco de dados sofrendo uma queda abrupta (crash).
- O mecanismo de failover redirecionando para o nó réplica secundário.
- A resposta sendo entregue ao cliente sem interrupção de serviço.
Formate apenas o bloco de código mermaid com rótulos em português.
```

**Resultado adotado:** `Cliente` → `PlayerStreamer` → `PlaybackOrchestrator` → `RedisPrimario`; `InjecaoDeFalha` derruba o primário; failover para `RedisReplica`; `resumeFrom(positionMs)` devolve o playback ao cliente. O mesmo cenário foi reescrito em PlantUML para ficar alinhado ao exemplo da atividade.

Para as demais abordagens, o pedido foi: *diagrama de classes ou de sequência UML, no estilo do exemplo Pix (classe, interface, stub/driver, estereótipos), sem fluxograma de CI/CD*.

---

## Prompt 3 — Autoarguição e validação crítica

Usado sobre o texto de **Teste de Fumaça (2.4)**, no formato pedido pelo professor:

```text
Atue como um professor universitário de Engenharia de Software da disciplina de
Verificação, Validação e Testes.
Analise a seguinte explicação que elaborei para o conceito de "Teste de Fumaça (Smoke Testing)":
"A SmokeSuite chama ApiGateway.login, obterManifesto e health e espera HTTP 200.
Qualquer não-200 aborta o pipeline. Não medimos p95 nem reembolso pro-rata."
Faça 3 perguntas técnicas desafiadoras sobre as decisões desse teste para avaliar se eu
realmente compreendi o conceito ou se apenas gerei o texto superficialmente.
```

**Perguntas recebidas e respostas usadas na revisão:**

1. *Por que fumaça não é teste de sistema?*  
   Porque não integra infra real com injeção de falha nem mede SLO; só pergunta se as rotas vitais respondem 200.
2. *Por que não incluir o cancelamento com pro-rata na suíte de fumaça?*  
   Pro-rata é critério de aceite (3.1). Fumaça que cresce demais deixa de ser fumaça.
3. *Se o manifesto voltar 200 com corpo vazio, a fumaça passa?*  
   Não. O oráculo de `test_manifesto_m3u8_retorna_200` exige `#EXT-X-STREAM-INF`, não só o status.

O mesmo tipo de autoarguição foi aplicado a stub vs driver (2.2/2.3), Alfa vs Beta (3.2/3.3) e estresse vs desempenho (4.3/4.4).

---

## Ferramentas de conferência dos diagramas

- [Mermaid Live Editor](https://mermaid.live/)
- [PlantText](https://www.planttext.com/)
- [PlantUML Web Server](https://www.plantuml.com/plantuml/uml/)
- Aula de classes UML: https://maxwellamaral.github.io/lessons/softeng/design/uml_classes/
