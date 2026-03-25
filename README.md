# Análise de Clustering de Ativos Financeiros (IBVESP e PETR4)

## 📋 Visão Geral

Este notebook realiza uma análise completa de clustering para identificar padrões de comportamento dos ativos financeiros IBVESP (Índice Bovespa) e PETR4 (Petrobras) no período de março de 2023 a junho de 2024. A análise utiliza técnicas de machine learning não supervisionado para agrupar dias com características similares de mercado.

## 🛠️ Tecnologias Utilizadas

- **Python 3.11**
- **Pandas**: Manipulação e análise de dados
- **NumPy**: Cálculos numéricos
- **Matplotlib**: Visualização de dados
- **Seaborn**: Visualização estatística avançada
- **Scikit-learn**: Machine learning e clustering
- **yfinance**: Download de dados financeiros
- **Plotly**: Visualizações interativas

## 📁 Estrutura do Notebook

### Célula 1: Importação das Bibliotecas
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler
from sklearn.metrics import silhouette_score
import yfinance as yf
import plotly.express as px
import plotly.graph_objects as go
from plotly.subplots import make_subplots
```

**Propósito**: Importa todas as bibliotecas necessárias para análise de dados, visualização e machine learning.

---

### Célula 2: Configurações de Visualização
```python
plt.style.use('seaborn-v0_8')
sns.set_palette("husl")
pd.set_option('display.max_columns', None)
pd.set_option('display.width', None)
```

**Propósito**: Configura o estilo dos gráficos e opções de exibição do pandas para melhor visualização dos dados.

---

### Célula 3: Download e Preparação dos Dados

#### O que esta célula faz:
1. **Download dos dados**: Baixa dados históricos do IBVESP e PETR4 usando yfinance
2. **Tratamento de dados**: Remove valores nulos e duplicados
3. **Cálculo de retornos**: Calcula retornos diários e log-retornos
4. **Indicadores técnicos**: Calcula médias móveis de 5, 20 e 50 dias

#### Gráficos Gerados:

##### 📊 Gráfico 1: Preços e Retornos dos Ativos
- **Tipo**: Gráfico de subplots (2x2)
- **Conteúdo**:
  - **Superior esquerdo**: Preços normalizados do IBVESP e PETR4
  - **Superior direito**: Retornos diários do IBVESP
  - **Inferior esquerdo**: Retornos diários da PETR4
  - **Inferior direito**: Retornos acumulados comparativos

**Análise**: Este gráfico mostra a evolução dos preços e a volatilidade dos ativos, permitindo visualizar períodos de alta/baixa e a correlação entre os movimentos.

---

### Célula 4: Análise de Correlação

#### O que esta célula faz:
1. **Cálculo de correlação**: Matriz de correlação entre todos os indicadores
2. **Visualização**: Heatmap interativo da matriz de correlação

#### Gráficos Gerados:

##### 📊 Gráfico 2: Heatmap de Correlação
- **Tipo**: Heatmap interativo
- **Conteúdo**: Matriz de correlação com valores numéricos e escala de cores
- **Cores**: Azul (correlação positiva) a Vermelho (correlação negativa)

**Análise**: Mostra a força e direção das relações entre diferentes indicadores. Valores próximos de 1 indicam forte correlação positiva.

---

### Célula 5: Cálculo de Indicadores Técnicos Avançados

#### O que esta célula faz:
1. **Volatilidade**: Calcula volatilidade em janelas de 20 dias
2. **RSI (Relative Strength Index)**: Indicador de momento (0-100)
3. **Bandas de Bollinger**: Limite superior e inferior baseado em médias móveis
4. **MACD**: Convergência/divergência de médias móveis

#### Gráficos Gerados:

##### 📊 Gráfico 3: Indicadores Técnicos Completos
- **Tipo**: Gráfico com múltiplos subplots
- **Conteúdo**:
  - **Painel principal**: Preços com Bandas de Bollinger
  - **Painel superior**: RSI com níveis de sobrecompra/sobrevenda
  - **Painel inferior**: MACD com linha de sinal

**Análise**: Fornece uma visão completa da análise técnica, mostrando níveis de sobrecompra/sobrevenda, volatilidade e tendências de momentum.

---

### Célula 6: Preparação para Clustering

#### O que esta célula faz:
1. **Seleção de features**: Escolhe indicadores relevantes para clustering
2. **Normalização**: Padroniza os dados usando StandardScaler
3. **Tratamento de valores nulos**: Remove ou preenche valores ausentes

**Features selecionadas**:
- Preços dos ativos
- Médias móveis de 20 dias
- Volatilidade
- RSI
- Bandas de Bollinger

---

### Célula 7: Determinação do Número Ótimo de Clusters

#### O que esta célula faz:
1. **Método do Cotovelo**: Testa diferentes números de clusters (2-10)
2. **Análise de Silhueta**: Avalia a qualidade do clustering
3. **Visualização**: Gráficos para determinar o número ideal

#### Gráficos Gerados:

##### 📊 Gráfico 4: Método do Cotovelo e Silhueta
- **Tipo**: Gráfico combinado (2 subplots)
- **Conteúdo**:
  - **Esquerda**: Inércia (WCSS) vs número de clusters
  - **Direita**: Score de silhueta vs número de clusters

**Análise**: O "cotovelo" no gráfico da esquerda indica o ponto ótimo. O score de silhueta mede quão bem os clusters estão separados.

---

### Célula 8: Aplicação do K-Means

#### O que esta célula faz:
1. **Aplicação do modelo**: K-Means com k=3 (determinado na célula anterior)
2. **Adição de labels**: Adiciona o número do cluster ao dataset
3. **Análise dos centroides**: Exibe as características médias de cada cluster

---

### Célula 9: Análise Detalhada dos Clusters

#### O que esta célula faz:
1. **Análise descritiva**: Estatísticas detalhadas para cada cluster
2. **Classificação de tendência**: Identifica se cada cluster representa alta/baixa
3. **Análise de momento**: Classifica RSI como sobrecomprado/sobrevendido

#### Gráficos Gerados:

##### 📊 Gráfico 5: Distribuição dos Clusters no Tempo
- **Tipo**: Gráfico de dispersão temporal
- **Conteúdo**: Dias coloridos por cluster ao longo do tempo
- **Cores**: Cada cluster tem uma cor diferente

**Análise**: Mostra como os clusters se distribuem ao longo do tempo, identificando períodos de transição e padrões sazonais.

---

### Célula 10: Visualização dos Clusters

#### O que esta célula faz:
1. **Scatter plot 2D**: Visualização bidimensional dos clusters
2. **Pair plot**: Relações entre diferentes features
3. **Análise visual**: Identifica padrões e sobreposições

#### Gráficos Gerados:

##### 📊 Gráfico 6: Visualização 2D dos Clusters
- **Tipo**: Scatter plot com cores por cluster
- **Conteúdo**: Dispersão dos dados em duas dimensões principais
- **Cores**: Cada cluster representado por uma cor diferente

**Análise**: Mostra a separação visual entre os clusters e possíveis sobreposições.

##### 📊 Gráfico 7: Pair Plot dos Clusters
- **Tipo**: Matriz de scatter plots
- **Conteúdo**: Relações entre todas as variáveis principais
- **Diagonal**: Histogramas de cada variável por cluster

**Análise**: Permite identificar correlações e padrões multidimensionais entre os diferentes clusters.

---

### Célula 11: Análise de Correlação por Cluster

#### O que esta célula faz:
1. **Correlação específica**: Calcula correlação IBVESP x PETR4 por cluster
2. **Comparação**: Compara como a correlação varia entre clusters
3. **Insights**: Identifica períodos de maior/menor correlação

#### Gráficos Gerados:

##### 📊 Gráfico 8: Correlação por Cluster
- **Tipo**: Gráfico de barras
- **Conteúdo**: Valores de correlação para cada cluster
- **Cores**: Diferentes cores para cada cluster

**Análise**: Mostra como a relação entre os ativos varia em diferentes regimes de mercado.

---

### Célula 12: Conclusões e Insights

#### O que esta célula faz:
1. **Resumo estatístico**: Principais métricas da análise
2. **Performance dos ativos**: Retornos no período analisado
3. **Caracterização dos clusters**: Interpretação de cada grupo
4. **Recomendações**: Sugestões para aplicações práticas
5. **Próximos passos**: Direções para pesquisas futuras

## 🎯 Principais Insights da Análise

### Cluster 0 (38.5% do tempo)
- **Características**: Período de transição ou comportamento misto
- **Volatilidade**: Moderada
- **RSI**: Neutro a ligeiramente otimista

### Cluster 1 (44.4% do tempo)
- **Características**: Fase de acumulação ou recuperação
- **Volatilidade**: Mais baixa
- **RSI**: Neutro
- **Tendência**: Predominantemente de alta

### Cluster 2 (17.1% do tempo)
- **Características**: Período de correção volátil
- **Volatilidade**: Mais alta
- **RSI**: Neutro
- **Tendência**: Misturada com tendência de baixa para PETR4

## 📈 Resultados Principais

- **Período analisado**: 14/03/2023 a 27/06/2024 (322 dias)
- **Número ótimo de clusters**: 3
- **Correlação média IBVESP x PETR4**: 0.486
- **Performance IBVESP**: +126.83%
- **Performance PETR4**: +20.77%

## 🚀 Aplicações Práticas

1. **Gestão de risco**: Identificar períodos de alta volatilidade
2. **Estratégias de trading**: Adaptar estratégias conforme o cluster atual
3. **Diversificação**: Entender correlações dinâmicas entre ativos
4. **Previsão**: Usar clusters como feature em modelos preditivos

## 🔄 Próximos Passos Sugeridos

1. **Validação temporal**: Testar o modelo com dados out-of-sample
2. **Mais indicadores**: Incluir volume, ordem book, sentimento de mercado
3. **Modelos avançados**: Testar DBSCAN, clustering hierárquico
4. **Análise setorial**: Expandir para múltiplos setores da economia
5. **Tempo real**: Implementar sistema de clustering em tempo real

## 📝 Observações Importantes

- Os clusters representam regimes de mercado e não garantias de performance futura
- A análise foi realizada com dados históricos e pode não capturar eventos extremos
- Recomenda-se combinar esta análise com outras metodologias de análise

## 👤 Autor

Análise desenvolvida como parte de pesquisa em iniciação científica sobre aplicações de machine learning em mercados financeiros.