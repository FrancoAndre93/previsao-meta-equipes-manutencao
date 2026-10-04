# Política de compartilhamento dos dados

## Fontes originais (não publicadas)

O trabalho usa três fontes internas do contrato Manutenção AT, cobrindo mar/2024 a dez/2025:

1. **Apontamento de execução e faturamento.** São os registros por atividade executada, e o faturamento usado é `valor_total`.
2. **Catálogo de serviços.** Contém o preço unitário licitado e a capacidade produtiva por código de serviço.
3. **Metas mensais por equipe.**

Esses arquivos contêm preços contratuais, faturamento detalhado e identificação de equipes. Por isso, **não são publicados neste repositório**. Estão bloqueados no `.gitignore`: `data/raw/` e `*.xlsx`.

## O que é publicado

**[PENDENTE — depende de autorização da coordenação e da empresa]**

Formato previsto: `base_modelagem_anonimizada.csv`, com a base agregada equipe × mês. As transformações de anonimização são:

- equipes recodificadas como `EQ01…EQ52`;
- colunas monetárias multiplicadas por um fator constante não divulgado.

Razões, indicadores de atingimento e defasamentos não se alteram com essa transformação. Os modelos usados também são invariantes a esse reescalonamento: a regressão logística porque as variáveis são padronizadas, e o Random Forest por construção. Assim, as Seções 3 a 6 do notebook reproduzem os números do artigo.

## Como obter os dados completos

**[PENDENTE]** — informar o responsável pela autorização e o procedimento de solicitação.
