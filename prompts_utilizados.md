# Registro de Prompts Utilizados

Laboratório 02 — Estratégias e Níveis de Teste na Prática  
Estudo de caso: **StreamPulse**  
Repositório: `DiogoBAguiar/atividade-testes-software`

Este arquivo documenta a engenharia de prompts que produziu o relatório técnico, os treze diagramas e as perguntas de defesa oral. O objetivo não é “pedir um desenho à IA”, e sim tornar **reproduzível** o raciocínio de V&V: persona, contexto de disciplina, instrução operacional e critérios de aceite do artefato.

---

## 1. Metodologia de Engenharia de Prompts

Adotou-se o quadro **PCIC** (Persona, Contexto, Instrução, Critérios), alinhado à forma como a disciplina trata artefatos: um resultado só é evidência se houver referência, responsável, versão e decisão.

### 1.1 Persona

A persona fixa o papel técnico e o vocabulário normativo. Sem ela, o modelo mistura “bug”, “erro” e “falha”, trata stub como mock e coloca UAT no mesmo saco que teste de unidade.

> Você é Arquiteto de Software e Engenheiro Especialista em Garantia da Qualidade (QA/VVTS). Fala a língua da disciplina: Pressman/Maxim (estratégia em espiral), Boehm (verificação × validação), IEEE/ISO (erro → defeito/fault → falha) e UML 2 (visibilidade `+` `#` `-`, estereótipos `<<interface>>` `<<service>>`).

### 1.2 Contexto

O contexto amarra o domínio, os materiais da disciplina e as restrições de publicação.

- Domínio único: StreamPulse (VOD + transcodificação distribuída).
- Componentes obrigatórios: API Gateway, Autenticação/Assinaturas, Catálogo, Transcodificação, CDN, banco distribuído de metadados/telemetria.
- Base teórica: apostila VVTS (Maxwell Amaral, 2026-07-30) e lição de diagrama de classes UML (visibilidade, interface, agregação/composição).
- Entrega: `README.md` renderizável no GitHub, `prompts_utilizados.md` e `diagramas/*.mmd`.
- Público: professor da disciplina e banca de defesa oral.

### 1.3 Instrução

A instrução operacional separa **o que gerar** de **como justificar**. Cada abordagem deve nascer com três blocos inseparáveis:

1. diagrama Mermaid válido, com papéis explícitos (módulo real, stub, driver, injeção de falha, ator, ambiente);
2. texto que referencia classes, métodos, parâmetros e mensagens **do próprio diagrama**;
3. defeitos-alvo nomeados (não “garantir qualidade”).

### 1.4 Critérios

Critérios transformam o prompt em oráculo de aceite — o mesmo princípio do teste: sem resultado esperado, a execução não é evidência.

| Código | Critério de aceite do artefato |
| --- | --- |
| C1 | As treze abordagens usam a numeração 1.1, 2.1–2.5, 3.1–3.3, 4.1–4.4. |
| C2 | Cada diagrama declara papéis (SUT, stub, driver, injeção, ator, ambiente). |
| C3 | Cadeia causal **erro → defeito → falha** aplicada a pelo menos um exemplo por nível. |
| C4 | Verificação (“produto corretamente?”) distinta de validação (“produto certo?”). |
| C5 | UML com `+` público, `-` privado, `#` protegido e estereótipos padrão. |
| C6 | Mermaid renderizável no GitHub; arquivos `.mmd` equivalentes em `diagramas/`. |
| C7 | Sem placeholders (`TODO`, `lorem`, “inserir diagrama aqui”). |
| C8 | Segurança descrita como **desenho de teste** (oráculos HTTP, classes OWASP), não como procedimento de exploração. |

### 1.5 Ciclo de refinamento

Cada geração passou por três voltas, análogas a revisão técnica + reteste:

1. **Elicitação** — arquitetura e vocabulário.
2. **Materialização** — diagrama + mecânica.
3. **Autoarguição** — perguntas que um professor faria na banca.

---

## 2. Prompt mestre (elicitação da entrega completa)

Prompt realmente usado para fixar escopo, arquivos e rigor. É a especificação do laboratório em forma de instrução única.

```text
Você é um Arquiteto de Software e Engenheiro Especialista em Garantia da Qualidade (QA/VVTS).
Sua tarefa é gerar a documentação técnica completa e os artefatos de engenharia para o
Laboratório 02 da disciplina de Verificação, Validação e Teste de Software (VVTS),
estruturando os arquivos prontos para publicação no repositório GitHub
DiogoBAguiar/atividade-testes-software.

DOMÍNIO: Plataforma de Streaming de Vídeo sob Demanda e Transcodificação Distribuída (StreamPulse).
Componentes: Gateway de API, Módulo de Autenticação/Assinaturas, Catálogo de Mídias,
Microsserviço de Transcodificação de Vídeo, Pipeline de Distribuição CDN e Banco de Dados
Distribuído de Metadados/Telemetria.

ARQUIVOS:
1. README.md — relatório técnico com diagramas Mermaid nativos e justificativas completas.
2. prompts_utilizados.md — registro dos prompts de ideação, diagramas e autoarguição.
3. diagramas/ — um arquivo .mmd por cenário (13 no total).

PARA CADA UMA DAS 13 ABORDAGENS:
- Título e numeração exata (1.1, 2.1–2.5, 3.1–3.3, 4.1–4.4).
- Diagrama Mermaid válido (classDiagram, sequenceDiagram, flowchart ou stateDiagram-v2)
  explicitando módulos reais, stubs, drivers, injeções de falha, atores e ambientes.
- Texto com: Contexto Arquitetural; Mecânica do Teste (classes, métodos, parâmetros,
  interfaces, mensagens); Objetivo e Defeitos Alvo.

RIGOR:
- Erro (humano) → Defeito/Fault (artefato) → Falha (comportamento observável).
- Verificação ≠ Validação (critério de Boehm).
- UML: + público, - privado, # protegido, <<interface>>, <<service>>.
- Conteúdo completo, sem placeholders.

Base teórica obrigatória: apostila VVTS (Pressman/Maxim, níveis de teste, SQA, CoQ)
e a lição de diagrama de classes UML da disciplina.
```

---

## 3. Prompt de elicitar a arquitetura StreamPulse

Usado para impedir que os treze testes “flutuem” em componentes inventados a cada seção.

```text
A partir do domínio StreamPulse, derive uma arquitetura em camadas coerente com microsserviços
de mídia. Para cada componente (ApiGateway, AuthService, BillingService, CatalogService,
TranscodeWorker, PlaybackOrchestrator, ICdnEdgeRouter, VideoSegmentDecoder,
AdaptiveBitrateController, TokenJwtValidator, CalculadoraTaxaTranscodificacao, SessionStore
Redis, WatchPartySignaling, pipeline de telemetria), informe:

1. responsabilidade de negócio;
2. interfaces públicas (assinatura de métodos e DTOs);
3. dependências e pontos de falha típicos;
4. qual nível da espiral de Pressman (unidade, integração, validação, sistema)
   é o mais informativo para aquele componente e por quê.

Restrições:
- Use nomenclatura UML e estereótipos <<service>>, <<interface>>, <<enumeration>>.
- Não misture SUT com dublês: se o componente ainda não existe, nomeie stub ou driver.
- Produza um classDiagram Mermaid da visão lógica e um flowchart da visão de implantação
  (cliente → PoP CDN → origem → workers de transcode → cluster de metadados).
- A CalculadoraTaxaTranscodificacao deve ter regra de precificação testável por valores-limite
  (duração, bitrate min/máx, fatores LIVE e HDR), no espírito do exemplo pytest da apostila.
```

**Saída obtida (síntese):** a visão lógica do `README.md` (Gateway, Auth/Billing, Catálogo, Playback, Transcode, CDN, Redis/telemetria) e o contrato `ICdnEdgeRouter.resolveManifest(assetId, region)`.

---

## 4. Prompts de geração dos blocos Mermaid

Em vez de um único “gere 13 diagramas”, cada família recebeu um prompt com oráculo de papéis. Isso reduz o defeito clássico de geração: diagramas esteticamente corretos e conceitualmente invertidos (stub no lugar do SUT, Beta desenhado como staging interno, estresse confundido com desempenho).

### 4.1 Família Unidade (1.1) — `classDiagram`

```text
Gere um classDiagram Mermaid para o teste de unidade da classe CalculadoraTaxaTranscodificacao.

SUT: CalculadoraTaxaTranscodificacao (módulo real).
Driver: TestCalculadoraTaxaTranscodificacao (pytest), métodos nomeados por valor-limite.
Proibido: stubs, rede, Redis, CDN.

A classe real deve expor:
+calcularCusto(perfil: PerfilTranscodificacao, duracaoSegundos: int, prioridade: PrioridadeFila) Decimal
+validarPerfil(perfil: PerfilTranscodificacao) void
-aplicarFatorPrioridade(...) Decimal
-arredondarCentavos(...) Decimal

Enumeração PrioridadeFila: BATCH, STANDARD, LIVE, EMERGENCIA.
Exceção PerfilInvalidoError.

Critério: o diagrama precisa deixar óbvio que não há dependência externa.
Depois do diagrama, explique a mecânica citando cada método de teste e o defeito
(off-by-one em bitrate, fator LIVE não aplicado, arredondamento bancário incorreto).
```

### 4.2 Família Integração estrutural (2.1, 2.2, 2.3)

```text
Modele três diagramas distintos, sem reutilizar a mesma topologia.

2.1 Big Bang — flowchart: BigBangTestHarness liga de uma vez AuthService, CatalogService,
PlayerStreamer e BillingService. Mostre MetadataCluster, EventBus e CdnOrigin.
A falha observável (HTTP 500 / entitlement negado) deve ter arestas pontilhadas para
TODOS os módulos, ilustrando a dificuldade de isolar causa-raiz.

2.2 Top-down — sequenceDiagram: PlaybackOrchestrator (real) chama
CdnEdgeRouterStub via ICdnEdgeRouter. O CdnEdgeRouter real permanece no box
"fora desta rodada". Três alt: 200+manifesto, 404 CdnNotFoundException, 503 timeout.

2.3 Bottom-up — sequenceDiagram: DecoderTestDriver aciona VideoSegmentDecoder
e FrameBuffer. PlayerStreamer e PlayerUiController existem no diagrama apenas como
"ainda não construídos". Exercitar decode feliz, NAL corrompido e CODEC_MISMATCH.

Papéis devem aparecer em boxes coloridos: real, stub, driver, ambiente.
```

### 4.3 Família Integração de build (2.4, 2.5)

```text
2.4 Smoke — flowchart LR do GitHub Actions. Três rotas vitais somente:
POST /auth/login, GET /playback/{id}/master.m3u8, GET /health.
Portão: build verde segue; qualquer não-200 nas vitais aborta o pipeline.
Não desenhe a suíte completa de regressão.

2.5 Regressão — flowchart após refatorar AdaptiveBitrateController.chooseLadder().
A suíte RegressionSuite deve reexecutar DrmLicenseService.issueLicense e
BillingService.accountPlaybackSecond contra golden files.
O objetivo é efeito colateral em DRM e faturamento, não retestar o ABR isolado.
```

### 4.4 Família Validação (3.1, 3.2, 3.3)

```text
Estes três diagramas respondem à pergunta de Boehm: "estamos construindo o produto certo?".
Não use linguagem de cobertura de código.

3.1 UAT — sequenceDiagram BDD. História: cancelamento com reembolso pro-rata.
Dado assinatura anual no dia 100/365; Quando titular confirma; Então CANCELED + crédito
round(precoAnual * 265/365, HALF_EVEN). Product Owner é ator do aceite.
AuthService.authorize(jwt, CANCEL_OWN_SUB) impede cancelar assinatura alheia.

3.2 Alfa — flowchart em STAGING. Atores: engenheiros, atendimento, QA interno.
Módulo Watch Party + WatchPartySignaling. Saída: go/no-go para Beta.
Proibido: usuários finais em produção.

3.3 Beta — flowchart para 1.000 usuários reais em Android, iOS e Smart TV.
Build 2.4.0-beta via TestFlight/Play Internal. Canário 5% no ApiGateway.
Telemetria (Sentry, QoE, feedback in-app). Ambiente NÃO controlado.
```

### 4.5 Família Sistema (4.1–4.4)

```text
Agora o SUT é o sistema + infraestrutura real (ou de laboratório equivalente).

4.1 Recuperação — sequenceDiagram. PlayerStreamer com checkpoint em SessionStore Redis.
FaultInjector envia kill -9 ao RedisPrimary. Sentinel promove RedisReplica.
Oráculo: resumeFrom(positionMs) igual ao último heartbeat; não voltar ao início.
Meça RTO do failover.

4.2 Segurança — sequenceDiagram. Três estímulos: JWT expirado, JWT com role adulterada
sem reassinatura, busca de catálogo com payload de injeção.
Oráculos: 401 em TokenExpiredException e InvalidSignatureException; busca sem dump.
Cite OWASP API1/API3/API8 apenas como classificação do objetivo, sem PoC explorável.

4.3 Estresse — flowchart. 500.000 VU versus capacidade nominal 100.000 req/s na estreia.
CircuitBreaker + throttling. Degradação aceitável: 429/503 + fallback 480p.
Inaceitável: OOM, deadlock, perda de writes de billing.

4.4 Desempenho — flowchart. Carga sustentada a 80% da capacidade.
SLO: p95 e p99 < 200 ms; throughput de chunks; taxa de erro < 0,1%.
Não use pico de estreia (isso é 4.3). Distinção explícita no texto.
```

---

## 5. Prompt de autoarguição crítica (defesa oral)

Usado depois dos diagramas, para gerar perguntas que **atacam** o próprio trabalho. A banca de VVTS não pergunta “o que é stub?”; pergunta se o aluno sabe por que aquele stub está naquele nível.

```text
Aja como o professor Maxwell Amaral em banca de 12 minutos. Não elogie o relatório.
Para cada um dos 13 cenários da StreamPulse, formule:

A. uma pergunta que distinga verificação de validação naquele cenário;
B. uma pergunta que force a cadeia erro → defeito → falha com nomes de classe da StreamPulse;
C. uma pergunta armadilha (ex.: "por que smoke não é teste de sistema?",
   "por que Beta não é Alfa remoto?", "por que estresse não é desempenho com mais usuários?").

Em seguida, responda cada pergunta em no máximo 8 linhas, no vocabulário da apostila.
Sinalize onde o diagrama atual seria insuficiente na lousa (por exemplo, Big Bang sem
arestas para o EventBus). Produza também 10 perguntas relâmpago de fechamento.
```

As perguntas e respostas-síntese estão na seção 6 do `README.md` (defesa oral) e na seção 6 deste arquivo.

---

## 6. Autoarguição — perguntas que o artefato precisa sobreviver

Registro das perguntas efetivamente usadas para revisar o `README.md` antes da publicação. Cada item gerou pelo menos um ajuste de diagrama ou de texto.

1. **1.1** Se `CalculadoraTaxaTranscodificacao` lesse preço em Redis, ainda seria unidade?  
   *Ajuste:* o texto declara isolamento total; preço é constante de classe, não I/O.
2. **2.1** Big Bang “integra tudo”: então por que não é teste de sistema?  
   *Ajuste:* o harness não injeta falha de infra nem mede SLO; o alvo é acoplamento de módulos.
3. **2.2** Stub e mock são sinônimos?  
   *Ajuste:* o stub implementa `ICdnEdgeRouter` com respostas pré-programadas; não há verificação de interação como em mock.
4. **2.3** Por que o `PlayerStreamer` aparece no diagrama se não é exercitado?  
   *Ajuste:* box “ainda não construídos” torna visível o que o driver está substituindo.
5. **2.4** Três rotas bastam para qualidade?  
   *Ajuste:* o texto afirma que fumaça é portão, não evidência de aceitação.
6. **2.5** Se a refatoração é no ABR, por que testar DRM?  
   *Ajuste:* o diagrama liga `pumpBuffer()` a `issueLicense` e `accountPlaybackSecond`.
7. **3.1** UAT que passa prova que o código está correto?  
   *Ajuste:* UAT valida necessidade de negócio; verificação de cálculo pro-rata também existe em unidade, mas o aceite é do PO.
8. **3.2** Funcionário interno em staging é Beta?  
   *Ajuste:* Alfa = ambiente do desenvolvedor/homologação; Beta = dispositivo e rede do usuário.
9. **3.3** Telemetria de crash substitui caso de teste?  
   *Ajuste:* Beta produz falhas de campo; não substitui oráculos de 3.1.
10. **4.1** `kill -9` no Redis é teste de unidade do `SessionStore`?  
    *Ajuste:* é recuperação de sistema; o oráculo é continuidade do playback.
11. **4.2** Por que 401 em token adulterado não “prova segurança”?  
    *Ajuste:* o texto recupera a falácia do software 100% seguro (apostila, Schneier).
12. **4.3 / 4.4** Qual experimento rodaria primeiro com orçamento de uma noite?  
    *Ajuste:* desempenho no envelope nominal; estresse só depois do baseline de 4.4.

---

## 7. Prompt de consistência UML e vocabulário

Última passagem, inspirada na lição de diagrama de classes da disciplina.

```text
Revise todos os classDiagram e trechos UML do README.

- Atributos: nome : tipo
- Métodos: nome(param: tipo) tipoRetorno
- Visibilidade: + público, - privado, # protegido
- Interfaces com <<interface>> e nome iniciando em I
- Serviços com <<service>>
- Exceções com <<exception>>
- Enumerações com <<enumeration>>
- Stubs e drivers com estereótipo no compartimento superior, não apenas na nota

Substitua qualquer uso de "erro" quando o fenômeno for falha observável.
Substitua qualquer "validar o módulo" em teste de unidade por "verificar o módulo".
Não trate Pressman (erro/defeito temporais) como contradição: declare a convenção causal
da disciplina e cite a nota da Aula 03 da apostila.
```

---

## 8. Ferramentas e parâmetros de geração

| Item | Valor usado |
| --- | --- |
| Persona | Arquiteto + QA/VVTS |
| Temperatura conceitual | Baixa: nomenclatura travada; exemplos numéricos fixos (100/365, 200 ms, 100k vs 500k) |
| Oráculo visual | Papéis em `box` (sequência) ou `subgraph` (fluxo) |
| Verificação de renderização | Sintaxe Mermaid 10+ compatível com GitHub |
| Materiais de ancoragem | `apostila-testes.pdf`; `Diagrama de classes - maxclass_it.pdf` |
| Não-objetivos | Código de produção; PoC de exploit; commits ou push sem pedido explícito |

---

## 9. Como reproduzir este laboratório com outro domínio

1. Troque apenas o **contexto** (persona e critérios permanecem).
2. Rode o prompt de arquitetura até fechar a lista de classes e interfaces.
3. Rode os prompts de família (unidade → integração → validação → sistema), nunca os treze de uma vez.
4. Rode a autoarguição e **altere o diagrama** quando a resposta não couber nele.
5. Publique `README.md`, `prompts_utilizados.md` e `diagramas/*.mmd` no repositório da disciplina.

Essa ordem é ela própria uma estratégia de teste: incrementos com dublês, depois o sistema, depois o usuário.
