# Prompt — Controle Mensal de Receita x Despesa (Escritório)

> Arquivo de instruções para ser buscado pelo Claude quando o Ricardo pedir a tarefa "controle mensal de receita x despesa" (ou similar, ex: "roda o ezmensal"). Cole este conteúdo no início da conversa (ou aponte o link raw deste arquivo no GitHub) para que a tarefa seja executada da forma combinada — direto, sem reperguntar o que já está definido aqui.

## Objetivo

Manter abastecida a planilha **EZ 2026** (Receita x Despesa) do escritório, mensalmente, e produzir duas leituras-chave:

1. **No dia 18 de cada mês:** quanto falta faturar para fechar o mês corrente com superávit.
2. **No dia 1º de cada mês:** a posição consolidada do resultado do mês anterior (fechamento).

## Fontes de dados

- **Planilha EZ 2026 (Receita x Despesa — a ser abastecida):**
  https://docs.google.com/spreadsheets/d/1A4X8xZFw3Lvk_9BsKssNBzXz_VoiYs8jChVY8ACcY58/edit
  - Aba **RECEITAS**: receita por cliente, mês a mês.
  - Aba **DESPESAS**: despesas por categoria/fornecedor, mês a mês.

- **Planilha "ESCRITÓRIO - Bancos | Caixa" (fonte de apoio/consulta):**
  https://docs.google.com/spreadsheets/d/1EHGPYgk3dKkUBuqXMcuSzgeGjOqMkNYw9mwojSjEWfc/edit
  - Aba **SICREDI**: extrato completo da conta corrente (ordenado cronologicamente, com saldo). A área usada tem >2000 linhas; pode estar com um grupo de linhas recolhido no topo — usar Ctrl+J (caixa de nome) para pular direto à data desejada em vez de rolar.
  - Aba **CAIXA**: movimentação em dinheiro/espécie.

- **Extrato do ASAAS:** fornecido manualmente pelo Ricardo a cada execução (upload de arquivo `.xlsx`, cabeçalho geralmente na linha 3). Nele:
  - identificar a **receita vinda de clientes** (recebimentos, campo `Descrição` costuma trazer "fatura nr. NNNN <nome do cliente>. Nota fiscal...");
  - identificar **tarifas bancárias / taxas do ASAAS** (despesas);
  - identificar **transferências Pix para o Sicredi** — são transferência interna (o Ricardo move o saldo do Asaas para a conta operacional no Sicredi), **não é receita nem despesa**, ignorar.

## Passo a passo da execução

1. Pedir ao Ricardo o extrato do ASAAS do período em aberto, se ainda não tiver sido enviado.
2. Classificar cada lançamento do extrato ASAAS em: receita de cliente / taxa Asaas (ignorar por enquanto — só lança no fechamento do mês seguinte) / transferência Pix interna (ignorar) / outro (perguntar).
3. Montar a lista `cliente → valor` da receita do período.
4. **Mapear cada cliente do extrato para a linha correta da aba RECEITAS** (ver seção "Como mapear clientes" abaixo — muitos nomes do Asaas não batem literalmente com o nome na planilha).
5. Lançar os valores na aba RECEITAS, na coluna "Realizado" do mês corrente (ver "Layout das colunas" abaixo).
6. **Conferir a planilha de bancos e caixa (SICREDI + CAIXA)** no mesmo período, procurando:
   - Receita de cliente que caiu **direto no banco ou no caixa sem passar pelo Asaas** (ex.: PIX direto, depósito em espécie). Indício forte: o valor bate exatamente com o "Previsto" do mês daquele cliente na aba RECEITAS — mas **lançar só depois de identificar/confirmar a linha do cliente**, nunca por semelhança de nome sozinha.
   - Despesas operacionais que não vêm do Asaas (aluguel, telecom, software, impostos, retiradas de sócios, etc.). **Sempre perguntar ao Ricardo a linha exata da aba DESPESAS antes de lançar** — os nomes do banco raramente batem literalmente com a descrição da despesa na planilha (ex.: "MARQUES E CASSOL LTDA ME" no banco = linha "IMPRESSORAS MANUTENÇÃO/TONER" na planilha). Se o nome do fornecedor/categoria ainda não existir na aba, perguntar onde criar a linha nova (a convenção é ordem alfabética dentro da seção).
   - Diferenças entre valor previsto e valor realmente recebido (ex.: caixa registrado a menor por erro de digitação) — perguntar ao Ricardo qual valor vale antes de lançar.
7. Regras já validadas para lançamentos recorrentes específicos (não perguntar de novo, a menos que o Ricardo avise que mudou):
   - **CIEE**: o pagamento do CIEE registrado no Sicredi normalmente conta para o **mês anterior** ao mês em que aparece no extrato (corte de mês civil). Se aparecer **mais de uma ocorrência de CIEE no mesmo mês**, perguntar ao Ricardo — pode ser encerramento de contrato e nesse caso conta para o mês corrente mesmo.
   - **"SAQUE POR CAIXA (nome)"** no Sicredi = transferência interna banco → caixa (confere com uma entrada de mesmo valor na aba CAIXA no mesmo dia). Não é despesa nem receita, ignorar.
   - **"PG DIULY" / "DIULY DA VEIGA CORREA" / "DIULY ... - PF" / "DIULY ... - SOCIETÁRIO"**: são linhas diferentes da aba DESPESAS (retiradas/pró-labore de sócia, pagas em parcelas ao longo do mês) — somar todas as ocorrências do mês por linha antes de lançar (ex.: duas saídas no Sicredi no mesmo mês para a mesma linha = lançar a soma).
   - **Mais de uma fatura do mesmo cliente no Asaas no mesmo mês** (ex.: mensalidade atrasada do mês anterior + mensalidade do mês): lançar a **soma** na linha do cliente, no mês em que o dinheiro entrou (regime de caixa).
   - **Serviço avulso de um cliente** (ex.: alteração de contrato social, abertura, serviço extra cobrado em fatura separada): lançar na **mesma linha do cliente**, somado à mensalidade, se a linha existir. Ex.: DE LA VEGA (R$ 1.300 mensalidade + R$ 1.621 extra = R$ 2.921 em out/2026); JULIANO ELIAS STREDA LTDA (R$ 1.200 de alteração contratual, out/2026).
   - **Mudança permanente de honorário de cliente**: ajustar o "Previsto" do mês corrente e dos meses seguintes. Ex.: COMPLEXO ESPORTIVO PADEL CENTER 232 passou de R$ 834 para R$ 450 a partir de out/2026.
   - **Lançamentos agendados no Sicredi com data futura** (ex.: FGTS, DCTFWEB, DAS, LEFISC, ISSQN lançados antecipadamente na planilha de bancos): não lançar na leitura do dia 18 enquanto a data não passou — entram no fechamento do mês.
8. Gerar a leitura correspondente à data de execução (ver seção "Saída esperada").

## Mapa de despesas (banco/caixa → linha da aba DESPESAS)

Já confirmados pelo Ricardo (lançar direto, sem perguntar de novo):

| Lançamento no Sicredi / Caixa | Linha na aba DESPESAS |
|---|---|
| FV COMP | FV COMP |
| BRPHONIA | BRPHONIA |
| ABRU TELECOM | ABRU TELECOM - TELEFONE |
| RGE | ENERGIA ELÉTRICA - RGE |
| R M ADMINISTRADORA LTDA | ALUGUEL \| RM ADMINISTRADORA LTDA |
| JB SOFTWARE | JB SOFTWARE |
| OTTIMIZZA | OTTIMIZZA |
| GUSTAVO HANSEL & CIA LTDA | GUSTAVO HANSEL GH - E-MAIL E CURSOS |
| MARQUES E CASSOL LTDA ME | IMPRESSORAS MANUTENÇÃO/TONER - L M MARQUES E CASSOL |
| PAGO GUARDA (caixa) | LOJA SIMBÓLICA FAROL GUIAS DAS MISSÕES - GUARDA |
| CLICK CAMISETAS | UNIFORMES - CLICK CAMISETAS (linha criada em out/2026) |
| FGTS / FGTS DIGITAL / FGTS RESCISÃO (qualquer funcionário) | FGTS |
| Rescisão de funcionário (ex.: CLAUDIA SMANIOTTO R$ 7.124,98 em out/2026) | linha do próprio funcionário |
| PG RICARDO (caixa) | RICARDO EMILIO ZIMMERMANN - LUCROS (retirada de lucros) |
| SEFAZ - GA \<CLIENTE\> (guia paga pelo escritório para cliente) | seção TAXAS E IMPOSTOS DE CLIENTES (A RESSARCIR), linha "\<CLIENTE\> \| GA" — criar se não existir (ex.: TARIFA ZERO \| GA, criada em out/2026; PERFITELHAS \| GA) |

Qualquer fornecedor fora desta tabela: perguntar ao Ricardo antes de lançar.

## Como editar a planilha (Google Sheets) — método validado

- **Método preferido (validado em out/2026): conector Google Sheets** (`mcp__Google_Sheets__*`).
  - Ler a aba com `get_values` (ex.: `RECEITAS!A1:AC200`) — o resultado inclui linhas ocultas, então a posição de cada linha na lista = número real da linha (linha 1 = primeiro item).
  - Gravar com `update_values` por **endereço absoluto**, de preferência a coluna inteira do mês em uma única chamada (ex.: `RECEITAS!Y10:Y155` com `null` nas linhas que não devem ser alteradas — `null` pula a célula, `""` apaga). Imune a linhas ocultas.
  - Mandar números como número (ex.: `1072.29`), nunca como texto com vírgula.
  - Para linha nova: `insert_dimension` (ROWS, `inheritFromBefore: true`) **dentro** do intervalo das fórmulas de total (assim a SUM do topo se expande sozinha), inserindo de baixo para cima quando forem várias; depois `update_formulas` com nome, fórmulas de TOTAL 2026 (colunas B/C) copiadas da linha vizinha e a coluna R (`=Q<linha>/$Q$3`) na DESPESAS. Atenção: a planilha está em locale pt-BR — em fórmulas, separador de argumentos é `;`.
  - **Conferir depois de gravar**: reler `A<ini>:A<fim>` (nomes) e a coluna do mês para checar que cada valor caiu no nome certo, e conferir o total "Realizado" do topo (linha 3) contra a soma esperada.
- **Alternativa (se o conector não estiver disponível): Claude in Chrome** (`mcp__claude-in-chrome__*`), não o browser embutido (`Claude_Browser`) — o browser embutido não consegue commitar edições de teclado no grid do Google Sheets.
  - **Nunca editar em sequência com "Enter" para descer linha por linha.** A aba RECEITAS tem linhas ocultas/agrupadas (ex.: linha 16), e o Google Sheets pula linhas ocultas ao pressionar Enter — isso desalinha os lançamentos.
  - Para cada célula, usar a **Caixa de Nome** (Ctrl+J), digitar o endereço absoluto (ex. `W86`), Enter, digitar o valor, Enter.
  - Conferir o total "Realizado" do topo a cada lote de 10-15 lançamentos.

## Layout das colunas (confirmado — pode mudar se o Ricardo reorganizar a planilha, reconferir se os totais não baterem)

- **Aba RECEITAS**: cada mês ocupa 2 colunas (Previsto | Realizado), a partir de `E` (Janeiro), com uma coluna extra de "%" depois de Julho (coluna `S`). Sequência: E/F=Jan, G/H=Fev, I/J=Mar, K/L=Abr, M/N=Mai, O/P=Jun, Q/R=Jul, S=(%), T/U=Ago, V/W=Set, X/Y=Out, Z/AA=Nov, AB/AC=Dez. Linhas de cliente começam na linha 10 (linha 9 é o cabeçalho "EMPRESAS CLIENTES").
- **Totais do topo da RECEITAS** (linha 2 = Orçado, linha 3 = Realizado): desde 19/10/2026 todos os meses usam `=SUM(<col>10:<col>160)`. Antes os intervalos variavam (começavam na linha 11 ou 12 e alguns paravam na 159), o que deixava de fora a 604 MEDIA HOUSE (linha 10), a ACPM (linha 11) e a última linha (WILLIAN DONEL LOPES). Se inserir linha nova antes da linha 160 o intervalo se expande sozinho; se acrescentar depois da última linha, ajustar as fórmulas.
- **Aba DESPESAS**: cada mês ocupa 2 colunas (Previsto | Realizado): D/E=Jan, F/G=Fev, H/I=Mar, J/K=Abr, L/M=Mai, N/O=Jun, P/Q=Jul, R=(%), S/T=Ago, U/V=Set, W/X=Out, Y/Z=Nov, AA/AB=Dez. Cabeçalho na linha 7-8; linhas de despesa começam na 12. Totais do topo somam da linha 12 até a última linha da seção A RESSARCIR (108 em out/2026).
- **Sempre confirmar a coluna do mês corrente antes de lançar em massa** (ex.: olhar o cabeçalho da linha 7-8 da aba), porque o layout pode mudar de um ano para o outro.
- Linhas novas (cliente novo, fornecedor novo) devem ser inseridas em ordem alfabética dentro da seção correspondente, copiando a formatação/fórmulas de TOTAL 2026 (colunas B/C ou C/D, que costumam ser `=E<linha>+G<linha>+...` somando as colunas Previsto, e o mesmo para Realizado) da linha vizinha.

## Como mapear clientes (Asaas → linha da planilha)

Nomes do extrato Asaas raramente batem literalmente com o nome na aba RECEITAS. Mapeamentos já confirmados pelo Ricardo (usar direto, sem perguntar de novo):

| Nome no extrato Asaas / banco | Nome/linha na planilha RECEITAS |
|---|---|
| ASSOC DOS PRODUTORES... | APRHOROSA |
| ASSOCIACAO DE AMIGOS BALNEARIO PILATTI... | ASSOCIAÇÃO BALNEÁRIO PILATTI |
| ASSOCIACAO DE FAM. E AMIGOS... | AFAPENE |
| ASSOCIACAO SANTA-ROSENSE... | ASOA |
| GALL MOVEIS SOB MEDIDA LTDA | GALL MOVEIS PLANEJADOS LTDA |
| IMOBILIARIA SULINA LTDA | IMOBILIÁRIA SULINA LTDA |
| NAIARA T. HOPPEN LTDA | NAIARA HOPPEN LTDA |
| WEIAND & NAUMANN LTDA | WEIAND E NAUMANN LTDA |
| JOÃO C. JUNGES / JOELMIR G. WINCK / VANDO K. HARTMANN / VOLMIR F. PASCH LTDA | **LEGVITA** (nome já renomeado na planilha) |
| RAFAELA MEDINA | RAFAELA MEDINA LTDA |
| PATRICIA TERESINHA BARBOSA SUCOLOTTI LTDA | PATRÍCIA BARBOSA SUCOLOTTI LTDA |
| ASSOCIACAO CASA ROTARIA DOS CLUBES DE ROTARY... | ASSOCIACAO CASA ROTARIA CLUBES DE ROTARY DE SANTA ROSA |
| E R ZANCAN CORRETORA DE SEGUROS LTDA | ER ZANCAN CORRETORA DE SEGUROS LTDA |
| FABRICIO BATISTA GONCALVES LTDA | FABRÍCIO BATISTA GONÇALVES |
| MARIA M ZERBIN | MARIA M. ZERBIN |
| SAMARA GOTTEMS DONATTI | SAMARA GOTTEMS DONATTI (seção PESSOAS FÍSICAS) |
| VICTORIO JOSE SUCOLOTTI | VICTORIO JOSE SUCOLOTTI (seção PESSOAS FÍSICAS) |
| ELIAS RIBEIRO FERNANDES | ELIAS RIBEIRO FERNANDES (seção RECEITAS EXTRAS) |

Se aparecer um nome novo sem correspondência óbvia, **perguntar ao Ricardo a linha exata** em vez de adivinhar — ele prefere sempre confirmar lançamentos ambíguos. Uma vez confirmado, adicionar a esta tabela (editar este arquivo e commitar/pedir para o Ricardo atualizar o repositório).

Clientes que recebem **direto no banco/caixa sem passar pelo Asaas**, todo mês (conferir sempre, mas a lista já vista inclui): APADA, JAQUELINE RIBAS DO AMARAL TRENTIN LTDA, LUIZA RIGO MAGALHAES (RURAL), MARCENARIA FITZ LTDA ME, MARIO RITTER, LETÍCIA PARISE CLINICA DE FISIOTERAPIA LTDA, WALTER FISCHER, PAULO FRANCISCO SCHMITT ("PAULO F SCHMITT" no Sicredi), JOSE ALBINO ROHR (caixa), ALINE SERAFIM DALLAGNOL LTDA (caixa — linha 16, que fica oculta), DIOGENES SCHRODER e WURSTER CIA LTDA (Sicredi, normalmente no último dia útil do mês).

## Parâmetros já definidos (não perguntar de novo)

- **Meta de superávit:** R$ 84.000,00/mês (baseado no ponto de equilíbrio informado pelo Ricardo — não usar o valor calculado por fórmula na planilha caso ele divirja).
- **Taxas Asaas** (tarifas da própria plataforma): ignorar durante o lançamento do período aberto; são lançadas de uma vez no fechamento do mês (início do mês seguinte).
- **Transferências Pix Asaas → Sicredi:** transferência interna, não é receita nem despesa.
- **Corte do período:** mês civil (dia 1º ao último dia do mês). Exceção: o pagamento do CIEE no Sicredi normalmente conta para o mês anterior (ver regra específica acima).
- **Lançamentos ambíguos:** sempre confirmar com o Ricardo antes de lançar — ele prefere isso a qualquer suposição automática, inclusive quando o nome do banco não bate exatamente com a planilha.

## Agenda (Google Calendar)

Dois lembretes recorrentes mensais na agenda do Ricardo, para que ele peça esta tarefa:

- Todo dia **18** do mês → leitura "quanto falta faturar para fechar com superávit".
- Todo dia **1º** do mês → posição consolidada do mês anterior.

Regra de ajuste: se a data (18 ou 1º) cair em **sábado, domingo ou feriado**, mover para o **primeiro dia útil seguinte** (feriados nacionais brasileiros, e municipais/estaduais de Santa Rosa/RS se souber — confirmar com o Ricardo se necessário).

## Saída esperada de cada execução

Uma mensagem objetiva com:
- o cálculo pedido (quanto falta faturar, ou o fechamento do mês anterior);
- o detalhamento de receitas e despesas lançadas nesta execução (por linha/valor);
- os totais "Realizado" do mês conferidos batendo com a soma esperada;
- qualquer pendência ou lançamento que precisou (ou ainda precisa) de confirmação do Ricardo.
