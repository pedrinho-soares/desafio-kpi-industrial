# Preditor de KPI Industrial — Desafio de Ciência de Dados

## Objetivo

Desenvolver um modelo de Machine Learning capaz de estimar o KPI 
de um processo industrial em resolução temporal de 1 minuto, com 
1 hora de antecedência, utilizando variáveis de processo coletadas 
por sensores de campo.

O sensor óptico responsável pela medição do KPI opera a cada 30 
minutos. O modelo substitui essa limitação, viabilizando monitoramento 
contínuo e previsão antecipada do desempenho do processo.

---

## Estrutura do projeto
```
desafio_ps/
│
├── dados_planta/
│   ├── dados_planta.csv              # Dataset original
│   ├── dados_planta_limpos.csv       # Após limpeza na EDA
│   └── dados_planta_modelagem.csv    # Após pré-processamento
│
├── EDA.ipynb                         # Análise exploratória de dados
├── pre_processamento.ipynb           # Limpeza e preparação
├── modelagem.ipynb                   # Modelo e avaliação
│
├── resultados.png                    # Gráficos finais
├── requirements.txt                  # Dependências
└── README.md                         # Este arquivo
```

---

## Decisão de formato

Os notebooks `.ipynb` foram escolhidos em vez de scripts `.py` 
porque permitem combinar código, visualizações e documentação 
em um único arquivo — facilitando a reprodução e avaliação do 
raciocínio analítico em cada etapa.

---

## Metodologia

### 1. EDA
- Análise de distribuição, valores ausentes e outliers
- Identificação de variáveis constantes e corrompidas
- Correlação das variáveis com a TARGET
- Hipótese: sensores de processo antecipam mudanças no KPI

### 2. Pré-processamento
- Remoção de var14 (dado corrompido — propagação de outlier via ffill)
- Remoção de linhas com TARGET < 5 (paradas de processo)
- Tratamento de NaN com forward fill
- Criação do alvo: `y = TARGET.shift(-60)` — previsão com 1h de antecedência

### 3. Feature engineering
- Lags temporais: t-15, t-30, t-60 minutos
- Médias móveis: janelas de 15 e 30 minutos
- Total: 81 features geradas a partir das 13 originais

### 4. Modelagem
- Algoritmo: XGBoost Regressor
- Validação: split temporal 80/20 — sem embaralhamento
- Baseline: média histórica do treino

---

## Resultados

| Modelo | RMSE | MAE | R² |
|---|---|---|---|
| Baseline (média) | 0.7686 | — | — |
| XGBoost v1 (sem lags) | 0.6477 | 0.4636 | 0.2895 |
| XGBoost v2 (com lags) | 0.5968 | 0.4155 | 0.3972 |

**Melhora sobre baseline: 22.3%**

A variável mais importante foi `var4_roll30` — média móvel de 30 
minutos da var4, respondendo por 57% do poder preditivo do modelo.

---

## Limitações e próximos passos

- R² de 0.40 indica espaço de melhoria
- Modelo atenua variações abruptas do KPI
- Próximos passos:
  - Features de turno e hora do dia
  - Modelos separados por regime operacional
  - Lags mais longos (120, 180 minutos)
  - Teste com LightGBM

---

## Como reproduzir
```bash
# 1. Instalar dependências
pip install -r requirements.txt

# 2. Executar na ordem
# Abrir e rodar todas as células de cada notebook:
# EDA.ipynb → pre_processamento.ipynb → modelagem.ipynb
```

---

## Dependências principais

- Python 3.11
- pandas
- numpy
- matplotlib
- scikit-learn
- xgboost
```

---

Agora cria o `requirements.txt` — cola isso num arquivo novo:
```
pandas
numpy
matplotlib
scikit-learn
xgboost
jupyterlab