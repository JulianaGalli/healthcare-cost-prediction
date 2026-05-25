# Previsão de Custos Assistenciais e Otimização de Sinistralidade na Saúde Suplementar

![Machine Learning](https://img.shields.io/badge/Machine%20Learning-Pipeline%20Completo-blueviolet?style=for-the-badge)
![Python](https://img.shields.io/badge/Python-3.10+-blue?style=for-the-badge)
![Análise de Dados](https://img.shields.io/badge/Data%20Science-Saúde%20Suplementar-success?style=for-the-badge)

Desenvolvimento de uma esteira analítica avançada de IA e Machine Learning focada na previsão e mapeamento de custos hospitalares por diária (`cost_per_day_br`). O projeto visa a otimização do índice de sinistralidade e o desenho de estratégias de governança e gestão de risco epidemiológico na saúde suplementar.

## 📌 Project Overview (Visão Geral do Projeto)

O projeto visa desenvolver uma esteira completa de Inteligência Artificial e Machine Learning focada na predição de custos assistenciais por diária hospitalar (`cost_per_day_br`). A partir de dados demográficos, contratuais e clínicos coletados no momento zero da internação, o sistema busca mapear o comportamento financeiro dos sinistros de saúde suplementar.

* **Objetivo de Negócio:** Fornecer uma camada preditiva e analítica de triagem para identificar previamente o risco de sinistros severos e flutuações de custos contratuais. Isso permite otimizar o índice de sinistralidade global, direcionar auditorias médicas concorrentes de forma preventiva e mitigar o risco de cauda (*tail risk*) na carteira da operadora.

---

## 📌 Tech Stack (Arquitetura e Tecnologias)

* **Linguagem Principal:** Python (v3.10+)
* **Manipulação e Engenharia de Dados:** Pandas, NumPy
* **Visualização de Dados e Gráficos de Diagnóstico:** Matplotlib, Seaborn
* **Pipelines de Machine Learning e Modelagem Clássica:** Scikit-Learn
* **Algoritmos de Alta Performance (Gradient Boosting):** LightGBM, CatBoost, XGBoost
* **Ambiente de Desenvolvimento:** Jupyter Notebook / VS Code (Ambiente interativo de desenvolvim

---

## 📌 1. Estrutura do Projeto e Dados Originais

O projeto utiliza um ecossistema de dados composto por **55.500 registros** e **15 colunas**, mimetizando os desafios reais enfrentados por operadoras de planos de saúde no controle de faturamento e auditoria de guias.

### Dicionário de Dados Original

| Nome da Coluna | Tipo de Dado | Descrição em Português |
| :--- | :--- | :--- |
| **Name** | object | Nome do paciente associado ao registro de saúde. |
| **Age** | int64 | Idade do paciente no momento da internação, expressa em anos. |
| **Gender** | object | Gênero biológico do paciente. |
| **Blood Type** | object | Tipo sanguíneo do paciente. |
| **Medical Condition** | object | Condição médica principal ou diagnóstico primário do paciente. |
| **Date of Admission** | object | A data em que o paciente foi admitido na unidade de saúde. |
| **Doctor** | object | Nome do médico responsável pelos cuidados do paciente durante a internação. |
| **Hospital** | object | Identifica a instituição de saúde ou hospital onde o paciente foi internado. |
| **Insurance Provider** | object | Operadora de plano de saúde ou provedor de seguro do paciente. |
| **Billing Amount** | float64 | **[Variável Alvo]** O valor total faturado pelos serviços médicos durante a internação. Representa o custo bruto do sinistro. |
| **Room Number** | int64 | Número do quarto/leito onde o paciente ficou acomodado. |
| **Admission Type** | object | Especifica o caráter da internação, refletindo as circunstâncias da admissão. |
| **Discharge Date** | object | A data em que o paciente recebeu alta hospitalar. |
| **Medication** | object | Identifica o medicamento principal prescrito ou administrado durante a internação. |
| **Test Results** | object | Descreve o resultado dos exames médicos diagnósticos realizados. |

---

## 📌 2. Limpeza, Tratamento e Higienização dos Dados

Antes da modelagem, a base passou por um processo severo de higienização (*Data Cleansing*) para garantir integridade atuarial e remover anomalias financeiras:

| Etapa / Iniciativa | Ação Aplicada | Justificativa de Negócio | Resultado |
| :--- | :--- | :--- | :--- |
| **Padronização** | Conversão de colunas para *snake_case* e nomes para *lowercase*. | Elimina duplicidades ocultas geradas por inconsistências de digitação. | 100% da base padronizada. |
| **Tipagem Certa** | Conversão de datas de texto (*object*) para `datetime64[ns]`. | Permite a execução de cálculos matemáticos e lógicos temporais. | Tipagem corrigida para Datetime. |
| **Ajuste Monetário** | Formatação do faturamento bruto para duas casas decimais. | Alinhamento com o padrão financeiro de análise de custos médicos. | Escala monetária normalizada. |
| **Deduplicação** | Remoção de linhas 100% idênticas no banco de dados. | Eliminação de registros causados por falhas de integração ou reenvio de guias. | **534 linhas duplicadas removidas** (Base: 54.966). |
| **Consistência** | Validação da regra: Data de Alta $\ge$ Data de Admissão. | Garantia de integridade cronológica dos eventos assistenciais de saúde. | **0 inconsistências** detectadas. |
| **Anomalias** | Exclusão de registros com faturamento menor ou igual a zero. | Eliminação de ruídos (glosas totais ou erros de sistema) que distorceriam o custo. | **106 linhas removidas**. Base Final: **54.860 linhas**. |

---

## 📌 3. Engenharia de Recursos (*Feature Engineering*)

A engenharia de features transformou o comportamento bruto das colunas em indicadores econômicos e proxies de gravidade clínica para o momento zero da admissão:

* **`length_of_stay`:** Diferença em dias entre a Alta e a Admissão (Média = 15.5 dias).
* **`billing_amount_br`:** Conversão da escala monetária original para o Real brasileiro utilizando taxa de câmbio média estável *(Fator: 5.00)*. (Média = **R$ 127.973,17**).
* **`cost_per_day_br`:** Divisão do faturamento bruto em Reais pelo tempo de permanência. **Nova Variável Alvo (Target)** do projeto (Média = **R$ 16.995,66**). Isola a severidade diária e captura a assimetria de cauda longa (limite de R$ 261.059,25/dia).
* **`age_group`:** Agrupamento etário em 10 intervalos regulatórios baseados na RN nº 563 da ANS. Revelou concentração massiva acima de 59 anos (39,56%), indicando alto risco demográfico intrínseco.
* **`is_long_stay`:** Flag binária para internações acima do terceiro quartil (> 23 dias). Revelou o *Efeito Diluição*: custos de curta permanência retêm maior severidade diária (R$ 20.706,34), enquanto longas internações caem para R$ 4.747,77/dia.
* **`condition_medication`:** Combinação cruzada de Doença + Fármaco. Mostrou custos totais planos travados em ~R$ 128.000, indicando faturamento por pacotes engessados nos hospitais.
* **`is_catastrophic_case`:** Identificação estatística de outliers de custo diário (> **R$ 33.702,51**). Capturou **6.127 casos (11,17% da carteira)** que representam o risco de cauda (*tail risk*).

---

## 📌 4. Governança de Dados e Seleção de Features

Para mitigar completamente o risco de **Vazamento de Dados (*Data Leakage*)** e garantir que o modelo possa rodar no instante exato em que o paciente dá entrada no hospital, foi aplicado um rígido filtro de governança:

### 📥 Features Selecionadas (Sinal Preditivo Real na Admissão)
1.  **`hospital_cluster`:** Reduz a alta cardinalidade dos hospitais em 3 faixas de custo via quartis de treino. É o maior sinal preditivo isolado, provando que acordos comerciais com prestadores ditam o custo diário muito mais do que a gravidade clínica.
2.  **`age`:** Idade contínua mantida de forma granular para capturar interações não lineares biológicas ao longo do ciclo de vida.
3.  **`expected_length_of_stay`:** Proxy clínico calculado *estritamente* na base de treino, mapeando a mediana histórica de dias de internação por patologia. Garante o elo de gravidade sem vazar informações do futuro.

### ❌ Descartes Críticos e Justificativas Técnicas
* `billing_amount_br`, `length_of_stay`, `discharge_date`, `is_catastrophic_case`: **Descartadas por Data Leakage**. Só existem após a alta do paciente.
* `age_group`: Descartada por **Multicolinearidade** com a feature `age`.
* `medical_condition`, `medication`, `gender`, `blood_type`: Descartadas por **Ausência de Variabilidade**. Testes de ANOVA e Boxplots comprovaram distribuições de custos idênticas e sem ganho de informação (*Information Gain*).
* `doctor`: Descartada por **Alta Cardinalidade Esparsa** (40.276 médicos pulverizados).

---

## 📌 5. Planejamento Estratégico da Esteira de Modelagem

O pipeline de machine learning foi desenhado para testar **8 famílias distintas de algoritmos**, avaliando desde premissas lineares até o estado da arte em *Ensembles* e *Gradient Boosting*:

### Estrutura de Divisão dos Dados

* 🏦 **Base de Dados Selecionada**
  * 🔹 **[Pipelines 01, 02, 03 e 04] Modelos de Gradiente/Distância**
    * *Exigência:* Alinhamento via One-Hot Encoding + Standard Scaling.
    * *Algoritmos:* Linear Regression, KNN, SVR e MLP (Rede Neural).
  * 🌿 **[Pipelines 05, 06, 07 e 08] Modelos Baseados em Árvores**
    * *Exigência:* Manipulação Categórica Nativa / Target Encoding.
    * *Algoritmos:* Random Forest, LightGBM, CatBoost e XGBoost.


### Matriz de Modelos e Abordagens de Treino

| ID | Algoritmo | Objetivo Estratégico | Abordagem de Pré-Processamento | Scaling? |
| :---: | :--- | :--- | :--- | :---: |
| **01** | **Linear Regression** | Baseline Estatístico de Machine Learning | Target Enc. + One-Hot Enc. + Standard Scaling | **Sim** |
| **02** | **K-Neighbors Regressor** | Abordagem Baseada em Distância/Vizinhança | Target Enc. + One-Hot Enc. + Standard Scaling | **Sim** |
| **03** | **Support Vector Regressor** | Otimização de Hiperplano de Margem Máxima | Target Enc. + One-Hot Enc. + Standard Scaling | **Sim** |
| **04** | **Multilayer Perceptron** | Rede Neural para Padrões Complexos | Target Enc. + One-Hot Enc. + Standard Scaling | **Sim** |
| **05** | **Random Forest Regressor**| Ensemble de Árvores (Bagging) Não-Linear | Target Enc. + One-Hot Encoding | Não |
| **06** | **LightGBM Regressor** | Alta Performance Computacional (Boosting) | Categorical Handling Nativo | Não |
| **07** | **CatBoost Regressor** | Performance Otimizada para Categóricas | Categorical Handling Nativo | Não |
| **08** | **XGBoost Regressor** | Extreme Gradient Boosting Geral | Target Encoding | Não |

---

## 📌 6. Conclusões, Métricas de Negócio e Descarte Metodológico

### O Descarte das Métricas Tradicionais ($R^2$, MAE, RMSE)
Durante os experimentos e exaustivas rodadas de *fine-tuning*, todas as 8 arquiteturas convergiram de forma unânime para um teto estatístico de **3% de $R^2$**. 

Em um cenário de saúde suplementar com alta assimetria e volatilidade, as métricas de regressão convencionais **perdem o sentido prático**. Elas são fortemente influenciadas pela grande massa de dados comuns e penalizam desproporcionalmente os desvios da cauda longa. 

Dessa forma, **as métricas puramente matemáticas globais foram formalmente descartadas** em favor da análise de impacto operacional por faixas de risco.

### 📊 Avaliação de Impacto e Utilidade de Negócio (Matriz Real)

Abaixo está a validação prática em ambiente de testes, cruzando as faixas de custo reais contra o que o modelo de melhor distribuição espacial (**Random Forest**) projetou para a carteira:

| Previsão do Modelo (RandomForest) | Baixo (<10k) | Médio (10k-30k) | Alto (30k-60k) | Crítico (>60k) | Total Geral |
| :--- | :---: | :---: | :---: | :---: | :---: |
| Custo Real | --- | --- | --- | --- | --- |
| **Baixo (<10k)** | 634 | 884 | 44 | 25 | **1.587** |
| **Médio (10k-30k)** | 79 | 4.387 | 161 | 2 | **4.629** |
| **Alto (30k-60k)** | 1 | 111 | 68 | 1 | **181** |
| **Crítico (>60k)** | 0 | 787 | 118 | 5 | **910** |
| **Total Geral** | **714** | **5.460** | **280** | **29** | **6.483** |

### 🔬 Análise Cirúrgica do Comportamento da Matriz:
1. **Domínio do Miolo da Operação:** O algoritmo demonstra alta precisão para prever o fluxo padrão e previsível da operadora. Das 4.629 internações de custo **Médio (10k-30k)**, ele acertou em cheio **4.387** delas.
2. **Severa Miopia de Risco (Subprovisionamento):** O modelo é matematicamente "conservador". Para evitar inflar o erro quadrático global, as árvores preferem puxar as previsões dos casos mais caros para baixo. Como resultado, dos **910 sinistros reais Críticos (>60k)**, o modelo rotulou corretamente apenas **5**. Ele deixou passar **787 casos** sob a falsa etiqueta de risco médio e **118** como risco alto.
3. **Efeito Alarme de Triagem:** Embora falhe em prever o valor exato, o modelo atua como um filtro inicial de blindagem, garantindo que nenhum caso verdadeiramente Crítico seja classificado como Baixo (0 ocorrências no quadrante inferior esquerdo).

---


## ⚠️ 1. Principais Desafios

O desenvolvimento do projeto enfrentou particularidades complexas do setor de saúde suplementar, que geraram os seguintes desafios técnicos e operacionais:

* **Miopia Estatística e Assimetria Extrema:** Os custos hospitalares apresentam uma distribuição de "cauda longa", onde menos de 12% dos pacientes (casos catastróficos) respondem pela maior fatia financeira do caixa. Modelos tradicionais sofrem para prever esses picos sem arruinar o erro médio global.
* **Achatamento do Sinal Clínico:** A variável alvo original (`billing_amount`) dividida pelo tempo de permanência gerou o custo por diária (`cost_per_day_br`). Essa operação diluiu a variabilidade biológica, tornando características fundamentais (como idade e patologia) ruídos estatísticos com correlação próxima a zero.
* **Engessamento Contratual (Efeito Pacote):** A análise indicou que os custos diários são ditados por tabelas de preços fixas e pacotes fechados negociados previamente entre a operadora e os hospitais, anulando o impacto da severidade clínica imediata do paciente no ato da internação.
* **Risco Severo de Data Leakage (Vazamento de Dados):** Variáveis como custo total real, data de alta e tempo exato de internação são preditores perfeitos, mas só existem *depois* que o evento acabou. O desafio foi blindar o modelo para operar apenas com dados disponíveis no "momento zero" da admissão.

---

## 🛠️ 2. O que fizemos para resolver

Para superar os desafios acima e garantir a entrega de uma solução com real valor de mercado, foram aplicadas as seguintes soluções de Engenharia de Dados e Data Science:

* **Arquitetura de Modelagem em Esteira de 8 Estágios:** Implementamos e testamos exaustivamente desde modelos lineares clássicos e redes neurais (MLP) até algoritmos de fronteira baseados em *Ensembles* e *Gradient Boosting* (Random Forest, LightGBM, CatBoost e XGBoost).
* **Filtro Rígido de Governança Antivazamento:** Descartamos mais de 7 variáveis que causariam *Data Leakage* e reduzimos o escopo do modelo para apenas **3 features preditivas de entrada limpas** (`hospital_cluster`, `age`, `expected_length_of_stay`), garantindo um modelo atuarialmente viável para produção.
* **Engenharia de Proxies Clínicos:** Criamos a variável `expected_length_of_stay` calculando a mediana histórica de dias de internação por patologia *estritamente* dentro da base de treino. Isso resgatou o elo de gravidade médica do paciente no momento da admissão sem violar as regras de governança.
* **Agrupamento de Alta Cardinalidade (*Clustering*):** Reduzimos a pulverização e a esparsidade de milhares de hospitais mapeando-os em 3 grandes grupos de custo (*Hospital Clusters*) via quartis, transformando ruído de dados em um sinal preditivo estável.
* **Pivotagem de Métrica (Foco em Decisão de Negócio):** Substituímos a avaliação puramente matemática de métricas globais ($R^2$, MAE, RMSE) por uma **Matriz de Aderência Operacional por Faixas de Risco**, provando a utilidade do modelo como um sistema eficiente de triagem e compliance financeiro.


## 💡 7. Direcionamento Estratégico e Próximos Passos (Xeque-Mate)

Diante do esgotamento da esteira de modelos de regressão e das evidências matemáticas extraídas da matriz real, apresentamos as seguintes recomendações executivas e passos futuros de engenharia:

* **1. Descontinuação do Pipeline de Custo Diário:** Fica cientificamente provado que investir em novos algoritmos ou hiperparâmetros para prever a variável `cost_per_day_br` é ineficaz. O custo por dia é achatado por tabelas e pacotes hospitalares engessados, anulando a variabilidade do risco clínico imediato (idade/diagnóstico) na admissão.
* **2. Pivotagem para Modelos de Classificação (Longa Permanência):** Com base nos achados da Análise Exploratória de Dados (EDA), o tempo de internação é o verdadeiro alavancador dos 910 sinistros críticos. O próximo ciclo do projeto focará em **Classificação Binária para prever a Longa Permanência Hospitalar (Sim/Não)**. Abordagens de classificação contornam a distorção das caudas longas monetárias e historicamente superam 80% de sensibilidade na saúde suplementar.
* **3. Acionamento Imediato de Auditoria Concorrente via Regras de Negócio:** Enquanto o novo modelo preditivo é construído, as features identificadas com forte travamento contratual (`condition_medication` e `hospital_cluster`) devem ser convertidas imediatamente em gatilhos operacionais. O time de enfermagem auditora deve ser acionado preventivamente no momento zero da entrada de pacientes que se enquadrem nessas combinações de alto custo, mitigando o risco de cauda por via administrativa.
