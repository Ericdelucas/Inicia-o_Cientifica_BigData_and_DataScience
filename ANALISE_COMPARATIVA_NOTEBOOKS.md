# 📋 ANÁLISE COMPARATIVA: test.ipynb vs PARTE1.ipynb

**Data:** 10 de janeiro de 2026  
**Objetivo:** Comparação detalhada de funcionalidades, estrutura e robustez entre os dois notebooks

---

## 1. ESTRUTURA GERAL

| Aspecto | test.ipynb | PARTE1.ipynb |
|---------|----------|------------|
| **Total de células** | 10 células | 16 células |
| **Células código** | 10 | 15 |
| **Células markdown** | 0 | 1 |
| **Comentários estruturados** | ✅ SIM (cabeçalhos com "BLOCO X") | ✅ SIM (menos estruturado) |

---

## 2. IMPORTAÇÕES DE BIBLIOTECAS

**test.ipynb (célula 1):**
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import yfinance as yf
import talib
from sklearn.cluster import KMeans
from scipy.stats import shapiro
import warnings
from pathlib import Path  # ✅ ÚNICO RECURSO

warnings.filterwarnings('ignore')
DATA_DIR = Path("data")
DATA_DIR.mkdir(exist_ok=True)
```

**PARTE1.ipynb (célula 1):**
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import yfinance as yf
import talib
from sklearn.cluster import KMeans
from scipy.stats import shapiro
import warnings

warnings.filterwarnings('ignore')
# ❌ SEM pathlib.Path e gestão de diretórios
```

| Feature | test.ipynb | PARTE1.ipynb |
|---------|----------|------------|
| **pathlib.Path** | ✅ SIM | ❌ NÃO |
| **Gestão de diretórios** | ✅ SIM | ❌ NÃO |

---

## 3. CARREGAMENTO E PREPARAÇÃO DOS DADOS

### 3.1 Leitura do Excel

| Funcionalidade | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Leitura Excel** | ✅ Bloco 1 (11 linhas, bem documentado) | ✅ Célula 2 (1 linha, minimalista) |
| **Visualização inicial** | ✅ `df.head()` | ✅ `df.head(5)` |

### 3.2 Conversão de Data

**test.ipynb (Bloco 1):**
```python
df['Data Do Pregão'] = pd.to_datetime(df['Data Do Pregão'], format='%Y%m%d')
df = df.set_index('Data Do Pregão')
```

**PARTE1.ipynb (Célula 9):**
```python
df['Data Do Pregão'] = pd.to_datetime(df['Data Do Pregão'], format='%Y%m%d')
df = df.set_index('Data Do Pregão')
```

✅ **Ambos idênticos**

### 3.3 Normalização de Preço (CRÍTICO)

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Normalização PREULT ÷ 100** | ✅ Bloco 1 | ⚠️ **Parcial: Célula 9 sim, mas Células 2-6 usam valor bruto** |
| **Filtragem Tipo Mercado = 10** | ✅ Bloco 1 | ❌ NÃO FILTRA |
| **Verificação (df.info())** | ✅ Bloco 1 | ⚠️ Célula 9 (contexto diferente) |

**⚠️ PROBLEMA IDENTIFICADO:**
- PARTE1 usa `PREULT` bruto (não dividido por 100) nas análises exploratórias iniciais (Células 3-6)
- test.ipynb usa corretamente `PREULT_NORMALIZADO` desde o Bloco 1
- Os valores de preço diferem por fator 100 nas primeiras análises do PARTE1

---

## 4. ANÁLISE EXPLORATÓRIA INICIAL

### 4.1 Estatísticas Descritivas

| Item | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Desvio padrão por ativo** | ✅ Bloco 2: `groupby("CODNEG").std().sort_values(ascending=False)` | ✅ Célula 3: Mesmo MAS SEM `.sort_values()` |
| **Média de preço** | ✅ Bloco 2: `groupby("CODNEG").mean()` | ✅ Célula 5: Mesmo |
| **Filtra PETR3** | ✅ Bloco 2: `df.query("CODNEG == 'PETR3'")` | ✅ Célula 4: Mesmo |
| **Ordenação** | ✅ Descrescente (mais útil) | ⚠️ Sem ordenação |

### 4.2 Visualização Inicial

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Gráfico PETR3** | ✅ Bloco 2 (figsize=12×5) | ✅ Célula 6 (sem especificar figsize) |
| **Série temporal** | ✅ Sobre PREULT_NORMALIZADO | ❌ Sobre PREULT bruto |
| **Grid** | ✅ `plt.grid(True)` | ❌ Sem grid |

---

## 5. MATRIZES PIVOTADAS (Estrutura Multivariada)

### 5.1 Criação de Matrizes

| Feature | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Matriz de fechamento** | ✅ Bloco 3: `pivot_table(index, columns, values)` com comentário explicativo | ✅ Célula 9: Mesmo método |
| **Matriz de volume** | ✅ Bloco 3: Criada e nomeada `df_volume` | ✅ Célula 9: Mesmo |
| **Preço normalizado** | ✅ PREULT_NORMALIZADO | ✅ PREULT_NORMALIZADO |
| **Mensagem de sucesso** | ✅ `print("✅ Matrizes criadas com sucesso")` | ✅ `print("✅ Dados carregados com sucesso!")` |

### 5.2 Tratamento de Valores Faltantes

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Tratamento de NaN** | ⚠️ Não mencionado | ✅ Célula 13: `fillna(method='ffill').fillna(method='bfill')` |
| **Robustez** | ⚠️ Pode ter problemas com séries incompletas | ✅ Mais seguro |

---

## 6. ANÁLISE DE VOLUME

### 6.1 Comparação

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Existe análise?** | ✅ Bloco 4 | ✅ Célula 11 |
| **Volume médio** | ✅ Calcula | ✅ Calcula |
| **Correlação Preço-Volume** | ✅ Calcula com `corr()` | ✅ Calcula com `corr()` |
| **Complexidade** | ⚠️ Simples | ✅ Mais detalhada |

### 6.2 Detecção de Anomalias

| Elemento | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Dias com volume atípico** | ❌ NÃO IDENTIFICA | ✅ SIM: `volume > média + 2×desvio` |
| **Utilidade** | ⚠️ Análise incompleta | ✅ Maior poder analítico |

**Exemplo PARTE1 (Célula 11):**
```python
limite = vol_medio + 2 * df_vol[ativo_exemplo].std()
dias_atipicos = df_vol[df_vol[ativo_exemplo] > limite]
print("\nDias com volume atípico:")
print(dias_atipicos)
```

---

## 7. INDICADORES TÉCNICOS (RSI, MACD, Bandas Bollinger)

### 7.1 Cobertura de Indicadores

| Indicador | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **RSI (14 períodos)** | ✅ Bloco 6: `talib.RSI(preco, 14)` | ✅ Célula 12: `talib.RSI(preco, timeperiod=14)` |
| **Bandas de Bollinger (20)** | ✅ Bloco 6: `talib.BBANDS(preco, 20)` | ✅ Célula 12: Mesmo |
| **MACD** | ❌ **NÃO IMPLEMENTADO** | ✅ Célula 12: `talib.MACD(preco)` |

### 7.2 Visualizações

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Visualização RSI** | ❌ Código sem gráfico | ✅ Gráfico com linhas de referência (30/70) |
| **Visualização BBANDS** | ❌ Código sem gráfico | ✅ Gráfico com UPPER, MIDDLE, LOWER |
| **Qualidade visual** | ⚠️ Minimal | ✅ Melhor estruturada |

**Exemplo PARTE1 (Célula 12):**
```python
plt.figure(figsize=(12, 4))
df_ta['RSI'].plot(title=f'RSI - {ativo_exemplo}')
plt.axhline(70, color='r', linestyle='--')  # Oversold
plt.axhline(30, color='g', linestyle='--')  # Overbought
plt.show()
```

---

## 8. BENCHMARK PETR4 vs IBOVESPA

### 8.1 Download de Dados

| Feature | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Download IBOV** | ✅ Bloco 5: `yf.download('^BVSP')` | ✅ Célula 10: Mesmo |
| **Intervalo de datas** | ✅ Min/Max de df_fechamento | ✅ Min/Max de df_fechamento |

### 8.2 Tratamento de Formato de Retorno

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **MultiIndex handling** | ✅ Acessa com `ibov_data.loc[:, ("Close", "^BVSP")]` | ✅ Detecta e corrige automaticamente |
| **Fallback para erros** | ❌ Pode quebrar se formato mudar | ✅ Procura 'Adj Close', depois 'Close', depois coluna numérica |
| **Mensagem de diagnóstico** | ⚠️ Apenas imprime colunas | ✅ Mensagem clara se usar fallback |
| **Robustez** | ⚠️ Frágil | ✅ Muito robusto |

**Comparação de código:**

**test.ipynb (Bloco 5):**
```python
ibov = ibov_data.loc[:, ("Close", "^BVSP")]
```

**PARTE1.ipynb (Célula 10):**
```python
if isinstance(ibov_data.columns, pd.MultiIndex):
    ibov_data.columns = ibov_data.columns.get_level_values(-1)

if 'Adj Close' in ibov_data.columns:
    ibov = ibov_data['Adj Close']
elif 'Close' in ibov_data.columns:
    ibov = ibov_data['Close']
else:
    ibov = ibov_data.select_dtypes(include=[np.number]).iloc[:, 0]
    print(f"⚠️ Nenhuma coluna 'Adj Close' ou 'Close' encontrada — usando '{ibov.name}'.")
```

### 8.3 Normalização e Visualização

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Retorno acumulado normalizado** | ✅ Base 100 | ✅ Base normalizada (divide por primeira obs.) |
| **Tratamento de timezone** | ✅ Remove timezone se presente | ✅ Não mencionado |
| **Visualização** | ✅ Simples | ✅ Com título e ylabel |

---

## 9. CÁLCULO DE RETORNOS E MÉTRICAS DE RISCO

### 9.1 Métricas Calculadas

| Métrica | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Retorno diário** | ✅ Bloco 7: `pct_change().dropna()` | ✅ Célula 14: Mesmo |
| **Retorno anual acumulado** | ❌ NÃO CALCULA | ✅ `(df_retornos + 1).prod() - 1` |
| **Retorno médio anual** | ✅ `mean() × 252` | ✅ Mesmo |
| **Volatilidade anual** | ✅ `std() × √252` | ✅ Mesmo |
| **Sharpe Ratio** | ✅ `(retorno - rf) / volatilidade` | ✅ Mesmo |
| **Taxa de risco-free** | ✅ 10% (SELIC aproximada) | ✅ Mesma |

### 9.2 Apresentação dos Resultados

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Top 10 ativos** | ✅ Retorna Series com Sharpe Ratio | ✅ Retorna DataFrame com 4 colunas |
| **Formatação** | ❌ Valores brutos | ✅ Formatação em percentual (%.2%) |
| **Informação** | ⚠️ Apenas Sharpe | ✅ Retorno acumulado, retorno anual, volatilidade, Sharpe |
| **Usabilidade** | ⚠️ Menos informativo | ✅ Muito mais informativo |

**Exemplo PARTE1 (Célula 14):**
```python
df_est = pd.DataFrame({
    'Retorno Acumulado': ret_acum,
    'Retorno Médio Anualizado': ret_medio_anual,
    'Volatilidade Anualizada': vol_anual,
    'Sharpe Ratio': sharpe
}).sort_values(by='Sharpe Ratio', ascending=False)

print("Top 10 ativos por Sharpe Ratio:")
print(df_est.head(10).applymap(lambda x: f'{x:.2%}'))
```

---

## 10. CLUSTERING (K-MEANS)

### 10.1 Preparação de Dados

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Transposição** | ✅ `df_retornos.T` | ✅ Mesmo |
| **Seleção de colunas numéricas** | ✅ `.select_dtypes(include=[np.number])` | ✅ Mesmo |
| **Preenchimento de NaN** | ✅ `.fillna(0)` | ✅ Mesmo |
| **Limpeza de dados insuficientes** | ✅ `.dropna(thresh=10)` | ⚠️ Não menciona |

### 10.2 Parâmetros e Validação

| Feature | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Número de clusters** | ✅ 5 clusters | ✅ 5 clusters |
| **Random state** | ✅ 42 | ✅ 42 |
| **n_init** | ✅ 10 | ✅ 10 |
| **Validação mínima** | ✅ `if n_ativos >= n_clusters` | ✅ `if len(df_cluster) >= 5` |

### 10.3 Output e Tratamento de Erros

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Mensagem de sucesso** | ✅ Imprime distribuição dos clusters | ✅ Imprime exemplos de ativos por cluster |
| **Mensagem de erro** | ✅ Mensagem detalhada | ⚠️ Mensagem breve |
| **Usabilidade** | ✅ Melhor debugging | ✅ Mais conciso |

**test.ipynb (Bloco 8):**
```python
print("✅ Clustering executado")
print("\nDistribuição dos clusters:")
print(clusters.value_counts())
clusters.head()
```

**PARTE1.ipynb (Célula 14):**
```python
print(f"\n✅ Clustering concluído com sucesso!\n")
for i in range(5):
    grupo = clusters[clusters == i]
    print(f"Cluster {i}: {len(grupo)} ativos")
    print("Exemplos:", grupo.head(10).index.tolist(), "\n")
```

---

## 11. TESTES ESTATÍSTICOS

### 11.1 Teste de Normalidade (Shapiro-Wilk)

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Import do módulo** | ✅ `from scipy.stats import shapiro` | ✅ Mesmo |
| **Implementação** | ❌ **IMPORTADO MAS NÃO USADO** | ✅ Célula 15: Totalmente implementado |
| **Validação de amostra** | ❌ NÃO | ✅ Verifica `3 < len(ret) ≤ 5000` |
| **Interpretação de resultados** | ❌ NÃO | ✅ Explica p-valor |

**PARTE1.ipynb (Célula 15):**
```python
ret = df_retornos[ativo_exemplo].dropna()
if 3 < len(ret) <= 5000:
    stat, p = shapiro(ret)
    print(f"Estatística: {stat:.4f}, p-valor: {p:.4f}")
    if p > 0.05:
        print("Os retornos seguem uma distribuição normal (p > 0.05).")
    else:
        print("Os retornos NÃO seguem uma distribuição normal (p ≤ 0.05).")
```

---

## 12. ANÁLISE FINAL (Série Temporal e Médias Móveis)

### 12.1 Gráficos de Série Temporal

| Elemento | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Série PETR4** | ✅ Bloco 9: Plotado | ✅ Célula 16: Plotado com ylabel |
| **Título** | ⚠️ Genérico | ✅ `f'Evolução Temporal - {ativo_exemplo}'` |
| **Ylabel** | ❌ NÃO | ✅ 'Preço (R$)' |

### 12.2 Médias Móveis

| Feature | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **MA20** | ✅ Criada com `rolling(20).mean()` | ✅ Criada com `rolling(20).mean()` |
| **MA50** | ✅ Criada com `rolling(50).mean()` | ✅ Criada com `rolling(50).mean()` |
| **Nomenclatura** | ❌ `MA20`, `MA50` | ✅ `MA_20`, `MA_50` (mais limpo) |

### 12.3 Volatilidade Anualizada

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Cálculo** | ❌ NÃO CALCULA | ✅ Célula 16: `std() × √252` |
| **Impressão** | ❌ NÃO | ✅ Imprime com formatação % |

**PARTE1.ipynb (Célula 16):**
```python
retornos_diarios = df_ativo[ativo_exemplo].pct_change().dropna()
vol_anu = retornos_diarios.std() * np.sqrt(252)
print(f"Volatilidade Anualizada ({ativo_exemplo}): {vol_anu:.2%}")
```

### 12.4 Tratamento de Erros

| Aspecto | test.ipynb | PARTE1.ipynb |
|---|---|---|
| **Validação de existência de ativo** | ⚠️ Implícita | ✅ `if ativo_exemplo in df_fechamento.columns` |
| **Mensagem de erro** | ❌ NÃO | ✅ `print(f"Ativo {ativo_exemplo} não encontrado.")` |

---

## 📊 RESUMO EXECUTIVO

### ✅ **test.ipynb TEM mas PARTE1 NÃO:**

1. **Gestão de diretórios** com `pathlib.Path` (DATA_DIR)
2. **Indicadores técnicos completos** (MACD não está implementado)
3. **Filtragem explícita** de tipo de mercado (Mercado == 10)
4. **Estrutura de comentários** bem organizada em BLOCOS
5. **Verificação de estrutura** com `df.info()`

### ✅ **PARTE1 TEM mas test NÃO:**

1. **Preenchimento de NaN** robusto (ffill + bfill)
2. **Tratamento de MultiIndex** com fallback inteligente
3. **Visualizações de indicadores técnicos** (RSI com linhas de referência, BBANDS)
4. **Indicador MACD** implementado
5. **Teste de Shapiro-Wilk** (normalidade dos retornos)
6. **Detecção de anomalias** (volume atípico > média + 2σ)
7. **DataFrame estruturado** com 4 métricas de risco
8. **Formatação em percentual** para melhor legibilidade
9. **Volatilidade anualizada** calculada e exibida
10. **Validação de existência** de ativos antes do processamento
11. **Mensagens de diagnóstico** mais informativas
12. **Clustering com exemplos** de ativos por cluster

---

## 🎯 RECOMENDAÇÕES

### Para test.ipynb:
- ✅ Adicionar teste de Shapiro-Wilk (célula separada)
- ✅ Adicionar visualizações de indicadores técnicos
- ✅ Adicionar indicador MACD
- ✅ Adicionar detecção de volume atípico
- ✅ Adicionar validação e tratamento de erro

### Para PARTE1.ipynb:
- ✅ Padronizar comentários em BLOCOS (como test.ipynb)
- ✅ Adicionar pathlib.Path para gestão de diretórios
- ✅ Adicionar df.info() para validação estrutural

### Ideal:
**Mesclar o melhor dos dois:**
- Estrutura de blocos do test.ipynb
- Robustez e completude do PARTE1.ipynb

---

**Análise realizada em:** 10 de janeiro de 2026  
**Versões:** test.ipynb (10 células) | PARTE1.ipynb (16 células)
