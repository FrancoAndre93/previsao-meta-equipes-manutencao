# Política de compartilhamento dos dados

## Fontes originais (não publicadas)

O trabalho usa três fontes internas do contrato Manutenção AT, cobrindo mar/2024 a dez/2025:

1. **Apontamento de execução e faturamento.** São os registros por atividade executada, e o faturamento usado é `valor_total`.
2. **Catálogo de serviços.** Contém o preço unitário licitado e a capacidade produtiva por código de serviço.
3. **Metas mensais por equipe.**

Esses arquivos contêm preços contratuais, faturamento detalhado e identificação de equipes. Por isso, **não são publicados neste repositório**. Ficam em `data/raw/`, bloqueado no `.gitignore`, junto com:

- `config_local.json`: nomes de equipes usados nas correções de grafia e no rateio de meta (§2.4);
- `mapa_equipes.csv`: correspondência nome original → código, gerada pelo notebook.

## Recodificação das equipes (já aplicada no notebook)

Na §2.4, após o casamento das 52 equipes entre apontamento e metas, cada equipe recebe um código `EQ01…EQ52`, atribuído em ordem alfabética do nome original. Com isso:

- a ordenação por `(equipe, mes)` fica idêntica, e os lags, as dobras de validação e todas as métricas se reproduzem exatamente;
- a base exportada (`base_modelagem_manutencao_at.csv`) e todas as saídas das Seções 3 a 6 exibem apenas os códigos.

`equipe` não é feature do modelo (§4.3); a coluna serve apenas como chave de agrupamento.

## O que é publicado

Nenhum dado é publicado nesta versão. A publicação da base agregada depende de autorização da empresa.

Formato previsto: `base_modelagem_anonimizada.csv`, com a base agregada equipe × mês e as equipes já recodificadas. Transformação adicional prevista: as colunas monetárias são multiplicadas por um fator constante não divulgado.

Razões, indicadores de atingimento e defasamentos não se alteram com essa transformação. Os modelos usados também são invariantes a esse reescalonamento: a regressão logística porque as variáveis são padronizadas, e o Random Forest por construção. Assim, as Seções 3 a 6 do notebook reproduzem os números do artigo.

## Como obter os dados completos

Solicitações de acesso devem ser feitas ao autor, por meio de uma *issue* neste repositório. O acesso, para fins de verificação acadêmica, fica sujeito à autorização da empresa detentora dos dados.
