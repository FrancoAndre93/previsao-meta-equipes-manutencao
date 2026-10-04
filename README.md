# Previsão de atingimento de meta mensal por equipe — contrato Manutenção AT

Código-fonte do TCC do MBA em Ciência de Dados (Unifor), orientação de Jorge Araújo.

**Problema.** Prever, no início do mês, quais equipes do contrato Manutenção AT não atingirão a meta financeira mensal. É uma classificação binária supervisionada, com alvo `atingiu_meta = 1` se `receita_mes >= meta_mes`, e cada amostra representa um par equipe × mês.

**Abordagem.** O modelo usa apenas variáveis ex-ante, isto é, conhecidas antes do início do mês. A validação é temporal, com origem móvel no desenvolvimento e holdout final. O ponto de operação é orientado ao custo assimétrico entre falso negativo e falso positivo. Todo resultado é comparado à linha de base de persistência, que repete o resultado do mês anterior.

## Estrutura

```
notebooks/   notebook final que gera todos os números e figuras do artigo
data/        base agregada (ver data/README_dados.md) — arquivos brutos não são publicados
figures/     figuras exportadas pelo notebook (PNG 300 dpi / PDF)
results/     métricas congeladas do holdout, para conferência da reprodução
```

## Como executar

1. Abrir `notebooks/Trabalho_Final_Modelo_ML_Franco_Andre_v3.ipynb` no Google Colab ou em um ambiente local.
2. Instalar as dependências com `pip install -r requirements.txt`.
3. Executar todas as células em ordem.
   - Sem os arquivos brutos em `data/raw/`, a construção da base (Seção 2) é pulada e o notebook carrega a base agregada de `data/`.
4. Comparar as métricas obtidas com `results/metricas_holdout.csv`. Os `assert` finais do notebook fazem essa conferência automaticamente.

- Semente aleatória: **[PENDENTE]**
- Python: **[PENDENTE]**
- Versões travadas: `requirements.txt`

## Protocolo e resultados esperados

| Item | Valor |
|---|---|
| Janela dos dados | mar/2024 a dez/2025, 52 equipes, 513 amostras após o descarte do 1º mês de cada equipe |
| Desenvolvimento | n=366, abr/2024–jul/2025, 74,9% positivos, 8 dobras de origem móvel |
| Holdout | n=147, ago–dez/2025, 76,2% positivos (35 amostras da classe 0) |
| Features | 12 variáveis ex-ante |
| Modelo final | Regressão logística, `C=0,01`, `class_weight='balanced'` |
| ROC AUC (holdout) | 0,8237 (IC 95% 0,750–0,894) |
| Recall classe 0, limiar 0,5 (holdout) | 0,6286 (IC 95% 0,483–0,788) |
| Persistência (linha de base) | ROC AUC 0,8069 · recall classe 0 0,5429 |

O ganho do modelo sobre a persistência é pontual e fica dentro da incerteza amostral, porque os intervalos de confiança se sobrepõem.

## Antivazamento

Toda coluna da base é classificada como ID, RÓTULO, MESMO-MÊS ou EX-ANTE, e apenas as EX-ANTE entram como feature.

- `receita_mes`, `razao_atingimento` e `cobriu_custo` participam da definição do rótulo e são excluídas.
- Todo defasamento usa `.shift(1)` após ordenação por equipe e mês.
- Padronização e imputação são ajustadas apenas no treino, via `Pipeline` + `ColumnTransformer`.

## Política de dados

Ver [`data/README_dados.md`](data/README_dados.md). Os arquivos brutos do contrato não são publicados.

## Licença

O código está sob licença MIT (ver `LICENSE`). Os dados não estão cobertos por essa licença.
