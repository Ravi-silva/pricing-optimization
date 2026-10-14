# Pricing Optimization — Price Elasticity

Otimização de preços de produtos de uma cafeteria com base na elasticidade-preço da demanda.

## Conteúdo

- `V3_Price_Optimization_price_elasticity.ipynb` — notebook principal com a análise e a otimização de preços.
- `Cafe+-+Transaction+-+Store.csv` — transações de venda.
- `Cafe+-+Sell+Meta+Data.csv` — metadados dos produtos vendidos.
- `Cafe+-+DateInfo.csv` — informações de calendário (feriados, fins de semana, clima).

## Como executar

```bash
python -m venv .venv
.venv\Scripts\activate        # Windows  (Linux/macOS: source .venv/bin/activate)
pip install -r requirements.txt
jupyter notebook V3_Price_Optimization_price_elasticity.ipynb
```

Os CSVs devem estar na mesma pasta do notebook.
