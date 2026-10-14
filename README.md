# Pricing Optimization — Elasticidade-Preço da Demanda

Projeto de ciência de dados que estima a **elasticidade-preço da demanda** dos produtos de um café e encontra o **preço que maximiza o lucro** de cada um.

## Problema de negócio

Um café vende quatro itens (hambúrguer, Coca-Cola, limonada e café), avulsos ou em combos. Cada produto vendável tem um identificador (`SELL_ID`). Para cada `SELL_ID`, o projeto responde a duas perguntas:

1. **Quanto a quantidade vendida cai quando o preço sobe?** (elasticidade-preço)
2. **Qual preço gera o maior lucro?**

## Dados

| Arquivo | Conteúdo |
|---|---|
| `Cafe+-+Transaction+-+Store.csv` | Uma linha por dia e `SELL_ID`, com o preço praticado e a quantidade vendida (5.404 linhas, de jan/2012 a set/2015) |
| `Cafe+-+Sell+Meta+Data.csv` | O que compõe cada `SELL_ID`: item avulso (`SELL_CATEGORY = 0`) ou combo (`SELL_CATEGORY = 2`) |
| `Cafe+-+DateInfo.csv` | Calendário: feriado, fim de semana, recesso escolar, temperatura média e se a área externa estava aberta |

Só 4 dos 7 `SELL_ID` têm histórico de vendas: o hambúrguer avulso e três combos.

## Metodologia

1. **Preparação**: as datas dos arquivos vêm em formatos diferentes e são convertidas para `datetime` antes do join. Feriados nulos viram `"No Holiday"`, e as vendas são agregadas por `SELL_ID`, data e preço.
2. **Isolamento do "dia normal"**: feriados, fins de semana e recessos escolares são removidos, para que a variação na demanda reflita o preço e não o calendário. Ficam 52% das linhas.
3. **Modelo de demanda**: uma regressão linear (OLS) por `SELL_ID`:

   $$\text{QUANTITY} = \beta_0 + \beta_1 \cdot \text{PRICE} + \varepsilon$$

   A elasticidade é calculada no ponto médio como $\beta_1 \cdot \bar{P} / \bar{Q}$.
4. **Otimização**: para uma grade de preços, calcula-se o lucro previsto

   $$\text{lucro}(p) = (p - \text{custo}) \times \widehat{Q}(p)$$

   e escolhe-se o preço de maior lucro. Entram na otimização só os produtos com inclinação negativa e significativa (p < 0,05).
5. **Teste de robustez**: toda a análise é repetida só com os dias em que a área externa estava aberta (`IS_OUTDOOR = 1`).

## Resultados

| `SELL_ID` | Produto | Elasticidade | Preço médio atual | Preço ótimo | Aumento de lucro previsto |
|---|---|---|---|---|---|
| 1070 | Hambúrguer | -1,33 | 15,12 | 18,96 | +34% |
| 2053 | Hambúrguer + Café + Coca | -1,30 | 11,91 | 15,26 | +48% |
| 2052 | Hambúrguer + Limonada | -1,11 | 11,97 | 15,95 | +51% |
| 2051 | Hambúrguer + Coca | -0,32 | 12,34 | — | sem relação significativa (p ≈ 0,053) |

O aumento de lucro compara o lucro previsto pelo modelo no preço ótimo com o lucro previsto no preço médio praticado hoje.

**Principais conclusões**

- O hambúrguer avulso e os combos 2052 e 2053 têm demanda **elástica** (elasticidade abaixo de -1), mas o custo unitário assumido deixa pouca margem nos preços atuais. Por isso o modelo recomenda preços mais altos, que reduzem o volume e aumentam o lucro.
- O combo Hambúrguer + Coca não mostrou relação estatisticamente confiável entre preço e quantidade e ficou fora da recomendação.
- Repetir a análise só com dias de área externa aberta quase não muda os resultados: a elasticidade varia no máximo cerca de 0,05 e o preço ótimo de -0,20 a +0,30. Isso acontece porque 88% dos dias normais já têm a área externa aberta.

## Limitações e próximos passos

- Os preços ótimos valem para **dias normais**. Feriados, fins de semana e recessos precisariam de modelos próprios.
- O **custo unitário é uma premissa** (90% do menor preço já praticado), porque o custo real não está nos dados.
- A demanda foi modelada como **linear**, então as previsões perdem confiabilidade fora da faixa de preços observada. Modelos log-log ou exponenciais dariam elasticidade constante e uma extrapolação mais segura.
- Próximo passo: aplicar os novos preços de forma gradual e comparar vendas e lucro reais com a previsão.

## Tecnologias

Python · pandas · NumPy · statsmodels · Matplotlib · seaborn · Jupyter

## Como executar

```bash
git clone https://github.com/Ravi-silva/pricing-optimization.git
cd pricing-optimization
python -m venv .venv
.venv\Scripts\activate        # Windows  (Linux/macOS: source .venv/bin/activate)
pip install -r requirements.txt
jupyter notebook V3_Price_Optimization_price_elasticity.ipynb
```

Os três CSVs precisam estar na mesma pasta do notebook.
