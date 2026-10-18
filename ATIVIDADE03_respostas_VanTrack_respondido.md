**Exercício 1 - Regra de Negócio × Requisito**
**RESPOSTA: A Diferença.** Uma **Regra de Negócio (RN)** é uma política, restrição ou condição do domínio que existe independentemente do software — ela continuaria valendo se o processo fosse feito em papel. Ela diz *o que o negócio permite, exige ou proíbe*. Um **Requisito de Software** descreve *o que o sistema deve fazer* (requisito funcional) ou *com que qualidade/restrição deve fazê-lo* (requisito não funcional) para atender aos objetivos dos usuários e, muitas vezes, para fazer cumprir as regras de negócio. Em resumo: a RN pertence ao negócio; o requisito pertence à solução.

| **Item**                                                                  | **Classificação**                        | **Justificativa**                                                                                                                                                                           |
|---------------------------------------------------------------------------|------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| a\) Todo empréstimo deve possuir prazo máximo de 30 dias.                 | **Regra de Negócio**                     | É uma política da biblioteca/empresa; vale com ou sem sistema. O sistema apenas fará com que seja respeitada.                                                                               |
| b\) O sistema deve permitir registrar um empréstimo.                      | **Requisito Funcional**                  | Descreve uma funcionalidade que o software oferece ao usuário (“o sistema deve permitir…”).                                                                                                 |
| c\) Usuários administradores devem utilizar autenticação em dois fatores. | **Requisito Não Funcional (Segurança)**  | É uma restrição de qualidade sobre *como* o acesso ao sistema é protegido; depende da tecnologia. (Pode ter origem numa política de segurança da organização, mas como enunciado é um RNF.) |
| d\) Clientes com pagamentos vencidos não podem realizar novas compras.    | **Regra de Negócio**                     | É uma restrição do domínio comercial; vale mesmo numa loja sem software. Gera requisitos como “o sistema deve bloquear a compra de clientes inadimplentes”.                                 |
| e\) O sistema deve responder às consultas em até 2 segundos.              | **Requisito Não Funcional (Desempenho)** | Define um atributo de qualidade mensurável do sistema, não uma política do negócio.                                                                                                         |

**Exercício 2 — Requisitos Funcionais e Não Funcionais**

| **Requisito (sistema de biblioteca)**               | **Tipo**      | **Categoria (se RNF)**                   |
|-----------------------------------------------------|---------------|------------------------------------------|
| Permitir empréstimo de livros                       | Funcional     | —                                        |
| Permitir consulta ao catálogo                       | Funcional     | —                                        |
| Suportar 500 usuários simultâneos                   | Não funcional | Desempenho / Escalabilidade (capacidade) |
| Exigir autenticação para operações administrativas  | Não funcional | Segurança (controle de acesso)           |
| Funcionar nos principais navegadores atuais         | Não funcional | Compatibilidade / Portabilidade          |
| Apresentar resultados de pesquisa em até 2 segundos | Não funcional | Desempenho (tempo de resposta)           |

**Por que RNF não são “menos importantes”?**

- **Eles podem inviabilizar o sistema inteiro.** Um catálogo que funciona, mas demora 40 segundos por consulta ou cai com 50 usuários, não é usado. A funcionalidade existe, mas o objetivo não é atingido.
- **Impactam a arquitetura.** Desempenho, segurança, disponibilidade e compatibilidade definem escolhas de tecnologia, infraestrutura e distribuição de responsabilidades. Descobri-los tarde custa muito caro, porque muitas vezes exigem refazer a base do sistema, e não apenas uma tela.
- **Atravessam vários requisitos funcionais.** Um único RNF (ex.: autenticação) afeta dezenas de funcionalidades; esquecê-lo gera falhas em todo o sistema.
- **Têm consequências legais e de risco.** Falhas de segurança e privacidade (por exemplo, LGPD) geram prejuízo financeiro e de imagem.
- **Podem e devem ser testados.** Quando escritos com métrica (2 s, 500 usuários), são critérios de aceite tão objetivos quanto os funcionais.

**Exercício 3 — Por que Casos de Uso?**

**RESPOSTA:** A afirmação é problemática porque requisito funcional e caso de uso descrevem coisas diferentes. O RF diz *o que* o sistema deve oferecer, geralmente em uma frase (“o sistema deve permitir registrar um empréstimo”). Ele não diz *como o ator alcança o objetivo*, em que ordem as coisas acontecem, o que acontece quando algo dá errado nem quem participa.

O Caso de Uso acrescenta:

- **Contexto e objetivo do ator** — quem inicia, por que, e o que considera sucesso.
- **Sequência de interação** — o diálogo passo a passo entre ator e sistema.
- **Pré-condições, pós-condições e gatilho** — quando o comportamento pode começar e em que estado o sistema fica.
- **Fluxos alternativos e de exceção** — o que acontece quando o caminho feliz não acontece. É aqui que normalmente aparecem as regras de negócio (“e se o aluno tiver empréstimo vencido?”).
- **Ligação entre requisitos** — um UC costuma realizar vários RF e aplicar várias RN; ele mostra como se combinam.
- **Base para teste, protótipo e projeto** — cada fluxo vira cenário de teste e é refinado em diagramas de sequência.

Ou seja, os RF são a lista de capacidades; os UC mostram o comportamento completo e revelam lacunas que a lista sozinha esconde.

**Exercício 4 — Caso de Uso não é tela**

| **Item**              | **Classificação**      | **Justificativa**                                                            |
|-----------------------|------------------------|------------------------------------------------------------------------------|
| Abrir Tela de Cliente | Ação de interface      | Não entrega valor sozinha; é um meio de navegação.                           |
| Clicar em Salvar      | Ação de interface      | É um gesto na tela, um passo dentro de um UC.                                |
| **Cadastrar Cliente** | **Bom candidato a UC** | Objetivo completo do ator, com resultado de valor (cliente registrado).      |
| Digitar CPF           | Passo interno          | Entrada de dado dentro de um UC (ex.: Cadastrar Cliente).                    |
| **Registrar Pedido**  | **Bom candidato a UC** | O ator alcança um objetivo de negócio (pedido registrado).                   |
| Selecionar Produto    | Passo interno          | Parte do fluxo de Registrar Pedido; não encerra um objetivo.                 |
| **Cancelar Pedido**   | **Bom candidato a UC** | Objetivo próprio, com regras próprias (prazo, estorno) e resultado de valor. |

Critério usado: um caso de uso é nomeado por **verbo + objeto** que representa um **objetivo do ator** e produz **resultado observável de valor**. Itens que dependem de uma tela específica ou que são apenas um passo do caminho não se sustentam sozinhos.

**Exercício 5 — Atores**

1)  **RESPOSTA: Podem ser atores:** Estudante, Professor, Coordenador, Funcionário (secretaria) e o **Serviço Externo de Autenticação** (ator secundário, sistema externo).

2)  **RESPOSTA: Sim.** Ator é qualquer entidade *externa* ao sistema que interage com ele — pessoa, outro sistema, dispositivo ou até o tempo (agendador). O serviço de autenticação é externo e troca mensagens com o sistema, portanto é ator (normalmente secundário). No VanTrack, por exemplo, o Serviço de Geolocalização e o Serviço de Notificação são atores secundários, e o “Tempo (agendador)” é ator principal dos casos de uso automáticos.

3)  **RESPOSTA: Sim.** Ator representa um **papel**, não uma pessoa. Um professor que também é coordenador assume dois papéis, conforme o caso de uso. No VanTrack, o aluno maior de idade pode ser ao mesmo tempo “Aluno” e responsável por si.

4)  **RESPOSTA: Normalmente não.** O banco de dados é parte interna da solução (camada de persistência), não algo externo com objetivos próprios. Colocá-lo como ator mistura análise com projeto técnico. Só faria sentido se fosse um **sistema externo legado**, mantido por outra organização, com o qual o sistema precisa se integrar.

**Parte 2 — Identificação de Regras, Requisitos e Casos de Uso**

**Exercício 6 — Sistema de estacionamento**

**Regras de Negócio**

- **RN01** — Todo veículo deve ter placa e horário de entrada registrados no momento da entrada.
- **RN02** — O valor a pagar é calculado conforme o período de permanência (entre o horário de entrada e o de saída), de acordo com a tabela de preços vigente.
- **RN03** — Clientes conveniados recebem 20% de desconto sobre o valor calculado.
- **RN04** — A saída do veículo só pode ser liberada após o pagamento registrado.
- **RN05** — Todas as operações (entrada, pagamento e saída) devem ficar registradas no histórico.
- **RN06** — Não pode haver duas entradas abertas (sem saída) para a mesma placa *(regra implícita, a validar com o cliente)*.

**Requisitos Funcionais**

- **RF01** — O sistema deve registrar a entrada do veículo com placa, data e horário.
- **RF02** — O sistema deve calcular o valor da permanência no momento da saída.
- **RF03** — O sistema deve identificar se o cliente é conveniado e aplicar o desconto de 20%.
- **RF04** — O sistema deve permitir que o atendente registre o pagamento e a forma de pagamento.
- **RF05** — O sistema deve permitir liberar a saída somente após pagamento registrado.
- **RF06** — O sistema deve manter e permitir consultar o histórico de operações.
- **RF07** — O sistema deve permitir manter o cadastro de clientes conveniados e da tabela de preços.

**Requisitos Não Funcionais (possíveis)**

- **RNF01 (Desempenho)** — O cálculo do valor na saída deve ser exibido em até 2 segundos.
- **RNF02 (Confiabilidade)** — Os registros do histórico não podem ser perdidos nem alterados sem trilha de auditoria.
- **RNF03 (Segurança)** — Somente atendentes autenticados podem registrar pagamentos e liberar saídas.
- **RNF04 (Usabilidade)** — O registro de entrada deve ser feito em no máximo duas ações (ex.: digitar placa e confirmar).
- **RNF05 (Disponibilidade)** — O sistema deve estar disponível durante todo o horário de funcionamento do estacionamento.

**Atores**

- **Atendente** (principal) — registra entradas, pagamentos e libera saídas.
- **Cliente** — beneficiário; interage com o atendente (não necessariamente com o sistema).
- **Gerente/Administrador** — mantém tabela de preços, conveniados e consulta o histórico.
- *(Possível)* **Leitor de placas / cancela automática** — sistema externo, se houver automação.

**Casos de Uso**

- **UC01** — Registrar Entrada de Veículo (RF01; RN01, RN06)
- **UC02** — Registrar Saída e Pagamento (RF02, RF03, RF04, RF05; RN02, RN03, RN04)
- **UC03** — Consultar Histórico de Operações (RF06; RN05)
- **UC04** — Manter Clientes Conveniados (RF07)
- **UC05** — Manter Tabela de Preços (RF07; RN02)

**Exercício 7 — Sistema acadêmico**

| **RN**                                                           | **Requisitos Funcionais derivados**                                                                                                                                                                                | **Casos de Uso afetados**                                                                  |
|------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------|
| RN01 — Nota entre 0 e 100                                        | **RF01** — O sistema deve validar que toda nota lançada ou alterada está no intervalo de 0 a 100, recusando valores fora dele.                                                                                     | UC Lançar Notas; UC Alterar Nota; UC Importar Notas (se houver)                            |
| RN02 — Só o professor responsável altera notas                   | **RF02** — O sistema deve verificar se o usuário é o professor responsável pela turma antes de permitir lançar/alterar notas.                                                                                      | UC Lançar Notas; UC Alterar Nota; UC Manter Turma (definição do professor responsável)     |
| RN03 — Após fechamento do período, notas não podem ser alteradas | **RF03** — O sistema deve permitir que a coordenação feche o período letivo. **RF04** — O sistema deve bloquear lançamento/alteração de notas de períodos fechados.                                                | UC Fechar Período; UC Lançar Notas; UC Alterar Nota                                        |
| RN04 — Frequência \< 75% reprova por frequência                  | **RF05** — O sistema deve permitir registrar a frequência dos estudantes. **RF06** — O sistema deve calcular o percentual de frequência e definir a situação “reprovado por frequência” quando for inferior a 75%. | UC Registrar Frequência; UC Calcular Situação Final / Fechar Período; UC Consultar Boletim |

Observação: RN01–RN03 aparecem principalmente como **exceções** nos UCs de lançamento/alteração (“nota fora do intervalo”, “usuário não é o responsável”, “período fechado”); RN04 aparece no cálculo da situação final.

**Parte 3 — Analisando Casos de Uso**

**Exercício 8 — Encontre os problemas**

**Problemas identificados**

1.  **Nome vago:** “Cadastro” não diz *o quê* é cadastrado nem segue verbo + objeto.
2.  **Ator genérico:** “Usuário” não identifica o papel (atendente? cliente?).
3.  **Passos orientados a tela:** “Abrir a tela”, “Clicar no botão” descrevem interface, não intenção (mistura UC essencial com real).
4.  **Passos sem sujeito:** os passos 1–3 não dizem quem age; o 6 (“Finaliza”) não diz quem finaliza nem o quê.
5.  **Dados indefinidos:** “Digitar os dados” — quais dados? Quais são obrigatórios?
6.  **Validação vaga:** “Sistema verifica” — verifica o quê? Contra qual regra?
7.  **Sem fluxos alternativos e exceções:** nada acontece se o dado for inválido ou duplicado.
8.  **Sem objetivo, pré-condições, pós-condições e gatilho.**
9.  **Sem rastreabilidade:** não referencia RN nem RF.
10. **Sem resultado para o ator:** o ator não recebe confirmação.

**Caso de Uso reescrito**

> **UC01 — Cadastrar Cliente**
>
> **Objetivo:** Registrar um novo cliente para que ele possa realizar pedidos. **Ator principal:** Atendente · **Atores secundários:** — **Gatilho:** Um novo cliente solicita cadastro. **Pré-condições:** Atendente autenticado. **Pós-condições:** Cliente registrado e ativo, disponível para pedidos.
>
> **Fluxo principal** 1. Atendente solicita o cadastro de um novo cliente. 2. Sistema solicita nome, CPF, e-mail e telefone. 3. Atendente informa os dados do cliente. 4. Sistema valida os campos obrigatórios e o formato do CPF e do e-mail (RN01). 5. Sistema verifica se já existe cliente com o mesmo CPF (RN02). 6. Sistema registra o cliente como ativo. 7. Sistema informa que o cadastro foi concluído.
>
> **Fluxos alternativos** - **A1 (Cliente sem e-mail)** — No passo 3, o atendente não informa e-mail → sistema aceita, pois o campo é opcional, e segue no passo 4.
>
> **Exceções** - **E1 (Dados inválidos)** — No passo 4: sistema indica os campos inválidos, mantém o que foi preenchido e retorna ao passo 3. - **E2 (CPF já cadastrado)** — No passo 5: sistema informa que o cliente já existe e oferece abrir o cadastro existente; UC encerrado sem novo registro.
>
> **Regras:** RN01 — campos obrigatórios e formatos válidos; RN02 — CPF único. **Requisitos:** RF01 — Cadastrar cliente.

**Exercício 9 — Caso de Uso incompleto**

**RESPOSTA: Análise.** O UC05 descreve só o caminho feliz e **não aplica nenhuma das quatro regras**:

| **Regra**                                        | **Situação no UC original** | **O que falta**                                            |
|--------------------------------------------------|-----------------------------|------------------------------------------------------------|
| RN01 — Aluno com empréstimo vencido não empresta | Não verificada              | Passo de verificação + exceção “aluno com pendência”       |
| RN02 — Máximo 5 livros simultâneos               | Não verificada              | Contagem de empréstimos ativos + exceção “limite atingido” |
| RN03 — Livro indisponível não empresta           | Não verificada              | Verificação da disponibilidade do exemplar + exceção       |
| RN04 — Prazo de 14 dias                          | Não aplicada                | O sistema deve calcular e informar a data de devolução     |

Faltam também: objetivo, pré/pós-condições, gatilho, exceções de aluno/livro não encontrado, registro da mudança de estado do exemplar e a confirmação ao ator.

**Versão corrigida**

> **UC05 — Realizar Empréstimo**
>
> **Objetivo:** Registrar o empréstimo de um ou mais livros para um aluno apto. **Ator principal:** Bibliotecário · **Ator beneficiado:** Aluno **Gatilho:** Aluno apresenta livro(s) no balcão para empréstimo. **Pré-condições:** Bibliotecário autenticado; aluno e livros cadastrados. **Pós-condições:** Empréstimo registrado com data de devolução; exemplar marcado como emprestado.
>
> **Fluxo principal** 1. Bibliotecário informa a identificação do aluno. 2. Sistema localiza o aluno. 3. Sistema verifica se o aluno possui empréstimos vencidos (RN01). 4. Sistema verifica a quantidade de livros emprestados ao aluno (RN02). 5. Bibliotecário informa o exemplar do livro. 6. Sistema verifica se o exemplar está disponível (RN03). 7. Sistema calcula a data de devolução: data atual + 14 dias (RN04). 8. Sistema registra o empréstimo e marca o exemplar como emprestado. 9. Sistema informa o empréstimo realizado e a data de devolução.
>
> **Fluxos alternativos** - **A1 (Mais de um livro)** — Após o passo 8, bibliotecário informa outro exemplar → retorna ao passo 4 (o limite é verificado novamente).
>
> **Exceções** - **E1 (Aluno não encontrado)** — No passo 2: sistema informa e retorna ao passo 1. - **E2 (Empréstimo vencido)** — No passo 3: sistema bloqueia o empréstimo, lista os itens vencidos; UC encerrado (RN01). - **E3 (Limite atingido)** — No passo 4: com 5 livros ativos, sistema bloqueia novos empréstimos; UC encerrado (RN02). - **E4 (Livro indisponível)** — No passo 6: sistema informa a situação do exemplar e retorna ao passo 5 (RN03).
>
> **Regras:** RN01, RN02, RN03, RN04.

**Parte 4 — Fluxos Alternativos e Exceções**

**Exercício 10 — O happy path não é suficiente**

**RESPOSTA: Fluxos alternativos** (outro caminho válido até o mesmo objetivo)

| **ID**       | **Situação**                                      | **Passo** | **Tratamento**                                                           |
|--------------|---------------------------------------------------|-----------|--------------------------------------------------------------------------|
| A1           | Cliente aplica cupom de desconto                  | 3         | Sistema valida o cupom e recalcula o valor; segue para o passo 4.        |
| A2           | Cliente escolhe retirar na loja em vez de entrega | 2         | Sistema dispensa o endereço, zera o frete e segue para o passo 3.        |
| A3           | Cliente paga com PIX em vez de cartão             | 4–5       | Sistema gera o código PIX, aguarda a confirmação e segue para o passo 6. |
| A4 *(extra)* | Cliente usa endereço já cadastrado                | 2         | Sistema preenche os dados e segue para o passo 3.                        |

**Fluxos de exceção** (algo impede o objetivo no caminho normal)

| **ID**       | **Situação**                                       | **Passo**               | **Tratamento**                                                                                        |
|--------------|----------------------------------------------------|-------------------------|-------------------------------------------------------------------------------------------------------|
| E1           | Produto sem estoque                                | 1 (ou revalidação no 6) | Sistema informa e remove/permite ajustar o item; se nada restar, UC encerrado.                        |
| E2           | CEP inválido / região não atendida                 | 2–3                     | Sistema informa que não entrega no local e retorna ao passo 2.                                        |
| E3           | Pagamento recusado                                 | 5                       | Sistema informa a recusa e retorna ao passo 4 para outra forma de pagamento; pedido não é registrado. |
| E4 *(extra)* | Gateway de pagamento indisponível / tempo esgotado | 5                       | Sistema informa a falha, não cobra, e permite tentar novamente.                                       |

**Exercício 11 — Fluxo alternativo ou exceção?**

| **Situação**                                      | **Classificação** | **Justificativa**                                                                                  |
|---------------------------------------------------|-------------------|----------------------------------------------------------------------------------------------------|
| a\) Cliente escolhe PIX em vez de cartão          | **Alternativo**   | Outro caminho legítimo que leva ao mesmo objetivo (compra concluída).                              |
| b\) Cartão é recusado                             | **Exceção**       | Algo deu errado e impede o passo de pagamento; exige tratamento/recuperação.                       |
| c\) Cliente adiciona outro produto                | **Alternativo**   | Variação normal do fluxo, escolha do ator; o objetivo continua sendo alcançado.                    |
| d\) Produto fica sem estoque antes da confirmação | **Exceção**       | Condição de erro externa à vontade do ator que impede concluir como planejado.                     |
| e\) Cliente informa cupom de desconto             | **Alternativo**   | Caminho opcional e válido (poderia até virar `<<extend>>` “Aplicar Cupom”).                        |
| f\) Cupom está vencido                            | **Exceção**       | Falha de uma regra de negócio (validade do cupom). Note que é exceção *dentro* do alternativo “e”. |

**Todas precisam estar no mesmo Caso de Uso?** Não necessariamente. Ficam no mesmo UC as variações curtas e ligadas ao seu objetivo (pagamento recusado, sem estoque). Quando uma variação fica grande, é reutilizada em outros UCs ou é opcional com comportamento próprio, ela deve ser separada:

- `<<extend>>` para comportamento opcional e condicional — ex.: “Aplicar Cupom de Desconto” estende “Realizar Compra”, e suas exceções (cupom vencido, cupom esgotado) ficam nele.
- `<<include>>` para comportamento obrigatório reaproveitado — ex.: “Processar Pagamento” incluído por “Realizar Compra” e “Renovar Assinatura”, concentrando PIX, cartão e recusa.
- Situações como “adicionar outro produto” normalmente pertencem a outro UC (“Manter Carrinho”).

O critério é manter cada UC focado em **um objetivo**, legível, e sem duplicar comportamento.

**Parte 5 — Testando a qualidade de um Caso de Uso (projeto VanTrack)**

**Exercício 12 — Revisão por checklist**

**RESPOSTA: Caso de uso escolhido:** **UC-S04 — Cancelar Utilização do Transporte**, do documento de casos de uso do VanTrack (entrega do 1º bimestre). Ele é central no produto, pois dispara a notificação ao motorista (UC-I01) e o recálculo da rota (UC-X02).

**Versão analisada (resumo do repositório)**

> Ator principal: Responsável ou Aluno · Atores secundários: Motorista Pré-condições: presença previamente confirmada; **embarque ainda não registrado**. Pós-condições: aluno ausente, com data/hora do cancelamento; motorista notificado. Gatilho: usuário escolhe “Cancelar presença”. 1. Usuário seleciona o aluno e o trajeto com presença confirmada. 2. Sistema verifica se o embarque ainda não foi registrado. 3. Sistema pede confirmação. 4. Usuário confirma. 5. Sistema altera status para ausente e registra data/hora. 6. Sistema executa UC-I01 («include»). 7. Sistema exibe “Presença cancelada”. Ponto de extensão: após o passo 5, se a rota já foi gerada → UC-X02. Exceções: E1 — embarque já registrado (passo 2). RN: RN-006, RN-007, RN-008 · Requisitos: RF-005, RNF-002

**Aplicação do checklist**

| **Item**                  | **Situação** | **Observação**                                                                                 |
|---------------------------|--------------|------------------------------------------------------------------------------------------------|
| Identificador             | ✔            | UC-S04                                                                                         |
| Nome orientado a objetivo | ✔            | Verbo + objeto, objetivo do ator                                                               |
| Ator principal            | ✔            | Responsável ou Aluno                                                                           |
| Atores secundários        | ◐            | Falta o **Serviço de Notificação**, usado no UC-I01                                            |
| Objetivo                  | ✔            | Presente                                                                                       |
| Gatilho                   | ✔            | Presente                                                                                       |
| Pré-condições             | ✘            | “Embarque ainda não registrado” está como pré-condição **e** como exceção E1 — contradição     |
| Pós-condições             | ◐            | Não cobre o caso de rota recalculada (UC-X02), nem distingue sucesso e falha                   |
| Fluxo principal           | ◐            | Verificação do embarque só no passo 2, antes da confirmação; não revalida no momento de gravar |
| Fluxos alternativos       | ✘            | Não há fluxo alternativo (ex.: usuário desiste no passo 4; cancelar só um dos trajetos)        |
| Exceções                  | ◐            | Só E1; falta perda de conectividade e falha na notificação                                     |
| Regras relacionadas       | ◐            | Falta RN-002 (o responsável só pode cancelar presenças de seus próprios alunos)                |
| Requisitos relacionados   | ◐            | Cita RF-005, mas não RF-006 e RF-008, realizados via include/extend; falta RNF-003             |
| Resultado esperado        | ◐            | Implícito no passo 7 e nas pós-condições; não há campo explícito                               |

**Registro dos problemas**

| **Problema**                                                       | **Por que é um problema?**                                                                                                                                     | **Correção proposta**                                                                                                                                     |
|--------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| “Embarque ainda não registrado” é pré-condição e também exceção E1 | Pré-condição é algo garantido antes do UC começar; se é garantido, a E1 nunca ocorreria. O documento fica contraditório e confunde quem vai testar.            | Remover da pré-condição e manter como verificação no passo 2 + exceção E1. Pré-condição: “usuário autenticado e vinculado ao aluno; presença confirmada”. |
| Embarque verificado só antes da confirmação do usuário             | Entre o passo 2 e o 5 o motorista pode registrar o embarque (o aluno está entrando na van). O sistema cancelaria uma presença já embarcada, violando a RN-006. | Revalidar a RN-006 no momento da gravação (passo 6 corrigido) e criar a exceção E2 “Embarque registrado durante a confirmação”.                           |
| Não há tratamento para o usuário desistir                          | O fluxo só termina em cancelamento; o protótipo não teria “voltar”.                                                                                            | Fluxo alternativo A1: no passo 4, o usuário desiste → presença permanece confirmada.                                                                      |
| RN-002 não aplicada                                                | Sem verificar o vínculo, um usuário poderia cancelar a presença de aluno de outra família (falha de privacidade e de segurança, RNF-002).                      | Passo 2: sistema verifica se o usuário é responsável pelo aluno (RN-002); exceção E3 acesso negado.                                                       |
| Sem exceção de conectividade                                       | RNF-003 e o UC-R01 (E2 “Sem conexão”) mostram que o caso é real; o UC de sistema não o descreve.                                                               | Exceção E4: sem conexão no passo 6 → sistema informa que o cancelamento não foi registrado e permite nova tentativa.                                      |
| Falha no envio da notificação não tratada                          | A pós-condição promete “motorista notificado”, mas o push pode falhar. Se a falha desfizesse o cancelamento, o responsável seria prejudicado.                  | Registrar o cancelamento mesmo assim; a notificação fica pendente para reenvio (tratado no UC-I01). Ajustar a pós-condição.                               |
| Atores secundários e requisitos incompletos                        | Prejudica a rastreabilidade (Ex. 15): mudanças no RF-006/RF-008 não apontariam para o UC-S04.                                                                  | Incluir Serviço de Notificação como ator secundário e RF-006, RF-008, RNF-003 nos requisitos relacionados.                                                |
| Cancelamento de ida e volta                                        | Cada presença é por trajeto (UC-R01 mostra “Ida” e “Volta”); o UC não diz se é possível cancelar os dois de uma vez.                                           | Fluxo alternativo A2: usuário seleciona mais de um trajeto; os passos 5–7 se repetem para cada um.                                                        |

**UC-S04 corrigido**

> **UC-S04 — Cancelar Utilização do Transporte**
>
> **Objetivo:** Cancelar uma presença confirmada para refletir imprevistos na operação da rota. **Ator principal:** Responsável ou Aluno · **Atores secundários:** Motorista, Serviço de Notificação **Gatilho:** Usuário escolhe “Cancelar presença”. **Pré-condições:** Usuário autenticado e vinculado ao aluno; presença confirmada para o trajeto. **Pós-condições (sucesso):** Presença com status “ausente”, data e hora do cancelamento registradas; motorista notificado (ou notificação pendente de reenvio); rota recalculada, se já gerada. **Pós-condições (falha):** Presença permanece confirmada.
>
> **Fluxo principal** 1. Usuário seleciona o aluno e o trajeto com presença confirmada. 2. Sistema verifica se o usuário é responsável pelo aluno (RN-002). 3. Sistema verifica se o embarque ainda não foi registrado (RN-006). 4. Sistema pede a confirmação do cancelamento. 5. Usuário confirma. 6. Sistema revalida que não há embarque registrado, altera o status para “ausente” e registra data e hora (RN-006). 7. Sistema executa **UC-I01 — Notificar Motorista sobre Cancelamento** («include», RN-007). 8. Sistema exibe o status “Presença cancelada”.
>
> **Ponto de extensão:** após o passo 6, se a rota do dia já tiver sido gerada, executa **UC-X02 — Recalcular Rota** (RN-008).
>
> **Fluxos alternativos** - **A1 (Usuário desiste)** — No passo 5, o usuário não confirma → presença permanece confirmada; UC encerrado. - **A2 (Ida e volta)** — No passo 1, o usuário seleciona mais de um trajeto → os passos 3 a 7 são executados para cada trajeto.
>
> **Exceções** - **E1 (Embarque já registrado)** — No passo 3: sistema não permite o cancelamento e informa o motivo (RN-006); UC encerrado. - **E2 (Embarque registrado durante a confirmação)** — No passo 6: sistema não grava o cancelamento e informa que o aluno acabou de embarcar; UC encerrado. - **E3 (Usuário sem vínculo com o aluno)** — No passo 2: sistema bloqueia a operação (RN-002); UC encerrado. - **E4 (Sem conectividade)** — No passo 6: sistema informa que o cancelamento não foi registrado e permite tentar novamente (RNF-003).
>
> **Regras:** RN-002, RN-006, RN-007, RN-008 **Requisitos:** RF-005, RF-006 (via UC-I01), RF-008 (via UC-X02), RNF-002, RNF-003 **Resultado esperado:** o aluno não é mais esperado no trajeto, o motorista fica sabendo e a rota deixa de passar pelo ponto do aluno.

**Exercício 13 — Teste das perguntas (UC-S04, fluxo original)**

| **Passo**                                      | **QUEM?**           | **O QUÊ?**             | **SOBRE O QUÊ?**                                            | **E SE NÃO DER CERTO?**                                                 | **QUAL REGRA?**                     | **O QUE ACONTECE DEPOIS?**                                           |
|------------------------------------------------|---------------------|------------------------|-------------------------------------------------------------|-------------------------------------------------------------------------|-------------------------------------|----------------------------------------------------------------------|
| 1\. Usuário seleciona aluno e trajeto          | Responsável/Aluno ✔ | Seleciona ✔            | Aluno + trajeto ✔ — **? pode escolher ida e volta juntos?** | **? Aluno sem presença confirmada não deveria aparecer**                | **? RN-002 não citada**             | Passo 2 ✔                                                            |
| 2\. Sistema verifica embarque                  | Sistema ✔           | Verifica ✔             | Registro de embarque do trajeto ✔                           | E1 ✔                                                                    | RN-006 ✔                            | Passo 3 ✔                                                            |
| 3\. Sistema pede confirmação                   | Sistema ✔           | Solicita confirmação ✔ | Cancelamento ✔                                              | **? Usuário pode desistir — não descrito**                              | —                                   | Passo 4 ✔                                                            |
| 4\. Usuário confirma                           | Usuário ✔           | Confirma ✔             | Cancelamento ✔                                              | **? E se o motorista registrou o embarque enquanto o usuário decidia?** | **? RN-006 deveria ser reaplicada** | Passo 5 ✔                                                            |
| 5\. Sistema altera status e registra data/hora | Sistema ✔           | Grava ✔                | Presença ✔                                                  | **? Sem conexão ou falha ao gravar — não descrito**                     | RN-006 ✔                            | **? Extensão UC-X02 ocorre aqui — consta só como ponto de extensão** |
| 6\. Sistema executa UC-I01                     | Sistema ✔           | Notifica ✔             | Motorista ativo da van ✔                                    | **? Push falha: o cancelamento é desfeito?**                            | RN-007, RN-012 ✔                    | Passo 7 ✔                                                            |
| 7\. Sistema exibe “Presença cancelada”         | Sistema ✔           | Exibe ✔                | Status ✔                                                    | —                                                                       | —                                   | **? Fim do UC; não diz se a rota foi atualizada**                    |

**Perguntas que revelaram informações ausentes**

- **E SE NÃO DER CERTO?** — a mais produtiva: revelou a desistência do usuário (A1), a corrida entre confirmação e embarque (E2), a falta de conectividade (E4) e a falha na notificação.
- **QUAL REGRA SE APLICA?** — revelou que a RN-002 (acesso restrito aos alunos do responsável) não estava aplicada, e que a RN-006 precisa ser verificada de novo no momento da gravação.
- **SOBRE O QUÊ?** — revelou a ambiguidade entre cancelar um trajeto ou ambos (A2).
- **O QUE ACONTECE DEPOIS?** — mostrou que o resultado para o usuário não informa o efeito na rota, e que a pós-condição não cobre a extensão UC-X02.
- **QUEM?** e **O QUÊ?** não revelaram lacunas: todos os passos já têm sujeito e ação claros, o que mostra que o documento da equipe já estava bem escrito nesses aspectos.

Todas essas descobertas foram incorporadas no UC-S04 corrigido do Exercício 12.

**Parte 6 — Rastreabilidade**

**Exercício 14 — Construa a cadeia**

**RN07** — Descontos superiores a 15% devem ser autorizados pelo gerente.

↓

**RF12** — O sistema deve permitir que o vendedor aplique desconto a uma venda e, quando o desconto for superior a 15%, deve exigir a autorização de um usuário com perfil de gerente antes de concluir a venda, registrando quem autorizou, quando e o percentual.

↓

**UC08 — Registrar Venda** (ator principal: Vendedor; ator secundário: Gerente)

1.  Vendedor informa os produtos da venda.
2.  Sistema calcula o valor total.
3.  Vendedor informa o percentual de desconto.
4.  Sistema verifica se o desconto é de até 15% (RN07).
5.  Sistema aplica o desconto e recalcula o total.
6.  Vendedor confirma a venda.
7.  Sistema registra a venda.

↓

**Fluxo Alternativo A1 — Desconto acima de 15% (passo 4)**

- A1.1 Sistema informa que o desconto exige autorização do gerente.
- A1.2 Gerente se identifica com suas credenciais.
- A1.3 Sistema verifica se o usuário tem perfil de gerente.
- A1.4 Gerente autoriza o desconto.
- A1.5 Sistema registra a autorização (gerente, data/hora, percentual) e retorna ao passo 5.
- *Exceções do A1:* gerente nega → retorna ao passo 3 para novo desconto; credencial sem perfil de gerente → autorização recusada, retorna ao A1.2.

↓

**Critérios de Aceitação**

- CA1 — Desconto de até 15% (inclusive) é aplicado sem pedir autorização.
- CA2 — Desconto acima de 15% não é aplicado sem autorização de um gerente.
- CA3 — Credenciais de usuário sem perfil de gerente não autorizam o desconto.
- CA4 — Toda autorização fica registrada com gerente, data/hora e percentual.
- CA5 — Se o gerente negar, a venda não é concluída com o desconto solicitado.

↓

**Cenários de Teste**

| **ID** | **Dado**                 | **Quando**                            | **Então**                                                           | **CA**   |
|--------|--------------------------|---------------------------------------|---------------------------------------------------------------------|----------|
| CT1    | Venda de R\$ 100         | Vendedor aplica 15%                   | Total = R\$ 85, sem pedido de autorização (valor-limite)            | CA1      |
| CT2    | Venda de R\$ 100         | Vendedor aplica 15,01%                | Sistema exige autorização do gerente                                | CA2      |
| CT3    | Desconto de 20% pendente | Gerente autoriza                      | Total = R\$ 80; autorização registrada com gerente, data/hora e 20% | CA2, CA4 |
| CT4    | Desconto de 20% pendente | Outro vendedor informa sua credencial | Autorização recusada; desconto não aplicado                         | CA3      |
| CT5    | Desconto de 20% pendente | Gerente nega                          | Venda não concluída com 20%; vendedor volta a informar desconto     | CA5      |

Todos os elementos usam o mesmo limite (**“superior a 15%”**, ou seja, 15% exatos não exigem autorização) e o mesmo papel (**gerente**), mantendo coerência com a RN07.

**Exercício 15 — Matriz de rastreabilidade (VanTrack)**

Matriz construída a partir do documento de regras de negócio, do documento de requisitos e do documento de casos de uso da entrega do 1º bimestre.

| **Regra**                                    | **Requisito(s)**                               | **Caso(s) de Uso**                             | **Fluxo/Cenário**                                   |
|----------------------------------------------|------------------------------------------------|------------------------------------------------|-----------------------------------------------------|
| RN-001 — Acesso por código da empresa        | RF-001, RF-013, RNF-002                        | UC-B02, UC-S01, UC-S08                         | UC-S01 passo 3; E1 (código inválido), E2 (expirado) |
| RN-002 — Acesso restrito ao aluno vinculado  | RF-001, RF-002, RF-003, RF-011, RF-012, RF-013 | UC-S01, UC-S02, UC-S08, UC-S10, UC-I02, UC-I03 | UC-S02 passo 2 / E2; UC-S08 passo 3 / E1            |
| RN-003 — Consentimento para localização      | RF-002, RF-003, RF-016                         | UC-B02, UC-S02, UC-S10, UC-C01                 | UC-C01 passos 5–6; pré-condição do UC-S02           |
| RN-004 — Prazo de 30 min para confirmar      | RF-004, RF-007                                 | UC-S03, UC-E01, UC-R01, UC-S09                 | UC-S03 passo 3 / E1; gatilho do UC-S09              |
| RN-005 — Rota só com confirmados             | RF-004, RF-007, RF-008                         | UC-S03, UC-S09, UC-X02                         | UC-S09 passos 1–4 / A1                              |
| RN-006 — Cancelar antes do embarque          | RF-005, RF-006, RF-008                         | UC-S04, UC-X02                                 | UC-S04 passo 2 / E1                                 |
| RN-007 — Notificar motorista no cancelamento | RF-006                                         | UC-S04, UC-I01                                 | UC-S04 passo 6 («include» UC-I01)                   |
| RN-008 — Recalcular rota após cancelamento   | RF-008                                         | UC-S04, UC-X02                                 | Ponto de extensão do UC-S04; UC-X02 / A1            |
| RN-009 — Menor vinculado a responsável       | RF-001, RF-011, RF-016                         | UC-S01, UC-C01, UC-X01, UC-I02                 | UC-X01 passo 5; UC-I02 A1                           |
| RN-010 — Registrar embarque e desembarque    | RF-009, RF-010, RF-011                         | UC-S05, UC-S06, UC-E02, UC-R02                 | UC-S05 passos 1–2; UC-R02 passo 3                   |
| RN-011 — Desembarque exige embarque          | RF-010                                         | UC-S06, UC-E02                                 | UC-S06 passo 3 / E1                                 |
| RN-012 — Um motorista ativo por van          | RF-002, RF-007, RF-012, RF-014                 | UC-ADM01, UC-S09, UC-I01                       | UC-ADM01 passos 4–5; UC-I01 passo 2                 |
| RN-013 — Chat restrito por vínculo           | RF-013                                         | UC-S08, UC-I03                                 | UC-S08 passo 3 / E2                                 |
| RN-014 — Atraso comunicado a todos           | RF-012, RF-015                                 | UC-S07, UC-S11, UC-I03                         | UC-S11 passo 3 / A1 (limiar 10 min)                 |
| RN-015 — *(cancelada)*                       | → RNF-003, RNF-006                             | —                                              | Reclassificada como RNF                             |
| RN-016 — Autorização para dados de menores   | RF-016                                         | UC-C01, UC-X01                                 | Extensão do UC-C01 (passo 4); UC-C01 E2             |

**Existem regras sem requisitos?** Na matriz oficial, não: todas as regras ativas têm RF. A RN-015 foi corretamente cancelada (era restrição técnica) e virou RNF-003/RNF-006. Porém a análise mais detalhada encontrou **partes de regras sem requisito**:

- **RN-014** diz que o limiar de 10 minutos “pode ser alterado pela empresa de transporte”, mas **nenhum RF ou UC permite configurar esse limiar**.
- **RN-001** depende de a empresa **gerar e desativar códigos de vinculação**, mas só existe o RF de *usar* o código (RF-001), não o de emiti-lo.
- **RN-003** cita o motorista como stakeholder, mas não há requisito para registrar o **consentimento do motorista** sobre a própria localização.

**Existem requisitos sem Casos de Uso?** Não. Todos os RF-001 a RF-016 são realizados por pelo menos um UC (sistema, inclusão, extensão ou administrativo). Os RNF não têm UC próprio, o que é esperado: eles aparecem como restrições dentro dos fluxos (ex.: RNF-006 no A1 do UC-S05).

**Existem comportamentos sem relação aparente com requisito ou regra?** Sim, alguns:

- O “**Desfazer embarque em até 10 segundos**”, que existia no UC-R02, não tinha RN nem RF — a equipe já o removeu na revisão.
- **UC-A01 — Manter Cadastro Base** não tem RF: é um UC abstrato, um padrão de reutilização.
- **Login/autenticação e cadastro de usuário** são exigidos como pré-condição em quase todos os UCs, mas não há RF nem UC para eles.
- O “**limite de proximidade configurado**” (RF-003, UC-S10) é usado, mas não há requisito dizendo quem o configura.
- Os UCs de inclusão (UC-I01, I02, I03) não são pedidos diretamente por um ator, mas são justificados pelas RN-007, RN-010 e RN-014.

**Isso necessariamente representa um erro?** Não. Cada caso deve ser analisado:

- **UC-A01** é uma **decisão legítima de modelagem** (reaproveitamento), não precisa de RF.
- **Login, cadastro de usuário, configuração de limiares e emissão de códigos** indicam **requisitos incompletos**: são comportamentos necessários que deveriam virar novos RF (ex.: “RF-017 — Emitir código de vinculação”; “RF-018 — Configurar limiar de atraso”).
- O **“Desfazer embarque”** era **comportamento sem origem** — foi corretamente removido, ou poderia ser mantido se a equipe criasse uma regra justificando-o.

A lacuna só é erro quando o comportamento contraria ou não pode ser justificado pela análise. A matriz serve justamente para provocar essa discussão.
