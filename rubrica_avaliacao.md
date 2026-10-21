# Rubrica de avaliação — Projeto Final
### DS-PY-018 · Análise Exploratória de Dados · App de delivery (`delivery_pedidos.csv`)


**Como ler:** cada critério tem 4 níveis. Para chegar a um nível, o trabalho precisa cumprir o descritor dele **e** o dos níveis abaixo. "Avançado" não é "mais coisas": é **mais julgamento** (escolher, justificar, desconfiar).

| Nível | Pontos | Em uma frase |
|---|---|---|
| Insuficiente | 0 | ausente, errado, ou feito sem entender |
| Em desenvolvimento | 1 | feito de forma mecânica, com erros ou lacunas importantes |
| Proficiente | 2 | correto, completo e interpretado |
| Avançado | 3 | correto, justificado, com senso crítico e foco no negócio |

---

## 1. Rubrica analítica

### 1.1 Uso de DataFrames no Pandas — peso 8

| Nível | Descritor observável neste projeto |
|---|---|
| **0 Insuficiente** | Não consegue carregar ou inspecionar a base; usa laços `for` para calcular; edita o CSV à mão. |
| **1 Em desenvolvimento** | Carrega e inspeciona, mas com laços para filtrar/calcular, atribuição encadeada (`df[m]["col"] = x`), `SettingWithCopyWarning` ignorado, ou nomes de coluna digitados à mão onde caberia código (ex.: `drop(columns=["pais", "moeda"])` sem detectar por `nunique`). |
| **2 Proficiente** | Tudo vetorizado: `.loc` com máscara, `.str`, `map`/`replace`, `to_numeric`, `to_datetime`, `.dt`; cria colunas derivadas (`hora`, `dia_semana`, flags); trabalha numa cópia e preserva o bruto; passa nos checkpoints. |
| **3 Avançado** | Código idiomático e legível: `assign`/encadeamento, `transform` para comparar cada pedido com o seu grupo (ex.: razão sobre a mediana da cozinha), `Categorical` ordenado para dias da semana, `.map` em vez de `.replace` para **detectar** variantes esquecidas; funções reutilizáveis (ex.: um `qui_quadrado(a, b)`); nenhum warning. |

### 1.2 Tratamento de dados (qualidade e outliers) — peso 9

| Nível | Descritor observável neste projeto |
|---|---|
| **0** | Não percebe a sujeira: `cidade` com 13 valores, `valor_pedido` como texto, `999` no tempo de entrega entram na análise. |
| **1** | Trata parte dos problemas; remove linhas em massa sem contar o impacto (ex.: `dropna()` geral, apaga as ~1.000 linhas com cidade "suja"); preenche `avaliacao` com a média sem discussão; remove todos os "outliers" do IQR. |
| **2** | Cidade (3) e pagamento (4) canônicos; `valor_pedido` numérico **sem NaN**; constantes removidas; 999/negativos e distâncias > 100 km tratados como erro **com contagem**; faltantes tratados com justificativa escrita; pedidos corporativos **mantidos** e identificados. |
| **3** | Distingue **erro × extremo legítimo** com evidência (salto na distribuição, coerência entre distância/tempo/taxa, dispersão por cidade e mês); cria **flags** em vez de apagar; investiga se o faltante de avaliação é aleatório; mostra **por que** o IQR global falha (marca Japonesa como outlier); bônus: recupera `distancia_km` pela regra da taxa. |

### 1.3 Entendimento de estatísticas descritivas — peso 8

| Nível | Descritor observável neste projeto |
|---|---|
| **0** | Só imprime `describe()`, sem nenhuma leitura. |
| **1** | Calcula média, mediana e desvio, mas interpreta errado (ex.: "o ticket médio de R$ 72 representa o cliente típico") ou não percebe que o `describe()` do tempo está contaminado por `999`. |
| **2** | Calcula e interpreta tendência central, dispersão, quartis/IQR, CV e assimetria; escolhe **mediana** para o ticket e explica por quê; usa percentis para o tempo de entrega (ex.: "10% passam de 50 min"). |
| **3** | Usa a estatística para tomar decisões: mostra o impacto dos pedidos corporativos na média (~3%) vs. mediana (centavos); percebe que a avaliação é concentrada em 4–5 e, por isso, quedas de 0,3 ponto importam; lê a moda da taxa (R$ 0) como sinal do frete grátis. |

### 1.4 Sumarização de dados — peso 8

| Nível | Descritor observável neste projeto |
|---|---|
| **0** | Nenhum `groupby`; ou tabelas sem relação com as perguntas. |
| **1** | `groupby` com uma métrica por vez; tabelas sem ordenação, sem nomes legíveis, ou com colunas de texto quebrando a agregação. |
| **2** | `groupby` com várias chaves e várias métricas (*named aggregation*), `pivot_table` (hora × dia), `crosstab` com percentual por linha; tabelas ordenadas e com nomes claros; responde por cidade, cozinha, plataforma, cupom e mês. |
| **3** | Sumarizações que **comparam o que é comparável**: participação no volume vs. no faturamento por cozinha; % de dias com chuva calculado sobre cidade-dia (não por pedido); tabela-resumo das hipóteses pronta para o README. |

### 1.5 Cálculo de métricas — peso 8

| Nível | Descritor observável neste projeto |
|---|---|
| **0** | Métricas ausentes ou erradas (ex.: soma de `valor_pedido` como texto, ticket médio com `999` incluído). |
| **1** | Calcula KPIs básicos (pedidos, faturamento, ticket médio), mas sem definição clara ou misturando conceitos (taxa de entrega somada ao ticket sem avisar). |
| **2** | Painel de KPIs completo e definido: pedidos, faturamento, ticket médio **e** mediano, % cupom, % clientes novos, % frete grátis, tempo médio, % acima de 45 min, avaliação média, % avaliados; variação % (cupom vs. sem cupom, julho vs. jun/ago). |
| **3** | Métricas **normalizadas e de negócio**: pedidos **por dia** (não total) para comparar meses e dias da semana; crescimento anual quantificado por dois métodos e discutido; "% de entregas em até 35 min" proposto como métrica operacional; tamanhos de efeito em unidade de negócio (min, R$, %). |

### 1.6 Escolha adequada de visualizações — peso 8

| Nível | Descritor observável neste projeto |
|---|---|
| **0** | Gráficos que não respondem a nenhuma pergunta, ou tipo inadequado (pizza com 6 cozinhas, linha ligando categorias sem ordem). |
| **1** | Tipos razoáveis, mas sem pergunta clara; scatter com 8.000 pontos opacos; histograma de variável categórica; série diária sem suavização onde a tendência importa. |
| **2** | Cada gráfico responde a uma pergunta e o tipo é adequado: histograma (ticket), barras (participação), boxplot ordenado (cozinha), linha (série temporal), heatmap (hora × dia), densidades/boxplot (chuva × tempo), pontos por faixa (tempo × avaliação). |
| **3** | Justifica cada escolha (Tarefa 7.9); usa a granularidade visual certa (média móvel; faixas de tempo que revelam o "joelho" em 35 min); combina painéis quando há duas perguntas ligadas; a **figura principal** resume a história em um olhar. |

### 1.7 Estilização de visualizações — peso 9

| Nível | Descritor observável neste projeto |
|---|---|
| **0** | Gráficos padrão sem título, sem rótulos, com eixos ilegíveis. |
| **1** | Título descritivo ("Valor por cozinha") e rótulos, mas sem unidade; eixos com `1e6` ou `50.0`; cores aleatórias que mudam de significado entre gráficos; legenda cobrindo dados. |
| **2** | **Título-conclusão** em todos os gráficos; rótulos com unidade (R$, min, %); eixos formatados; ordenação com sentido; tamanho legível; paleta consistente. |
| **3** | Padrão visual definido uma vez e reutilizado (cores, formatadores, função de título); **destaque** com cor apenas no que importa (Japonesa/Doces, julho) e cinza no resto; anotações no próprio gráfico (14/08, campanha, mediana); subtítulo com fonte/período/base; rótulos diretos em vez de legenda; números no padrão brasileiro. |

### 1.8 Validação de hipóteses — peso 13

| Nível | Descritor observável neste projeto |
|---|---|
| **0** | Sem testes, ou conclusões só "pelo gráfico". |
| **1** | Roda testes, mas sem H0/H1, com teste inadequado (Pearson para avaliação ordinal sem discussão; t para 6 grupos), ou interpreta p-valor como "probabilidade de H1 ser verdade"; trata a H5 não significativa como "prova de independência". |
| **2** | H1–H5 + H6 com H0/H1, teste adequado ao tipo de variável (Welch/Mann-Whitney, Spearman, Kruskal-Wallis, qui-quadrado), p-valor e decisão a 5%; reporta corretamente a H5 como **não significativa**; frase de negócio para cada uma. |
| **3** | Reporta **tamanho de efeito** em unidade de negócio e padronizado (d de Cohen, ε², V de Cramér); discute que com n ≈ 8.000 quase tudo é significante; mostra por que o t no valor bruto é frágil (log, não paramétrico); H6 própria **com controle de confundidora** (ex.: estratos cidade-mês) ou na unidade de análise correta (cidade-dia). |

### 1.9 Uso de granularidade adequada — peso 9

| Nível | Descritor observável neste projeto |
|---|---|
| **0** | Tudo no nível do pedido; nenhuma agregação temporal. |
| **1** | Agrega por mês ou cidade, mas compara totais de grupos de tamanhos diferentes (meses de 29 vs. 31 dias; 53 segundas vs. 52 sextas; total de pedidos em dias com vs. sem chuva). |
| **2** | Usa dia, semana, mês e hora × dia da semana; identifica o dia anômalo (14/08) na série diária; compara volume por **pedidos por dia**; analisa chuva por cidade-dia. |
| **3** | Explica **o que cada granularidade revela e esconde** (14/08 some no mês; última semana incompleta; julho só aparece no mês); escolhe a unidade de análise do teste conforme a pergunta (pedido para tempo de entrega, cidade-dia para volume); usa faixas de tempo para revelar o limiar de 35 min; detecta outlier dentro do grupo certo (cozinha). |

### 1.10 Publicação dos dados — peso 8

| Nível | Descritor observável neste projeto |
|---|---|
| **0** | Sem repositório, ou repositório sem README / com o notebook quebrado. |
| **1** | Repositório com notebook, mas README genérico (só o título); um único commit; caminhos absolutos da máquina do aluno (`C:\Users\...`); base limpa ausente. |
| **2** | README seguindo o modelo (pergunta, dados, como rodar, metodologia, achados com figura, limitações, autores); base limpa + dicionário em `saidas/`; vários commits com mensagens descritivas; notebook roda do início ao fim. |
| **3** | Outra pessoa reproduz tudo só com o README; histórico de commits conta a história do projeto (por etapa e por autor); README com tabela de hipóteses e limitações honestas; dicionário da base limpa explica as flags; figura principal no topo do README; `.gitignore` adequado. |

### 1.11 Comunicação e storytelling (conclusões + apresentação) — peso 12

| Nível | Descritor observável neste projeto |
|---|---|
| **0** | Sem conclusões, ou conclusões que contradizem os números calculados. |
| **1** | Lista de fatos sem hierarquia ("o ticket médio é R$ 71,55; a mediana é..."); recomendações genéricas ("melhorar a entrega"); apresentação lê o notebook célula a célula; estoura o tempo. |
| **2** | 5 achados com número, fonte e "por que importa"; 3 recomendações ligadas a achados, cada uma com métrica; ≥ 3 limitações; apresentação em 10 min com a figura principal; todos os membros falam e respondem perguntas. |
| **3** | Narrativa com começo-meio-fim para a diretoria (ex.: chuva → atraso → avaliação → plano de chuva); pelo menos uma **nuance** (H0 não rejeitada, confundidora, efeito pequeno apesar de significante); recomendações priorizadas e acionáveis em 2025; limitações que viram próximos passos (ex.: `cliente_id` para medir recompra do cupom). |

---

## 2. Pesos e cálculo da nota

| # | Critério | Habilidade da ementa | Peso |
|---|---|---|---|
| 1.1 | DataFrames no Pandas | uso de DataFrames no Pandas | 8 |
| 1.2 | Tratamento de dados (qualidade e outliers) | metodologia e reprodutibilidade | 9 |
| 1.3 | Estatísticas descritivas | entendimento de estatísticas descritivas | 8 |
| 1.4 | Sumarização | sumarização de dados | 8 |
| 1.5 | Cálculo de métricas | cálculo de métricas | 8 |
| 1.6 | Escolha de visualizações | escolha adequada de visualizações | 8 |
| 1.7 | Estilização de visualizações | estilização de visualizações | 9 |
| 1.8 | Validação de hipóteses | validação de hipóteses | 13 |
| 1.9 | Granularidade | uso de granularidade adequada | 9 |
| 1.10 | Publicação dos dados | publicação dos dados | 8 |
| 1.11 | Comunicação e storytelling | (objetivo do módulo: comunicar achados) | 12 |
| | **Total** | | **100** |

**Fórmula:**

```
pontuação (0–100) = Σ  peso × (nível / 3)
nota (0–10)       = pontuação / 10, arredondada a 0,5
```

**Exemplo:** um grupo Proficiente (2) em tudo, Avançado (3) em hipóteses e granularidade:
`(100 − 13 − 9) × 2/3 + (13 + 9) × 3/3 = 52 + 22 = 74 → nota 7,5`.

### Tabela de conversão

| Pontuação | Nota | Leitura |
|---|---|---|
| 95–100 | 10,0 | Avançado em quase tudo — referência para a turma |
| 85–94 | 9,0 – 9,5 | Proficiente em tudo, Avançado na maioria |
| 75–84 | 8,0 – 8,5 | Proficiente em tudo, Avançado em alguns critérios |
| 65–74 | 7,0 – 7,5 | Proficiente na maioria (≈ "tudo correto e interpretado") |
| 55–64 | 6,0 – 6,5 | Mistura de Proficiente e Em desenvolvimento |
| 45–54 | 5,0 – 5,5 | Em desenvolvimento na maioria |
| 30–44 | 3,0 – 4,5 | Lacunas importantes em vários critérios |
| 0–29 | 0 – 2,5 | Projeto incompleto ou não executável |

**Regras gerais:**
- Notebook que **não roda do início ao fim** limita os critérios 1.1 e 1.10 ao nível 1.
- Conclusão que **contradiz os números** do próprio notebook zera o achado correspondente e limita 1.11 ao nível 1.
- Critério 1.2 só passa de 1 se o checkpoint da limpeza (`assert`) passar sem ser alterado.
- Nota individual = nota do grupo, salvo divergência clara no histórico de commits **e** na ficha de avaliação entre pares (o professor conversa com o grupo antes de diferenciar).

---


## 3. Apresentação de 7 min

