# Modelagem e Previsão do Preço do Ouro

Aplicação da metodologia Box–Jenkins à série mensal do preço do ouro (USD/onça), 2000–2024.

**Disciplina:** Elementos de Séries Temporais, UFC
**Professor:** Dr. Daniel Tomaz
**Autor:** Diego Lopes

---

## Objetivo

Investigar se é possível prever o preço mensal do ouro por meio de modelos ARIMA e avaliar a confiabilidade dessas previsões fora da amostra, aplicando de forma completa as quatro etapas da metodologia Box–Jenkins: identificação, estimação, diagnóstico e previsão.

## Dados

- **Ativo:** Gold Futures (GC=F)
- **Fonte:** Yahoo Finance, via biblioteca `yfinance`
- **Frequência:** mensal (agregada a partir de dados diários, para evitar lacunas)
- **Período:** agosto/2000 a dezembro/2024 (293 observações)
- **Treino:** ago/2000 – dez/2023 (281 obs.)
- **Teste:** jan/2024 – dez/2024 (12 obs.)

## Metodologia

1. **Identificação** — testes de raiz unitária (ADF, KPSS, PP, DF-GLS, Zivot-Andrews) com especificação de constante e tendência; análise de FAC/FACP da série diferenciada.
2. **Estimação** — modelos candidatos comparados por AIC/BIC (estimação por máxima verossimilhança).
3. **Diagnóstico** — teste de Ljung-Box (autocorrelação residual), Jarque-Bera e Shapiro-Wilk (normalidade), ARCH-LM (heterocedasticidade condicional).
4. **Previsão** — geração de previsões fora da amostra e avaliação por ME, RMSE, MAE, MAPE e MASE.

A função `auto_arima` foi usada apenas como referência complementar, conforme orientação da disciplina.

## Principais Resultados

| Etapa | Resultado |
|---|---|
| Estacionaridade | Série em nível é I(1): não estacionária; estacionária em 1ª diferença (d = 1) |
| Modelos candidatos | ARIMA(1,1,0), ARIMA(0,1,1), ARIMA(1,1,1) |
| Melhor por critério de informação | ARIMA(0,1,1) — AIC 3063,62 / BIC 3070,88 (praticamente empatado com ARIMA(1,1,0)) |
| Diagnóstico de resíduos | Ljung-Box não rejeita ausência de autocorrelação (ruído branco); Jarque-Bera/Shapiro-Wilk rejeitam normalidade; ARCH-LM indica heterocedasticidade condicional |
| Previsão 2024 | Modelos estabilizam a previsão em ~US$ 2.059; preço real ultrapassou US$ 2.700 |
| MASE | ≈ 8,3 — modelos ARIMA superados pela previsão *naïve* neste horizonte |

**Interpretação:** os modelos foram corretamente identificados e diagnosticados (resíduos como ruído branco), mas modelos ARIMA com d = 1 projetam o último nível mais uma deriva constante. Em 2024, um ano atipicamente altista para o ouro, essa característica linear impediu que o modelo acompanhasse a aceleração de preço — uma limitação do horizonte/período testado, não uma falha de especificação.

## Estrutura do Código (`A. Código-fonte`)

O script Python contido no apêndice do trabalho executa, em sequência:

1. Download e resample dos dados (`yfinance`)
2. Estatísticas descritivas e decomposição aditiva (`seasonal_decompose`)
3. Testes de raiz unitária em nível e em diferença (`adfuller`, `kpss`, `arch.unitroot`)
4. FAC/FACP da série diferenciada (`plot_acf`, `plot_pacf`)
5. Estimação dos modelos ARIMA candidatos (`statsmodels.tsa.arima.model.ARIMA`)
6. Diagnóstico dos resíduos (`acorr_ljungbox`, `jarque_bera`, `shapiro`, `het_arch`)
7. Previsão fora da amostra e cálculo de métricas de erro (ME, RMSE, MAE, MAPE, MASE)

### Dependências

```
yfinance
pandas
numpy
matplotlib
statsmodels
scipy
arch
```

### Saídas geradas pelo script

- `dados_ouro.csv` — série mensal completa
- `fig1_serie_nivel.png`, `fig2_decomposicao.png`, `fig3_serie_diferenciada.png`, `fig4_fac_facp.png`, `fig_previsao_*.png`
- `tabela1_raiz_unitaria_nivel.csv`, `tabela2_raiz_unitaria_diferenca.csv`
- `tabela3_criterios_informacao.csv`
- `tabela4_ljungbox.csv`, `tabela5_normalidade_arch.csv`
- `tabela6_observado_previsto.csv`, `tabela7_medidas_erro.csv`
