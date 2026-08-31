# Prompt — Controle Mensal de Receita x Despesa (Escritório)

> Arquivo de instruções para ser buscado pelo Claude quando o Ricardo pedir a tarefa "controle mensal de receita x despesa" (ou similar). Cole este conteúdo no início da conversa (ou aponte o link raw deste arquivo no GitHub) para que a tarefa seja executada da forma combinada.

## Objetivo

Manter abastecida a planilha de Receita x Despesa da empresa, mensalmente, e produzir duas leituras-chave:

1. **No dia 18 de cada mês:** quanto falta faturar para fechar o mês corrente com superávit.
2. **No dia 1º de cada mês:** a posição consolidada do resultado do mês anterior (fechamento).

## Fontes de dados

- **Planilha de Receita x Despesa (a ser abastecida):**
  https://docs.google.com/spreadsheets/d/1A4X8xZFw3Lvk_9BsKssNBzXz_VoiYs8jChVY8ACcY58/edit?gid=260505735#gid=260505735

- **Planilha de controle de bancos e caixa do Escritório (fonte de apoio/consulta):**
  https://docs.google.com/spreadsheets/d/1EHGPYgk3dKkUBuqXMcuSzgeGjOqMkNYw9mwojSjEWfc/edit?gid=0#gid=0

- **Extrato do ASAAS:** fornecido manualmente pelo Ricardo a cada execução (upload de arquivo ou colado no chat). Nele:
  - identificar a **receita vinda de clientes** (recebimentos);
  - identificar **tarifas bancárias / taxas do ASAAS** (despesas).

## Passo a passo da execução

1. Pedir ao Ricardo o extrato do ASAAS do período em aberto, se ainda não tiver sido enviado.
2. Ler a planilha de bancos e caixa do Escritório para cruzar/validar lançamentos (saldo, transferências entre contas, outras entradas/saídas que não passam pelo ASAAS).
3. Classificar cada lançamento do extrato ASAAS em:
   - Receita de cliente (entrada);
   - Tarifa bancária / taxa do ASAAS (despesa);
   - Outro (perguntar ao Ricardo se não estiver claro, e registrar a decisão como parâmetro — ver seção "Parâmetros" abaixo).
4. Lançar os valores classificados na planilha de Receita x Despesa, nas linhas/colunas do mês corrente.
5. Conferir se a planilha de bancos e caixa aponta algum lançamento relevante (receita ou despesa) que não veio do ASAAS e que também deveria compor o mês.
6. Gerar a leitura correspondente à data de execução:
   - **Dia 18 (ou dia útil seguinte, ver abaixo):** calcular e informar "quanto falta faturar para fechar o mês com superávit", com base no acumulado de receita e despesa lançados até a data e a meta/parâmetros definidos.
   - **Dia 1º (ou dia útil seguinte):** consolidar e informar o resultado fechado do mês anterior (receita total, despesa total, superávit ou déficit).

## Parâmetros da tarefa

Esta tarefa tem parâmetros que ainda não estão definidos e que devem ser decididos com o Ricardo **nos primeiros meses de execução**. Uma vez decidido, o parâmetro passa a valer para os meses seguintes sem precisar perguntar de novo — a menos que o Ricardo peça para mudar.

Parâmetros a definir (perguntar na primeira execução de cada um, se ainda não estiver registrado):

- O que exatamente conta como "meta de superávit" (valor fixo mensal? percentual sobre receita? cobre só despesas do Escritório ou também retiradas/pró-labore?).
- Quais categorias de despesa existem na planilha e como mapear cada tipo de lançamento do ASAAS/bancos para essas categorias.
- Como tratar lançamentos ambíguos do ASAAS (ex.: estornos, chargebacks, transferências internas).
- Formato/local exato na planilha onde cada informação deve ser lançada (linha, coluna, aba).
- Fuso/critério de corte do período (ex.: mês corrente = do dia 1º ao último dia do mês civil).

**Importante:** assim que um parâmetro for decidido com o Ricardo, grave-o na memória persistente do usuário (arquivo `/areas/financeiro-escritorio.md`) para que fique disponível em qualquer sessão futura, já que este arquivo no GitHub é só o roteiro da tarefa e não é reescrito automaticamente pelo Claude.

## Agenda (Google Calendar)

Deve haver dois lembretes recorrentes mensais na agenda do Ricardo, para que ele peça esta tarefa:

- Todo dia **18** do mês → leitura "quanto falta faturar para fechar com superávit".
- Todo dia **1º** do mês → posição consolidada do mês anterior.

Regra de ajuste: se a data (18 ou 1º) cair em **sábado, domingo ou feriado**, o lembrete deve ser movido para o **primeiro dia útil seguinte**. Considerar feriados nacionais brasileiros (e, se souber, feriados municipais/estaduais relevantes para o Ricardo — confirmar com ele se necessário). Como o Google Calendar não recalcula feriados automaticamente em uma recorrência simples, ao configurar ou revisar esses lembretes:
- ou gerar os eventos já com as datas corretas para os próximos 12 meses, ajustando manualmente cada ocorrência que cair em fim de semana/feriado;
- ou manter um evento recorrente simples (dia 18 e dia 1º) com uma nota no título/descrição pedindo para confirmar se é dia útil, e revisar/mover manualmente quando cair em data não útil.

## Saída esperada de cada execução

Uma mensagem objetiva com:
- o cálculo pedido (quanto falta faturar, ou o fechamento do mês anterior);
- o detalhamento de receitas e despesas lançadas nesta execução;
- qualquer pendência ou lançamento que precisou de confirmação do Ricardo.
