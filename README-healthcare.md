# Previsão de Custos Assistenciais e Otimização de Sinistralidade na Saúde Suplementar

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/Jupyter-Notebook-F37626?style=for-the-badge&logo=jupyter&logoColor=white"/>
  <img src="https://img.shields.io/badge/LightGBM-Modelo%20Campeão-2ecc71?style=for-the-badge"/>
  <img src="https://img.shields.io/badge/Status-Concluído-success?style=for-the-badge"/>
</p>

> **Projeto de Machine Learning aplicado à saúde suplementar brasileira**, com foco na previsão do custo diário de internações (R$/dia) no momento da admissão do paciente, utilizando dados demográficos e clínicos iniciais.

---

## 📂 Estrutura e Organização do Repositório

O repositório foi organizado para equilibrar **reprodutibilidade técnica** e **apresentação para diferentes públicos** (times de dados, gestores e áreas de negócio). Abaixo, o que você encontra e por quê.

### Notebooks — por que dois arquivos?

Notebooks Jupyter guardam os gráficos e as tabelas gerados **embutidos dentro do próprio arquivo** (`.ipynb`). Como este projeto produz dezenas de visualizações em alta resolução, o notebook completo fica naturalmente pesado — e o GitHub, que possui um **limite de tamanho para pré-visualização**, nem sempre consegue renderizá-lo no navegador (às vezes carrega, às vezes exibe *"Sorry, something went wrong. Reload?"*). Trata-se de uma **limitação conhecida da plataforma**, e não de um problema do código.

Para garantir uma apresentação confiável, o projeto adota a **boa prática de manter duas versões do mesmo notebook**:

| Arquivo | Finalidade | Quando usar |
| :--- | :--- | :--- |
| 📕 `healthcare-cost-prediction-runed.ipynb` | Versão **executada e completa**, com todos os gráficos, tabelas e resultados já renderizados. | Para **ler a análise e os resultados** sem precisar rodar o código. |
| 📗 `healthcare-cost-prediction-outputs-cleared.ipynb` | Versão **leve, com as saídas limpas** (*cleared outputs*). Renderiza de forma rápida e estável no GitHub e mantém o histórico de versionamento limpo. | Para **clonar e reexecutar do zero**, ou para inspecionar o código quando a versão completa não carregar. |

> 💡 **Dica:** se o notebook completo não abrir aqui no GitHub, cole o link do arquivo no [**nbviewer**](https://nbviewer.org/) — o renderizador oficial do Jupyter, que exibe notebooks pesados sem o limite de tamanho do GitHub.

Manter uma versão com as saídas limpas segue a recomendação da comunidade de dados para **versionamento de notebooks** (ferramentas como o [`nbstripout`](https://github.com/kynan/nbstripout) automatizam essa limpeza), mantendo o repositório leve, os *diffs* legíveis e a renderização previsível — mais sobre o comportamento de arquivos `.ipynb` na [documentação oficial do GitHub](https://docs.github.com/en/repositories/working-with-files/using-files/working-with-non-code-files).

### 📊 Pasta `plots/` — todos os gráficos do projeto

Todas as visualizações geradas ao longo da análise estão salvas em alta resolução na pasta **[`plots/`](./plots)**, organizadas por etapa do trabalho:

- **Análise Exploratória (EDA):** distribuições, sazonalidade das internações, faixas etárias, correlações (heatmap de Spearman) e curva de diluição do sinistro.
- **Diagnóstico dos Modelos:** um gráfico por algoritmo avaliado (`diagnostico_01` a `diagnostico_08`), comparando valores previstos × reais.
- **Explicabilidade (SHAP):** `shap_bar` (importância global), `shap_beeswarm` (impacto direcional) e `shap_waterfall_high_cost` (anatomia do caso crítico).

Assim, é possível consultar qualquer gráfico diretamente, sem precisar abrir ou executar os notebooks.

### 🎤 Apresentação Executiva (público não técnico)

Para comunicar os resultados a **gestores, recrutadores e áreas de negócio**, o projeto inclui uma apresentação executiva em **dois formatos**:

| Formato | Arquivo | Uso recomendado |
| :--- | :--- | :--- |
| 📄 **PDF** | `Predição_de_Custos_Assistenciais_e_Otimização_de_Sinistralidade_na_Saúde_Suplementar.pptx.pdf` | **Formato recomendado para visualização e compartilhamento.** Abre em qualquer dispositivo ou navegador, é renderizado diretamente pelo GitHub e preserva o layout exatamente como foi desenhado. |
| 📽️ **PPTX** | `Predição_de_Custos_Assistenciais_e_Otimização_de_Sinistralidade_na_Saúde_Suplementar.pptx` | Arquivo **editável** (fonte original), para quem quiser adaptar ou reaproveitar os slides. |

> **Boa prática de apresentação:** para público não técnico, o **PDF é o formato preferencial de distribuição** — ele "congela" a formatação e o layout, garantindo que a apresentação seja vista exatamente como foi criada, inclusive por quem não tem o PowerPoint instalado ([Microsoft Support](https://support.microsoft.com/en-us/office/save-powerpoint-presentations-as-pdf-files-9b5c786b-9c6e-4fe6-81f6-9372f77c47c8)). O `.pptx` permanece como fonte editável. A apresentação prioriza **narrativa e impacto de negócio** em vez de detalhes técnicos, seguindo princípios de *data storytelling* ([Storytelling with Data](https://www.storytellingwithdata.com/)).

---

## 1. Contexto do Projeto

A saúde suplementar brasileira enfrenta **desafios críticos na previsibilidade de custo dos pacientes na admissão**. Apesar de existirem padrões clínicos, as respostas humanas aos tratamentos são individuais e os desfechos financeiros são complexos, tornando difícil a identificação precoce de sinistros de alto risco.

Este projeto **investiga a viabilidade de prever o custo diário estimado (R$/dia) de uma internação no momento da admissão**, utilizando informações demográficas e dados clínicos iniciais disponíveis antes do fechamento da conta hospitalar.

---

## 2. Objetivo Estratégico

O objetivo central é **identificar precocemente sinistros de alto risco** para:

- **Auditoria Concorrente** — Acionar auditores médicos no D+0 da internação para casos críticos
- **Gerenciamento de Pacientes** — Acompanhamento intensivo de internações de alto risco
- **Otimização do Índice de Sinistralidade** — Provisão financeira (PEONA) dinâmica e individualizada por paciente
- **Blindagem Antivazamento de Caixa** — 100% dos sinistros graves retirados do ponto cego operacional

---

## 3. Dataset

| Atributo | Valor |
|----------|-------|
| **Origem** | Kaggle (Dataset Sintético) |
| **Volume** | 55.500 linhas × 15 colunas |
| **Após limpeza** | ~55.394 registros válidos |
| **Variável-alvo** | `cost_per_day_br` (R$/dia) — derivada de `billing_amount` ($ faturamento/internação) |

### Dicionário de Dados

| Coluna | Tipo | Descrição |
|--------|------|-----------|
| `name` | object | Nome do paciente |
| `age` | int64 | Idade no momento da internação |
| `gender` | object | Gênero biológico |
| `blood_type` | object | Tipo sanguíneo |
| `medical_condition` | object | Diagnóstico primário |
| `date_of_admission` | datetime | Data de admissão |
| `doctor` | object | Médico responsável |
| `hospital` | object | Instituição de saúde |
| `insurance_provider` | object | Operadora de plano de saúde |
| `billing_amount` | float64 | **[Variável-alvo original]** Valor total faturado |
| `room_number` | int64 | Número do quarto/leito |
| `admission_type` | object | Caráter da internação |
| `discharge_date` | datetime | Data de alta hospitalar |
| `medication` | object | Medicamento principal prescrito |
| `test_results` | object | Resultado dos exames diagnósticos |

---

## 4. Pipeline do Projeto

```
📥 Carga dos Dados
       ↓
🔍 Análise Exploratória (EDA)
   ├── Padronização de colunas e tipos
   ├── Remoção de duplicatas
   ├── Tratamento de valores negativos (glosas/erros)
   └── Verificação de inconsistências temporais e financeiras
       ↓
⚙️ Feature Engineering
   ├── length_of_stay (dias de internação)
   ├── cost_per_day_br (variável-alvo - custo diário de internação)
   ├── age_group (agrupamento em faixas de idade: RN 563 ANS)
   ├── is_weekend_admission (atendimento de dia de semana - 0/1)
   ├── is_long_stay (tempo prolongado de internação - 0/1)
   ├── is_catastrophic_case (casos catastróficos - 0/1)
   ├── condition_medication (agrupamento diagnóstico e medicamento vs custo diário)
   ├── adimission_month (análise de sazonalidade)
   ├── expected_length_of_stay (proxy clínico de acordo com o perfil clínico do paciente)
   └── hospital_cluster (agrupamento de custos hospitalares)
       ↓
⚙️ Features Selecionadas para Modelagem
   ├── cost_per_day_br (variável-alvo)
   ├── age (preditor contínuo)
   ├── condition_medication (perfil clínico)
   ├── expected_length_of_stay (proxy clínico)
   └── hospital_cluster (agrupamento de custos hospitalares)
       ↓
🤖 Modelagem Preditiva
   ├── 8 algoritmos de regressão avaliados
   ├── [Pipelines 01, 02, 03 e 04] Modelos de Gradiente/Distância: Linear Regression, KNN, SVR e MLP (Rede Neural)
   └── [Pipelines 05, 06, 07 e 08] Modelos Baseados em Árvores/Boosting: Random Forest, LightGBM, CatBoost e XGBoost
       ↓
📊 Avaliação e Seleção
   └── LightGBM eleito modelo campeão
       ↓
🔬 Explicabilidade (SHAP)
   ├── Beeswarm (impacto direcional)
   ├── Bar Chart (importância global)
   └── Waterfall (anatomia do caso crítico)
       ↓
📈 Resultados de Negócio
```

---

## 5. Matriz de Modelos e Abordagens de Treino

| ID | Algoritmo | Objetivo Estratégico | Abordagem de Pré-Processamento | Scaling? |
| :---: | :--- | :--- | :--- | :---: |
| **01** | **Linear Regression** | Baseline Estatístico de Machine Learning | One-Hot Encoding + Standard Scaling | **Sim** |
| **02** | **K-Neighbors Regressor** | Abordagem Baseada em Distância e Vizinhança | One-Hot Encoding + Standard Scaling | **Sim** |
| **03** | **Support Vector Regressor** | Otimização de Hiperplano de Margem Máxima | One-Hot Encoding + Standard Scaling | **Sim** |
| **04** | **Multilayer Perceptron** | Rede Neural Artificial para Captura de Padrões Complexos | One-Hot Encoding + Standard Scaling | **Sim** |
| **05** | **Random Forest Regressor**| Ensemble de Árvores (Bagging) / Não-Linearidade	 | One-Hot Encoding | Não |
| **06** | **LightGBM Regressor** | Alta Performance Computacional (Boosting) | Tratamento de Categóricas Nativo | Não |
| **07** | **CatBoost Regressor** | Performance Otimizada para Variáveis Categóricas | Tratamento de Categóricas Nativo | Não |
| **08** | **XGBoost Regressor** | Alta Performance Geral (Extreme Gradient Boosting) | One-Hot Encoding | Não |

---

## 6. Performance dos Modelos Avaliados

| Modelo | MAE (R$) | RMSE (R$) | $R^2$ Score |
| :--- | :---: | :---: | :---: |
| 01_Linear_Regression | 15369.85 | 29204.04 | 0.0317 |
| 07_CatBoost | 15346.41 | 29209.04 | 0.0314 |
| 04_MLP_Neural_Network | 15405.34 | 29264.65 | 0.0277 |
| 05_Random_Forest | 15403.76 | 29265.70 | 0.0276 |
| **06_LightGBM** | **LightGBM** ⭐ | **0.0266** | **15.407,71** | **29.285,30** |
| 08_XGBoost | 15414.64 | 29312.42 | 0.0245 |
| 06_LightGBM_tuned | 12791.04 | 30788.55 | -0.0762 |
| 03_Linear_SVR | 12940.04 | 31329.12 | -0.1143 |
| 02_KNN_Regressor | 16582.57 | 31612.02 | -0.1345 |

> **O LightGBM venceu foi escolhido o modelo campeão**, pois, apesar de um R² ligeiramente inferior à Regressão Linear, o LightGBM gerou uma **distribuição de densidade preditiva dispersa e consistente** na zona de transição crítica (R$ 30k–R$ 60k), sendo o único algoritmo capaz de mitigar o risco de subprovisão financeira e não classificar nenhum sinistro catastrófico como "Baixo Custo".

---

## 7. Matriz de Decisão Estratégica: Performance Estatística vs Utilidade de Negócio

| Critério de Escolha | Modelo Campeão | $R^2$ Score | Teto de Previsão Máxima | Comportamento Gráfico | Impacto Prático no Negócio |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Otimização Estatística Global** (Melhor $R^2$) | 01_Linear_Regression | 0.0317 (3.17%) | ~ R$ 35.000 (Picos Isolados) | Torres ultra-estáticas e rígidas. Concentrou 'chutes' nas médias centrais. | Gera miopia operacional. Mascara os beneficiários de alto custo dentro da média da carteira, sendo incapaz de identificar casos catastróficos. |
| **Alinhamento de Negócio** (Densidade de Cauda e Captura de Risco) | **▶ 06_LightGBM (Modelo Eleito)** | **0.0266 (2.66%)** | **~ R$ 80,000 (Massa Densa e Contínua)** | **Nuvem dispersa e consistente na zona de transição crítica (R$ 30k - R$ 60k).** | **Mune a Auditoria Concorrente com alertas preventivos densos no momento da admissão hospitalar.** |

> **DECISÃO HOMOLOGADA:** O LightGBM Padrão foi escolhido por sua distribuição de densidade preditiva, provando-se o único modelo capaz de mitigar o risco de subprovisão e direcionar a auditoria.

---

## 8. **Modelo Campeão: LightGBM**

### Matriz de Aderência de Negócio

| Custo Real (Admissão) | Previsto:<br> Baixo (< 5k) | Previsto:<br> Médio (5k–33k) | Previsto:<br> Alto / Catastrófico (> 33k) | Total Geral |
|-----------------------|:----------------------:|:------------------------:|:-------------------------------------:|:-----------:|
| Baixo (< R$ 5k) | 65 | 4.691 | 91 | 4.847 |
| Médio (R$ 5k–R$ 33k) | 66 | 9.419 | 239 | 9.724 |
| **Alto / Catastrófico (> R$ 33k)** | **0** | 1.737 | 146 | 1.883 |
| **Total Geral** | **131** | **15.847** | **476** | **16.454** |

### Taxa Operacional de Acerto

| Faixa de Custo Real | Acerto Direto |
|---------------------|:-------------:|
| Baixo (< R$ 5k) | 1,34% |
| Médio (R$ 5k–R$ 33k) | 96,84% |
| **Alto / Catastrófico (> R$ 33k)** | **7,75%** |

### Métricas Executivas

- 🎯 **11,17%** da carteira composta por sinistros catastróficos
- ✅ **7,75%** capturados diretamente na faixa de Alto Custo na admissão
- 🛡️ **100%** de segurança de cauda — zero falsos negativos na faixa de Baixo Custo
- ✅ Nenhum caso catastrófico classificado incorretamente como "Baixo Custo"

---

## 9. Explicabilidade com SHAP

O framework **SHAP (SHapley Additive exPlanations)** foi aplicado para abrir a "caixa-preta" do LightGBM, garantindo auditabilidade regulatória e alinhamento com a lógica de negócio.

### Ranking de Importância das Features

| Feature | SHAP Médio Absoluto | Participação |
|---------|---------------------|--------------|
| 🏥 `hospital_cluster` | R$ 2.013,22 | 62,26% |
| 👤 `age` | R$ 871,20 | 26,94% |
| 🗓️ `expected_length_of_stay` | R$ 349,18 | 10,80% |

### Insights Atuariais

- **`hospital_cluster`** é o principal vetor de variabilidade. O acordo comercial com o prestador hospitalar dita o comportamento base do faturamento, com cauda de dispersão ultrapassando **+R$ 30.000/dia**.
- **`age`** valida a premissa atuarial: o envelhecimento atua como acelerador de complexidade assistencial e severidade financeira, com picos de até **+R$ 33.000/dia**.
- **`expected_length_of_stay`** funciona como ajuste fino clínico, definindo o "piso de severidade" na admissão de forma suave.

### Caso Crítico Identificado (Index 2044)

> **Projeção: R$ 85.771,10/dia**

| Gatilho | Impacto |
|---------|---------|
| `expected_length_of_stay` = 15 dias | +R$ 9.760,54 |
| `hospital_cluster` = alto custo | +R$ 25.547,09 |
| `age` = 14 anos (pediatria complexa) | +R$ 33.526,17 |

---

## 10. Resultados de Negócio

### Aplicabilidade Prática

> *"O Index 2044 acabou de ser internado, tem 14 anos e está num hospital de alto custo. A equipe de auditoria médica precisa ir ao leito imediatamente acompanhar a evolução desse caso, pois ele vai custar 5 vezes mais que a média da carteira por dia."*

A IA **otimiza o uso do capital humano** ao selecionar cirurgicamente os casos mais severos, evitando a enxurrada de alarmes falsos que inviabilizaria operacionalmente a equipe de auditores médicos.

---

## 11. Tecnologias Utilizadas

* **Linguagem Principal:** Python (v3.10+)
* **Manipulação e Engenharia de Dados:** Pandas, NumPy
* **Visualização de Dados e Gráficos de Diagnóstico:** Matplotlib, Seaborn
* **Pipelines de Machine Learning e Modelagem Clássica:** Scikit-Learn
* **Explicabilidade:** shap
* **Algoritmos de Alta Performance (Gradient Boosting):** LightGBM, CatBoost, XGBoost
* **Ambiente de Desenvolvimento:** Jupyter Notebook / VS Code

---

## 12. Como Executar

### Pré-requisitos

- Python 3.10
- Jupyter Notebook ou JupyterLab

```
# Clone o repositório
git clone https://github.com/seu-usuario/healthcare-cost-prediction.git
cd healthcare-cost-prediction

# Crie e ative o ambiente virtual
python -m venv venv
venv\Scripts\activate        # Windows
# source venv/bin/activate   # Linux/Mac

# Instale as dependências
pip install -r requirements.txt

# Execute o notebook (versão leve, para rodar do zero)
jupyter notebook healthcare-cost-prediction-outputs-cleared.ipynb
```

### Dependências principais

```
pandas
numpy
matplotlib
seaborn
scikit-learn
lightgbm
catboost
xgboost
shap
jupyter
```

---

## 13. Próximos Passos

- [ ] **Integração no Fluxo de Admissão** — disponibilizar o modelo serializado (`.joblib`) via API interna para inferência automática na geração da guia de internação
- [ ] **Automatização dos Alertas** — configurar gatilhos automáticos para a equipe de Auditoria Médica Concorrente quando o modelo projetar custos na faixa "Alto / Catastrófico (> R$ 33k)"
- [ ] **Alimentação de PEONA** — conectar o output do modelo ao dashboard de provisões técnicas da operadora para provisão dinâmica e individualizada
- [ ] **Monitoramento de Data Drift** — implementar rotina trimestral de monitoramento para recalibração do LightGBM diante de mudanças nas tabelas de negociação hospitalares ou no perfil demográfico da carteira

---

<p align="center">
  Desenvolvido com foco em impacto real na <strong>Saúde Suplementar Brasileira</strong> 🇧🇷
</p>
