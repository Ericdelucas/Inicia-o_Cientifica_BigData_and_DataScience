# RESUMO COMPLETO DO CÓDIGO - Análise de Ações Brasileiras com IA

## O QUE ESTE CÓDIGO FAZ:

Este é um sistema de análise quantitativa de dados do mercado de ações brasileiro. O código carrega dados históricos de ações de um arquivo Excel (Dados2.xlsx), processa essas informações e realiza uma série de análises financeiras sofisticadas.

### Principais Funcionalidades:

1. **Carregamento e Preparação de Dados**: Lê um arquivo Excel com dados de ações, converte datas, normaliza preços e cria tabelas pivotadas de preço e volume.

2. **Comparação com Benchmark (IBOVESPA)**: Faz o download de dados do Índice Bovespa do Yahoo Finance e compara o desempenho de ações específicas (como PETR4) com o índice.

3. **Análise de Volume**: Calcula volume médio diário, correlação entre preço e volume, e identifica dias com volumes atípicos (anormalmente altos).

4. **Indicadores Técnicos**: Calcula RSI (Relative Strength Index), Bandas de Bollinger e MACD usando a biblioteca TALib, ferramentas essenciais para análise técnica.

5. **Métricas de Risco e Retorno**: Calcula retorno acumulado, retorno médio anualizado, volatilidade anualizada (medida de risco) e Sharpe Ratio (relação risco-retorno) para todos os ativos.

6. **Clustering de Ativos**: Usa K-Means para agrupar 5 clusters de ações com características semelhantes de retorno.

7. **Teste de Normalidade**: Aplica o teste de Shapiro-Wilk para verificar se os retornos de uma ação seguem distribuição normal.

8. **Análise Técnica Temporal**: Plota gráficos de evolução de preço, calcula volatilidade anualizada e cria médias móveis (MA20 e MA50) para análise de tendências.

### Tecnologias Utilizadas:
- **Pandas**: Manipulação de dados
- **NumPy**: Operações numéricas
- **Matplotlib**: Visualização de gráficos
- **YFinance**: Download de dados do mercado
- **TALib**: Indicadores técnicos
- **Scikit-Learn**: Machine Learning (K-Means)
- **SciPy**: Testes estatísticos

---

## CÓDIGO COMPLETO:

```python
# ============================================================================
# IMPORTAR BIBLIOTECAS NECESSÁRIAS
# ============================================================================
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import yfinance as yf
import talib
from sklearn.cluster import KMeans
from scipy.stats import shapiro
import warnings

warnings.filterwarnings('ignore')

# ============================================================================
# SEÇÃO 1: CARREGAMENTO INICIAL E EXPLORAÇÃO DE DADOS
# ============================================================================

# Carregar dados do arquivo Excel
df = pd.read_excel("Dados2.xlsx")
df.head(5)  # Mostrar primeiras 5 linhas

# Calcular desvio padrão de preços por ação (código)
dfSTD = df[["CODNEG","PREULT"]].groupby("CODNEG").std()
dfSTD

# Filtrar dados de uma ação específica (PETR3)
dfPETR3 = df.query("CODNEG=='PETR3'")

# Calcular preço médio de PETR3
dfPETR3[["CODNEG","PREULT"]].groupby("CODNEG").mean()

# Plotar preço da PETR3 ao longo do tempo
import matplotlib.pyplot as plt
eixoX = dfPETR3['Data Do Pregão']
eixoY = dfPETR3['PREULT']
plt.plot(eixoX, eixoY)
plt.show()

# ============================================================================
# SEÇÃO 2: PROCESSAMENTO AVANÇADO DE DADOS
# ============================================================================

df = pd.read_excel("Dados2.xlsx")

# Conversão de datas
df['Data Do Pregão'] = pd.to_datetime(df['Data Do Pregão'], format='%Y%m%d')
df = df.set_index('Data Do Pregão')

# Normalização dos preços
df['PREULT_NORMALIZADO'] = df['PREULT'] / 100

# Filtra ações (Tipo de Mercado 10)
df_acoes = df[df['Tipo de Mercado'] == 10].copy()

# Cria tabelas pivotadas de preço e volume
df_fechamento = df_acoes.pivot_table(index='Data Do Pregão', columns='CODNEG', values='PREULT_NORMALIZADO')
df_volume = df_acoes.pivot_table(index='Data Do Pregão', columns='CODNEG', values='VOLTOT')

print("✅ Dados carregados com sucesso!")
print(df_fechamento.tail())

# ============================================================================
# SEÇÃO 3: COMPARAÇÃO COM IBOVESPA (BENCHMARK)
# ============================================================================

# Comparar com IBOVESPA
data_ini, data_fim = df_fechamento.index.min(), df_fechamento.index.max()

# Faz o download dos dados do Ibovespa (^BVSP)
ibov_data = yf.download('^BVSP', start=data_ini, end=data_fim, progress=False)

# 🔍 Diagnóstico: mostra como o yfinance retornou as colunas
print("Colunas retornadas por yfinance:")
print(ibov_data.columns)

# 🔧 Corrige caso o retorno seja MultiIndex
if isinstance(ibov_data.columns, pd.MultiIndex):
    ibov_data.columns = ibov_data.columns.get_level_values(-1)

# 📈 Escolhe a melhor coluna disponível
if 'Adj Close' in ibov_data.columns:
    ibov = ibov_data['Adj Close']
elif 'Close' in ibov_data.columns:
    ibov = ibov_data['Close']
else:
    # Fallback — pega a primeira coluna numérica (raro, mas evita erro)
    ibov = ibov_data.select_dtypes(include=[np.number]).iloc[:, 0]
    print(f"⚠️ Nenhuma coluna 'Adj Close' ou 'Close' encontrada — usando '{ibov.name}'.")

# Cria o DataFrame de comparação PETR4 x IBOV
df_bench = pd.DataFrame({'PETR4': df_fechamento['PETR4'], 'IBOV': ibov}).dropna()

# Calcula retorno acumulado normalizado
bench_norm = (1 + df_bench.pct_change().fillna(0)).cumprod()
bench_norm /= bench_norm.iloc[0]

# Plota os resultados
bench_norm.plot(figsize=(12, 6), title='Desempenho PETR4 vs IBOVESPA', linewidth=2)
plt.ylabel('Retorno Normalizado (Base 100)')
plt.grid(True)
plt.show()

# ============================================================================
# SEÇÃO 4: ANÁLISE DE VOLUME
# ============================================================================

ativo_exemplo = 'PETR4'

if ativo_exemplo in df_volume.columns:
    df_vol = df_volume[[ativo_exemplo]].dropna()
    vol_medio = df_vol[ativo_exemplo].mean()
    print(f"Volume médio diário ({ativo_exemplo}): {vol_medio:,.0f}")

    df_pv = pd.DataFrame({
        'Preço': df_fechamento[ativo_exemplo],
        'Volume': df_vol[ativo_exemplo]
    }).dropna()
    corr = df_pv['Preço'].corr(df_pv['Volume'])
    print(f"Correlação Preço x Volume: {corr:.4f}")

    limite = vol_medio + 2 * df_vol[ativo_exemplo].std()
    dias_atipicos = df_vol[df_vol[ativo_exemplo] > limite]
    print("\nDias com volume atípico:")
    print(dias_atipicos)

# ============================================================================
# SEÇÃO 5: INDICADORES TÉCNICOS (RSI, BANDAS DE BOLLINGER, MACD)
# ============================================================================

ativo_exemplo = 'PETR4'
if ativo_exemplo in df_fechamento.columns:
    df_ta = df_fechamento[[ativo_exemplo]].dropna()
    preco = df_ta[ativo_exemplo].values

    # Calcular indicadores técnicos
    df_ta['RSI'] = talib.RSI(preco, timeperiod=14)
    upper, middle, lower = talib.BBANDS(preco, timeperiod=20)
    df_ta['BB_UPPER'], df_ta['BB_MIDDLE'], df_ta['BB_LOWER'] = upper, middle, lower
    df_ta['MACD'], _, _ = talib.MACD(preco)

    # Plotar RSI
    plt.figure(figsize=(12, 4))
    df_ta['RSI'].plot(title=f'RSI - {ativo_exemplo}')
    plt.axhline(70, color='r', linestyle='--')
    plt.axhline(30, color='g', linestyle='--')
    plt.show()

    # Plotar Bandas de Bollinger
    df_ta[[ativo_exemplo, 'BB_UPPER', 'BB_MIDDLE', 'BB_LOWER']].plot(figsize=(12, 6), title=f'Bandas de Bollinger - {ativo_exemplo}')
    plt.show()

# ============================================================================
# SEÇÃO 6: MÉTRICAS DE RISCO E RETORNO (SHARPE RATIO)
# ============================================================================

df_retornos = df_fechamento.pct_change().dropna()

ret_acum = (df_retornos + 1).prod() - 1
ret_medio_anual = df_retornos.mean() * 252
vol_anual = df_retornos.std() * np.sqrt(252)

taxa_rf = 0.10
sharpe = (ret_medio_anual - taxa_rf) / vol_anual

df_est = pd.DataFrame({
    'Retorno Acumulado': ret_acum,
    'Retorno Médio Anualizado': ret_medio_anual,
    'Volatilidade Anualizada': vol_anual,
    'Sharpe Ratio': sharpe
}).sort_values(by='Sharpe Ratio', ascending=False)

print("Top 10 ativos por Sharpe Ratio:")
print(df_est.head(10).applymap(lambda x: f'{x:.2%}'))

# ============================================================================
# SEÇÃO 7: PREENCHIMENTO DE DADOS FALTANTES
# ============================================================================

# Garantir que todos os ativos tenham valores válidos (sem NaN)
df_fechamento_preenchido = df_fechamento.fillna(method='ffill').fillna(method='bfill')

# Recalcular os retornos com base nesses dados completos
df_retornos = df_fechamento_preenchido.pct_change().dropna()

# ============================================================================
# SEÇÃO 8: CLUSTERING DE ATIVOS COM K-MEANS
# ============================================================================

df_cluster = df_retornos.T.select_dtypes(include=[np.number]).fillna(0)

if len(df_cluster) >= 5:
    kmeans = KMeans(n_clusters=5, random_state=42, n_init=10)
    kmeans.fit(df_cluster)
    clusters = pd.Series(kmeans.labels_, index=df_cluster.index)

    print(f"\n✅ Clustering concluído com sucesso!\n")
    for i in range(5):
        grupo = clusters[clusters == i]
        print(f"Cluster {i}: {len(grupo)} ativos")
        print("Exemplos:", grupo.head(10).index.tolist(), "\n")
else:
    print("⚠️ Ainda há poucos ativos válidos para clustering.")

# ============================================================================
# SEÇÃO 9: TESTE DE NORMALIDADE (SHAPIRO-WILK)
# ============================================================================

ativo_exemplo = 'PETR4'
if ativo_exemplo in df_retornos.columns:
    ret = df_retornos[ativo_exemplo].dropna()
    if 3 < len(ret) <= 5000:
        stat, p = shapiro(ret)
        print(f"Estatística: {stat:.4f}, p-valor: {p:.4f}")
        if p > 0.05:
            print("Os retornos seguem uma distribuição normal (p > 0.05).")
        else:
            print("Os retornos NÃO seguem uma distribuição normal (p ≤ 0.05).")
    else:
        print("Amostra inadequada para o teste de Shapiro-Wilk.")

# ============================================================================
# SEÇÃO 10: ANÁLISE TÉCNICA TEMPORAL E MÉDIAS MÓVEIS
# ============================================================================

ativo_exemplo = 'PETR4'

if ativo_exemplo in df_fechamento.columns:
    df_ativo = df_fechamento[[ativo_exemplo]].dropna()

    # Gráfico de preço
    plt.figure(figsize=(12, 6))
    df_ativo[ativo_exemplo].plot(title=f'Evolução Temporal - {ativo_exemplo}')
    plt.ylabel('Preço (R$)')
    plt.show()

    # Volatilidade anualizada
    retornos_diarios = df_ativo[ativo_exemplo].pct_change().dropna()
    vol_anu = retornos_diarios.std() * np.sqrt(252)
    print(f"Volatilidade Anualizada ({ativo_exemplo}): {vol_anu:.2%}")

    # Médias móveis
    df_ativo['MA_20'] = df_ativo[ativo_exemplo].rolling(20).mean()
    df_ativo['MA_50'] = df_ativo[ativo_exemplo].rolling(50).mean()

    df_ativo[['MA_20', 'MA_50']].plot(figsize=(12, 6), title=f'Médias Móveis - {ativo_exemplo}')
    plt.ylabel('Preço (R$)')
    plt.show()
else:
    print(f"Ativo {ativo_exemplo} não encontrado.")
```

---

## RESUMO EXECUTIVO PARA USAR NO ChatGPT:

Copie o texto acima (da seção "## O QUE ESTE CÓDIGO FAZ" até o final da seção "## CÓDIGO COMPLETO") e cole no ChatGPT.

O ChatGPT será capaz de:
- Explicar cada seção do código
- Sugerir melhorias e otimizações
- Ajudar a debugar problemas
- Expandir funcionalidades
- Explicar conceitos financeiros usados no código
