# Previsão de atingimento de meta mensal por equipe — contrato Manutenção AT

Código-fonte do TCC do MBA em Ciência de Dados (Unifor), orientação de Jorge Araújo.

**Problema.** Prever, no início do mês, quais equipes do contrato Manutenção AT não atingirão a meta financeira mensal. É uma classificação binária supervisionada, com alvo `atingiu_meta = 1` se `receita_mes >= meta_mes`, e cada amostra representa um par equipe × mês.

**Abordagem.** Os modelos usam apenas variáveis ex-ante, isto é, conhecidas antes do início do mês. Três modelos supervisionados (regressão logística, Ridge, Random Forest) e duas linhas de base (maioria e persistência, que repete o resultado do mês anterior) são comparados em 8 dobras de origem móvel. A comparação principal usa as 187 previsões fora de dobra; o período ago–dez/2025 é apenas descritivo, porque foi consultado em decisões analíticas anteriores. **Nenhum modelo final foi validado**: a logística e a persistência são estatisticamente indistinguíveis nesta amostra.

## Estrutura

```
notebooks/   notebook executado (scikit-learn 1.6.1): construção da base, modelagem e período descritivo
data/        base agregada (ver data/README_dados.md) — arquivos brutos não são publicados
figures/     figuras exportadas pelo notebook (PNG 300 dpi / PDF)
results/     métricas congeladas (fora de dobra e período descritivo), para conferência
```

## Como executar

1. Abrir `notebooks/previsao_meta_equipes.ipynb` no Google Colab ou em um ambiente local.
2. Instalar as dependências com `pip install -r requirements.txt`.
3. **Com os dados brutos** (detentor dos dados): colocar em `data/raw/` `Execucao_Contrato.xlsx`, `Medicao_por_Equipes.xlsx` e `config_local.json` (nomes de equipes usados nas correções de grafia) e executar todas as células em ordem.
   - A Seção 2 recodifica as equipes como `EQ01…EQ52` e grava a correspondência em `data/raw/mapa_equipes.csv`.
4. **Sem os dados brutos:** executar a célula 0.1 (imports) e, em seguida, a partir da Seção 3, com `base_modelagem_manutencao_at.csv` no diretório de trabalho.
5. Comparar as métricas obtidas com `results/metricas_holdout.csv` (Seção 6.1–6.2 do notebook).

`data/raw/` está no `.gitignore`: dados brutos, configuração local e tabela de correspondência das equipes nunca entram no repositório.

Semente aleatória: `random_state=42` em todos os procedimentos estocásticos, inclusive no bootstrap.

| Ambiente | Uso | Python | scikit-learn | pandas | NumPy |
|---|---|---|---|---|---|
| Notebook (Colab) | construção da base, modelagem, período ago–dez/2025 (Tabelas 8–9, Figuras 5–7 do artigo) | 3.12.10 | 1.6.1 | 2.2.3 | 2.1.3 |
| Reexecução | comparação fora de dobra, k=3, importâncias, análise de erros, estresse (Tabelas 5, 7, 10; Figuras 4, 8, 9) | 3.12.13 | 1.8.0 | 2.2.3 | 2.3.5 |

As médias de ROC AUC nas 8 dobras e os hiperparâmetros escolhidos coincidem nos dois ambientes (logística 0,7418, Ridge 0,7469, Random Forest 0,7493). Versões travadas do notebook: `requirements.txt`.

## Protocolo e resultados

| Item | Valor |
|---|---|
| Janela dos dados | mar/2024 a dez/2025, 52 equipes, 513 amostras após o descarte do 1º mês de cada equipe |
| Desenvolvimento | n=366, abr/2024–jul/2025, 8 dobras de origem móvel, 187 previsões fora de dobra (43 da classe 0) |
| Período descritivo | n=147, ago–dez/2025 — consultado em decisões anteriores; não é avaliação confirmatória |
| Atributos | 12 variáveis ex-ante |
| Hiperparâmetros (GridSearchCV, ROC AUC nas 8 dobras) | logística `C=0,01`; Ridge `alpha=100`; Random Forest 400 árvores, profundidade 3, folha 1; todos com `class_weight='balanced'` |

**Comparação principal — 187 previsões fora de dobra, limiar 0,50** (IC 95% por bootstrap de meses inteiros; `results/metricas_oof.csv`)

| Método | ROC AUC agrupada | Recall₀ | Precisão₀ | F1₀ |
|---|---|---|---|---|
| Persistência | 0,7093 | 0,4651 | 0,4348 | 0,4494 |
| Regressão logística | 0,7075 | 0,5116 | 0,4783 | 0,4944 |
| Ridge | 0,7062 | 0,4884 | 0,4667 | 0,4773 |
| Random Forest | 0,7259 | 0,3721 | 0,5161 | 0,4324 |

Os intervalos pareados das diferenças logística − persistência incluem zero ou o tocam: os métodos não se separam. Em k=3 equipes por mês, logística, Ridge e persistência empatam com 15 acertos em 24 seleções.

**Período ago–dez/2025 (descritivo)** — regressão logística ROC AUC 0,8237 [0,750; 0,894], recall₀ 0,6286, matriz `[[22, 13], [23, 89]]`; persistência 0,8069. Intervalos por reamostragem de linhas.

## Antivazamento

Toda coluna da base é classificada como ID, RÓTULO, MESMO-MÊS ou EX-ANTE, e apenas as EX-ANTE entram como feature.

- `receita_mes`, `razao_atingimento` e `cobriu_custo` participam da definição do rótulo e são excluídas.
- Todo defasamento usa `.shift(1)` após ordenação por equipe e mês.
- Padronização e imputação são ajustadas apenas no treino, via `Pipeline` + `ColumnTransformer`.

## Política de dados

Ver [`data/README_dados.md`](data/README_dados.md). Os arquivos brutos do contrato não são publicados.

## Licença

O código está sob licença MIT (ver `LICENSE`). Os dados não estão cobertos por essa licença.
