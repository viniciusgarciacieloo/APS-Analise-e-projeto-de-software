Análise e Projeto de Software — 2026

**Atividade 04 — Diagrama de Sequência**

*Participantes, fragmentos alt/loop, distribuição de responsabilidades e exercício integrador*

**Projeto da equipe:** VanTrack — aplicativo de gestão e acompanhamento de vans escolares

**Integrantes:** Luís Gustavo Ribeiro de Freitas (RA143379) · Pedro Augusto dos Santos Watermann (RA145121) · Gustavo Bragada Cavalcanti (RA145116) · Amanda da Silva Palacio (RA133649) · Vinicius Garcia Del Cielo (RA143653)

*O Exercício 24 e a questão final foram resolvidos a partir dos artefatos da pasta* `entrega-1-bimestre` *do repositório VanTrack. Os diagramas foram feitos em PlantUML.*

# Parte 7 — Introdução ao Diagrama de Sequência

## Exercício 16 — Caso de Uso × Diagrama de Sequência

- O **Caso de Uso** responde *como o ator alcança seu objetivo usando o sistema*. Ele trata o sistema como uma **caixa-preta**: descreve o diálogo ator ↔ sistema, as pré e pós-condições, os fluxos alternativos e as exceções, as regras aplicadas e o valor entregue. É escrito na linguagem do negócio, para ser validado com o cliente.
- O **Diagrama de Sequência** responde *como os participantes colaboram ao longo do tempo para realizar um cenário*. Ele **abre a caixa-preta**: mostra quais objetos internos (tela, controlador, serviço, entidades, repositórios, sistemas externos) trocam quais mensagens, em que ordem, e onde ficam as decisões e repetições.

**Por que o Diagrama de Sequência não substitui o Caso de Uso?**

1.  **Cobre um cenário, não o comportamento completo.** Um diagrama normalmente representa um caminho (ou poucos, via `alt`); o UC reúne todos os caminhos de um objetivo num só lugar.
2.  **Não registra o “porquê”.** Objetivo, gatilho, pré e pós-condições, e a justificativa das regras não aparecem — ou aparecem só como anotações.
3.  **É uma visão de projeto, não de requisitos.** Ele já assume decisões técnicas (classes, camadas, métodos). O cliente valida o UC, não um diagrama com `ReservaRepository`.
4.  **Depende do UC para ser correto.** Cada mensagem deve ter origem em um passo, regra ou requisito; sem o UC não há como saber se o diagrama está certo (ver Ex. 22).
5.  **Muda por motivos diferentes.** Se a arquitetura muda (ex.: troca de banco), o diagrama muda e o UC continua igual.

Os dois são complementares: o UC define **o que** deve acontecer, e o diagrama mostra **quem faz o quê** para que aconteça.

## Exercício 17 — Identificando participantes

Cenário: transferência bancária.

| **Categoria**            | **Participante**             | **Responsabilidade**                                                                                                                         | **Justificativa**                                                                          |
|--------------------------|------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------|
| **Ator**                 | Cliente                      | Solicita a transferência, informa conta de destino e valor, confirma a operação.                                                             | É externo ao sistema e possui o objetivo.                                                  |
| **Interface / Boundary** | TelaTransferencia            | Coletar os dados, exibir confirmação e resultado; repassar a solicitação ao controle. Não contém regras.                                     | O texto diz “a interface envia a solicitação para o sistema”.                              |
| **Controle / Aplicação** | TransferenciaController      | Receber a requisição da tela e encaminhá-la ao serviço; converter o resultado em resposta.                                                   | Separa a tela da lógica de aplicação.                                                      |
|                          | TransferenciaService         | Coordenar o cenário: verificar a conta de origem, consultar o saldo, mandar validar, registrar e devolver o resultado. Controla a transação. | “O sistema verifica, consulta, valida, registra e informa” — é uma orquestração de passos. |
| **Domínio**              | Conta                        | Saber seu saldo e se está ativa; `debitar(valor)` e `creditar(valor)`, garantindo que não fique com saldo insuficiente.                      | A regra “não transferir sem saldo” pertence ao conceito de conta.                          |
|                          | Transferencia / Movimentacao | Representar a operação (origem, destino, valor, data/hora) e validar-se (valor \> 0, origem ≠ destino, limites).                             | É o registro do fato de negócio.                                                           |
| **Persistência**         | ContaRepository              | Buscar e atualizar contas.                                                                                                                   | Acesso a dados fica fora do domínio.                                                       |
|                          | MovimentacaoRepository       | Gravar a movimentação/extrato.                                                                                                               | “Registra a movimentação”.                                                                 |

Fluxo resumido: Cliente → TelaTransferencia → TransferenciaController → TransferenciaService → ContaRepository (buscar origem) → Conta.saldoSuficiente(valor) → Transferencia.validar() → Conta.debitar / Conta.creditar → MovimentacaoRepository.salvar → resposta até a tela.

# Parte 8 — Construção de Diagramas de Sequência

## Exercício 18 — Da especificação para a sequência

Mapeamento dos passos do UC03 para as mensagens do diagrama:

| **Passo do UC**                      | **Mensagens** |
|--------------------------------------|---------------|
| 1\. Professor solicita reserva       | 1             |
| 2\. Sistema solicita data e horário  | 2             |
| 3\. Professor informa data e horário | 3             |
| 4\. Sistema verifica disponibilidade | 4–9           |
| 5\. Sistema apresenta a sala         | 10            |
| 6\. Professor confirma               | 11            |
| 7\. Sistema registra a reserva       | 12–16         |
| 8\. Sistema informa a confirmação    | 17–19         |

![ex18_reservar_sala](diagramas/ex18_reservar_sala.png)

## Exercício 19 — Adicione uma decisão (RN05)

![ex19_reservar_sala_alt](diagramas/ex19_reservar_sala_alt.png)

**Como a RN05 aparece no comportamento**

- **Onde é verificada:** a regra “não pode haver duas reservas para períodos conflitantes” virou a mensagem `estaDisponivel(periodo, reservas)` na entidade **Sala** (mensagem 8). A decisão é do domínio, não da tela nem do controller.
- **O que muda no fluxo:** a resposta da verificação abre o fragmento `alt`. No ramo **\[sala disponível\]** o fluxo segue para confirmação e gravação; no ramo **\[sala indisponível\]** o sistema informa o conflito e não cria a reserva.
- **Revalidação na gravação:** ao desenhar, percebeu-se que entre “apresentar a sala” e “professor confirma” outra pessoa pode reservar o mesmo horário. Por isso a RN05 é verificada de novo (`existeConflito`, mensagem 16) antes do `salvar`. Esse detalhe não estava no UC e foi descoberto pelo diagrama.
- **Rastreabilidade:** RN05 → RF “verificar conflito de reservas” → UC03, passo 4 e exceção “sala indisponível” → ramo `alt` do diagrama.

## Exercício 20 — Adicione uma repetição

![ex20_pedido_loop](diagramas/ex20_pedido_loop.png)

**Por que** `loop` **é melhor do que repetir as mensagens manualmente**

- **A quantidade é variável.** Um pedido pode ter 1 ou 50 produtos; repetir as mensagens manualmente só representaria um número fixo de itens — e esse número seria falso.
- **Expressa a regra, não um exemplo.** `loop [para cada produto]` diz que o comportamento vale para *todos* os itens, com a condição explícita.
- **Legibilidade e manutenção.** O diagrama fica curto; se o passo do item mudar (ex.: incluir verificação de preço promocional), muda-se em um lugar só.
- **Evita inconsistência.** Cópias manuais podem ficar diferentes entre si por descuido.
- **Aproxima-se da implementação.** O `loop` corresponde diretamente a um `for` no `PedidoService`.

# Parte 9 — Encontrando problemas no projeto

## Exercício 21 — Quem deveria fazer isso?

![ex21_projeto_ruim](diagramas/ex21_projeto_ruim.png)

**1. Qual é o problema?** A **TelaPedido concentra todas as responsabilidades**: interação com o usuário, regras de negócio, cálculo, persistência e integração com e-mail. É uma “classe Deus” na camada de interface. Consequências: regras não podem ser reutilizadas por outro canal (app, API), testar exige a interface, qualquer mudança afeta a tela, há acoplamento direto com o banco e com o servidor de e-mail, e o controle de transação fica impossível.

**2. Responsabilidades concentradas indevidamente**

- `validarCliente()` — regra de domínio (ex.: cliente bloqueado ou inadimplente).
- `verificarEstoque()` — regra de domínio do produto.
- `calcularDesconto()` e `calcularTotal()` — regras de negócio do pedido.
- `salvarPedido()` — persistência.
- `enviarEmail()` — infraestrutura / integração externa.
- A **coordenação** de toda a sequência — papel da camada de aplicação.

**3. Distribuição entre as camadas**

| **Camada**             | **Participante**                    | **Responsabilidades**                                                                                                                      |
|------------------------|-------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Interface              | TelaPedido                          | Coletar itens, exibir resumo e resultado; repassar a ação ao controller.                                                                   |
| Controller / Aplicação | PedidoController                    | Receber a requisição e chamar o serviço.                                                                                                   |
|                        | PedidoService                       | Orquestrar: buscar cliente, criar o pedido, percorrer os itens, aplicar desconto, mandar salvar, disparar o e-mail; controlar a transação. |
| Domínio                | Cliente                             | `podeComprar()` — validação do cliente.                                                                                                    |
|                        | Produto                             | `verificarEstoque(qtd)`.                                                                                                                   |
|                        | Pedido                              | `adicionarItem()`, `aplicarDesconto()`, `calcularTotal()`.                                                                                 |
| Infraestrutura         | ClienteRepository, PedidoRepository | Buscar e gravar dados.                                                                                                                     |
|                        | ServicoEmail                        | Enviar a confirmação (de forma assíncrona, sem bloquear o pedido).                                                                         |

**Solução proposta**

![ex21_projeto_corrigido](diagramas/ex21_projeto_corrigido.png)

## Exercício 22 — Mensagens sem origem

`PedidoService → ClienteService: bloquearCliente()` aparece sem nenhuma RN, RF ou passo de UC que fale em bloqueio. As quatro hipóteses:

1.  **O diagrama está errado** — o projetista inventou um comportamento (ex.: “achou que fazia sentido” bloquear clientes com muitos cancelamentos). Ele altera o estado do negócio sem autorização da análise.
2.  **O requisito está incompleto** — o negócio de fato bloqueia clientes, mas ninguém escreveu o RF (“o sistema deve bloquear o cliente quando…”).
3.  **Uma Regra de Negócio não foi documentada** — existe uma política real (ex.: “cliente com 3 pagamentos estornados é bloqueado”) que só estava na cabeça de alguém.
4.  **Decisão interna legítima de projeto** — o “bloqueio” é técnico e transitório, sem efeito de negócio (ex.: travar o registro do cliente durante a transação, para evitar concorrência).

**Como decidir**

- **Perguntar se o comportamento muda algo que o usuário/negócio percebe.** Um cliente bloqueado não consegue comprar: isso é visível e tem consequências comerciais. Então **não** pode ser apenas decisão interna — precisa de RN/RF (hipóteses 2 ou 3) ou deve ser removido (hipótese 1).
- **Validar com o cliente/stakeholder.** Se ele confirmar a política → documentar a **RN**, derivar o **RF**, incluir o passo/exceção no **UC** e só então manter a mensagem. Se negar → **remover** a mensagem.
- **Se for técnico (hipótese 4)**, o nome deve deixar isso claro (`bloquearRegistroParaAtualizacao()` / `lock`), e o ideal é registrar a decisão em nota ou documento de arquitetura — ninguém deve confundi-la com regra de negócio.
- **Usar a rastreabilidade como critério:** toda mensagem com efeito de negócio precisa de origem em RN → RF → UC. Foi exatamente o que a equipe do VanTrack fez com o “Desfazer embarque em até 10 segundos” do UC-R02: sem origem, foi removido.

# Parte 10 — Exercício Integrador

## Exercício 23 — Do problema ao Diagrama de Sequência (Clínica)

**1. Regras de Negócio**

- **RN01** — Uma consulta só pode ser agendada em horário disponível da agenda do profissional.
- **RN02** — Um profissional não pode ter duas consultas no mesmo horário.
- **RN03** — O paciente pode cancelar a consulta até 24 horas antes do horário agendado.
- **RN04** — O profissional escolhido deve atender a especialidade selecionada.
- **RN05** — Todas as consultas e cancelamentos devem ser registrados (com data/hora da operação).

**2. Requisitos Funcionais**

- **RF01** — O sistema deve listar as especialidades disponíveis.
- **RF02** — O sistema deve listar os profissionais da especialidade escolhida.
- **RF03** — O sistema deve exibir os horários disponíveis do profissional numa data.
- **RF04** — O sistema deve permitir agendar uma consulta, garantindo RN01 e RN02.
- **RF05** — O sistema deve permitir que o paciente cancele uma consulta, respeitando o prazo de 24 h.
- **RF06** — O sistema deve manter o histórico de consultas e cancelamentos.
- **RF07** — O sistema deve permitir manter a agenda (horários de atendimento) dos profissionais.

**3. Requisitos Não Funcionais**

- **RNF01 (Desempenho)** — A lista de horários disponíveis deve ser exibida em até 2 s.
- **RNF02 (Confiabilidade/Integridade)** — Em agendamentos simultâneos para o mesmo horário, apenas um deve ser aceito.
- **RNF03 (Segurança/Privacidade)** — Dados de pacientes devem ser protegidos conforme a LGPD; apenas o próprio paciente e a clínica acessam suas consultas.
- **RNF04 (Usabilidade)** — O agendamento deve ser concluído em até 5 telas/etapas.
- **RNF05 (Compatibilidade)** — Disponível na web e em dispositivos móveis.

**4. Atores**

- **Paciente** (principal) — agenda e cancela consultas.
- **Atendente/Recepção** — pode agendar em nome do paciente e manter agendas.
- **Profissional de saúde** — consulta sua agenda.
- **Serviço de Notificação** (secundário, externo) — envia confirmações e lembretes.

**5. Casos de Uso**

- **UC01** — Consultar Horários Disponíveis
- **UC02** — Manter Agenda do Profissional
- **UC03** — **Agendar Consulta**
- **UC04** — Cancelar Consulta
- **UC05** — Consultar Histórico de Consultas

**6–7. Especificação de UC03 — Agendar Consulta (versão inicial)**

> **Objetivo:** Permitir que o paciente reserve uma consulta com um profissional, em um horário disponível. **Ator principal:** Paciente · **Atores secundários:** Serviço de Notificação **Pré-condições:** Paciente autenticado. **Pós-condições:** Consulta registrada e horário ocupado na agenda.
>
> **Fluxo principal** 1. Paciente solicita agendar uma consulta. 2. Sistema exibe as especialidades. 3. Paciente escolhe a especialidade. 4. Sistema exibe os profissionais da especialidade. 5. Paciente escolhe o profissional e a data. 6. Sistema exibe os horários disponíveis. 7. Paciente escolhe o horário e confirma. 8. Sistema registra a consulta. 9. Sistema exibe a confirmação.
>
> **Fluxos alternativos** - **A1 (Trocar profissional/data)** — No passo 6, o paciente volta e escolhe outro profissional ou outra data → retorna ao passo 5. - **A2 (Qualquer profissional)** — No passo 5, o paciente escolhe “primeiro horário disponível” → o sistema lista horários de todos os profissionais da especialidade.
>
> **Exceções** - **E1 (Sem horários disponíveis)** — No passo 6: sistema informa e sugere outra data; retorna ao passo 5.

**8. Relação RN ↔ RF/RNF ↔ UC**

| **RN**                                     | **RF / RNF**      | **UC**           |
|--------------------------------------------|-------------------|------------------|
| RN01 — Só horário disponível               | RF03, RF04, RNF02 | UC01, UC03       |
| RN02 — Sem conflito do profissional        | RF04, RNF02       | UC03             |
| RN03 — Cancelar até 24 h antes             | RF05              | UC04             |
| RN04 — Profissional da especialidade       | RF02              | UC03             |
| RN05 — Registrar consultas e cancelamentos | RF06, RNF03       | UC03, UC04, UC05 |
| —                                          | RF07              | UC02             |

**9. Checklist aplicado à versão inicial**

| **Problema**                                  | **Por que é um problema?**                                                       | **Correção**                                                    |
|-----------------------------------------------|----------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Sem **gatilho**                               | Não se sabe o que inicia o UC.                                                   | Gatilho: paciente escolhe “Agendar consulta”.                   |
| Sem **regras relacionadas**                   | RN01, RN02 e RN04 não aparecem no fluxo.                                         | Citar as regras nos passos 4, 6 e 8.                            |
| Sem **requisitos relacionados**               | Quebra a rastreabilidade.                                                        | RF01–RF04, RF06; RNF01, RNF02.                                  |
| Passo 8 não revalida a disponibilidade        | Dois pacientes podem escolher o mesmo horário ao mesmo tempo → violação da RN02. | Revalidar no passo 8 e criar exceção E2 “horário ocupado”.      |
| Paciente com consulta no mesmo horário        | Não foi pensado.                                                                 | Exceção E3.                                                     |
| Pós-condição não fala do registro/notificação | RN05 exige registro; o paciente espera confirmação.                              | Pós-condição: registro com data/hora + confirmação enviada.     |
| Falha do serviço de notificação não tratada   | Não pode desfazer o agendamento.                                                 | Alternativo A3: confirmação fica só na tela, reenvio posterior. |

**10. UC03 — Agendar Consulta (corrigido)**

> **Objetivo:** Permitir que o paciente reserve uma consulta com um profissional, em um horário disponível. **Ator principal:** Paciente · **Atores secundários:** Serviço de Notificação **Gatilho:** Paciente escolhe “Agendar consulta”. **Pré-condições:** Paciente autenticado; profissionais com agenda cadastrada. **Pós-condições:** Consulta registrada com data/hora da operação (RN05); horário do profissional ocupado; confirmação enviada ao paciente.
>
> **Fluxo principal** 1. Paciente solicita agendar uma consulta. 2. Sistema exibe as especialidades. 3. Paciente escolhe a especialidade. 4. Sistema exibe os profissionais que atendem a especialidade (RN04). 5. Paciente escolhe o profissional e a data. 6. Sistema exibe somente os horários livres do profissional (RN01, RN02). 7. Paciente escolhe o horário e confirma. 8. Sistema revalida se o horário continua disponível (RN01, RN02). 9. Sistema registra a consulta com data/hora da operação (RN05). 10. Sistema envia a confirmação ao paciente e exibe o comprovante.
>
> **Fluxos alternativos** - **A1 (Trocar profissional/data)** — No passo 6 → retorna ao passo 5. - **A2 (Primeiro horário disponível)** — No passo 5, paciente não escolhe profissional → sistema lista horários de todos os profissionais da especialidade. - **A3 (Notificação indisponível)** — No passo 10, o envio falha → sistema exibe o comprovante na tela e agenda reenvio; a consulta permanece registrada.
>
> **Exceções** - **E1 (Sem horários)** — No passo 6: sistema informa e sugere outra data; retorna ao passo 5. - **E2 (Horário ocupado na confirmação)** — No passo 8: sistema informa que o horário foi ocupado e exibe os horários atualizados; retorna ao passo 7. - **E3 (Paciente já tem consulta no horário)** — No passo 8: sistema informa o conflito; retorna ao passo 7.
>
> **Regras:** RN01, RN02, RN04, RN05 · **Requisitos:** RF01–RF04, RF06, RNF01, RNF02

**11. Participantes do fluxo principal**

Paciente · TelaAgendamento · AgendamentoController · AgendamentoService · Profissional (entidade) · Consulta (entidade) · ProfissionalRepository · ConsultaRepository · ServicoNotificacao (externo).

**12–13. Diagrama de Sequência com** `alt` **\[horário disponível\] / \[horário indisponível\]**

![ex23_agendar_consulta](diagramas/ex23_agendar_consulta.png)

**14. Responsabilidade de cada participante**

| **Participante**       | **Responsabilidade**                                                                                                     | **Origem na análise**                       |
|------------------------|--------------------------------------------------------------------------------------------------------------------------|---------------------------------------------|
| Paciente               | Escolhe especialidade, profissional, data e horário; confirma.                                                           | Ator principal do UC03.                     |
| TelaAgendamento        | Exibir listas e resultado; coletar escolhas. Sem regras.                                                                 | Passos de interação 2–7, 10.                |
| AgendamentoController  | Receber requisições da tela e encaminhar ao serviço.                                                                     | Separação interface × aplicação.            |
| AgendamentoService     | Coordenar o fluxo: listar dados, calcular horários livres, revalidar, criar e registrar a consulta, pedir a notificação. | Passos 4, 6, 8, 9 e 10.                     |
| Profissional           | `horariosLivres(data, consultas)` — conhece sua agenda e decide o que está livre.                                        | RN01, RN02, RN04.                           |
| Consulta               | Representar o agendamento (paciente, profissional, data/hora, situação).                                                 | RN05; objeto criado no passo 9.             |
| ProfissionalRepository | Buscar especialidades e profissionais.                                                                                   | RF01, RF02.                                 |
| ConsultaRepository     | Buscar consultas existentes, verificar conflito e gravar a nova consulta.                                                | RF04, RF06; revalidação do passo 8 (RNF02). |
| ServicoNotificacao     | Enviar a confirmação ao paciente (assíncrono).                                                                           | Passo 10; ator secundário; A3.              |

# Parte 11 — Desafio com o projeto da equipe (VanTrack)

## Exercício 24 — Da análise ao projeto

**Caso de uso escolhido:** **UC-S04 — Cancelar Utilização do Transporte**. É importante porque representa a “roteirização dinâmica”, diferencial do VanTrack: um cancelamento avisa o motorista e muda a rota em tempo real.

### Regras de Negócio

- **RN-006** — O aluno ou responsável pode cancelar a utilização do transporte a qualquer momento **antes do registro de embarque**; após o cancelamento, o aluno é considerado ausente.
- **RN-007** — Quando houver cancelamento, o **motorista responsável pela rota deve ser notificado**.
- **RN-008** — Após o cancelamento, o ponto do aluno deve ser **removido e a rota recalculada** com os alunos restantes (se não restar ninguém, a rota fica sem paradas de alunos).
- **RN-002** — O responsável só acessa informações dos alunos sob sua responsabilidade.
- **RN-012** — A van tem um único motorista ativo (é ele quem recebe o aviso).

### Requisitos

- **RF-005** — Cancelar utilização do transporte (só sem embarque registrado; marcar ausente com data/hora).
- **RF-006** — Notificar o motorista sobre cancelamento.
- **RF-008** — Recalcular rota após cancelamento, a partir da localização atual da van.
- **RNF-002** — Proteção de dados: validar vínculo antes de liberar operações sobre o aluno.
- **RNF-003** — Tratamento da perda de conectividade.

### Caso de Uso e Fluxo Principal (versão do repositório)

1.  Usuário seleciona o aluno e o trajeto com presença confirmada.
2.  Sistema verifica se o embarque ainda não foi registrado.
3.  Sistema pede a confirmação do cancelamento.
4.  Usuário confirma.
5.  Sistema altera o status do aluno para ausente e registra data e hora.
6.  Sistema executa UC-I01 — Notificar Motorista sobre Cancelamento («include»).
7.  Sistema exibe “Presença cancelada”.

Ponto de extensão: após o passo 5, se a rota do dia já foi gerada → UC-X02 — Recalcular Rota («extend»).

### Alternativo e Exceção

- **Exceção existente — E1 (Embarque já registrado):** no passo 2, o sistema não permite cancelar (RN-006).
- **Alternativos identificados na revisão:** A1 — usuário desiste na confirmação; A2 — cancelar ida e volta juntos.
- **Exceções identificadas na revisão:** E2 — embarque registrado enquanto o usuário confirmava; E3 — usuário sem vínculo com o aluno (RN-002); E4 — sem conectividade (RNF-003).

### Checklist / Revisão

A revisão completa está no Exercício 12 (checklist) e no Exercício 13 (teste das perguntas) da Atividade 03. Principais problemas:

| **Problema**                                                                 | **Correção**                                 |
|------------------------------------------------------------------------------|----------------------------------------------|
| “Embarque não registrado” era pré-condição e exceção ao mesmo tempo          | Ficou apenas como verificação (passo 3) + E1 |
| RN-006 checada só antes da confirmação (condição de corrida com o motorista) | Revalidação no momento da gravação + E2      |
| Sem fluxo de desistência                                                     | A1                                           |
| RN-002 não aplicada                                                          | Passo 2 + E3                                 |
| Sem conectividade não tratada                                                | E4                                           |
| Serviço de Notificação ausente como ator secundário; RF-006/RF-008 ausentes  | Incluídos                                    |

### Caso de Uso corrigido

> **UC-S04 — Cancelar Utilização do Transporte** **Ator principal:** Responsável ou Aluno · **Atores secundários:** Motorista, Serviço de Notificação **Pré-condições:** Usuário autenticado e vinculado ao aluno; presença confirmada para o trajeto. **Pós-condições:** Presença “ausente” com data/hora; motorista notificado; rota recalculada, se já gerada.

1.  Usuário seleciona o aluno e o trajeto com presença confirmada.
2.  Sistema verifica se o usuário é responsável pelo aluno (RN-002).
3.  Sistema verifica se o embarque ainda não foi registrado (RN-006).
4.  Sistema pede a confirmação do cancelamento.
5.  Usuário confirma.
6.  Sistema revalida que não há embarque, altera o status para “ausente” e registra data e hora (RN-006).
7.  Sistema executa UC-I01 — Notificar Motorista sobre Cancelamento («include», RN-007).
8.  Sistema exibe “Presença cancelada”.

> **Extensão:** após o passo 6, se a rota do dia já foi gerada → UC-X02 — Recalcular Rota (RN-008). **A1** — Usuário desiste no passo 5 → presença mantida. **A2** — Ida e volta: passos 3–7 para cada trajeto. **E1** — Embarque já registrado (passo 3). **E2** — Embarque registrado durante a confirmação (passo 6). **E3** — Sem vínculo (passo 2). **E4** — Sem conectividade (passo 6).

### Diagrama de Sequência

![ex24_vantrack_uc_s04](diagramas/ex24_vantrack_uc_s04.png)

### Responsabilidades e justificativa de cada elemento do diagrama

| **Elemento do diagrama**                                          | **Responsabilidade**                                                                                                         | **Justificativa na análise**                                                              |
|-------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|
| Ator **Responsável / Aluno**                                      | Inicia o cancelamento e confirma ou desiste.                                                                                 | Ator principal do UC-S04; gatilho “Cancelar presença”.                                    |
| **TelaPresenca** (boundary)                                       | Exibe a pergunta de confirmação e o resultado (“Presença cancelada” ou motivo da negação). Não decide nada.                  | Passos 1, 4, 5 e 8; mesma tela do UC-R01 (“Não vou mais”).                                |
| **PresencaController** (control)                                  | Recebe as duas requisições (verificar e confirmar) e repassa ao serviço, incluindo o `usuarioId` para a checagem de vínculo. | Separação interface × aplicação definida no documento de sequência do projeto.            |
| **PresencaService**                                               | Coordena o cenário: busca a presença, pede ao domínio as verificações, grava, aciona a notificação e decide pela extensão.   | Ordem dos passos 2 a 7 do UC corrigido; um serviço por grupo de UCs (decisão do projeto). |
| **Presenca** (entity) — `pertenceAoResponsavel()`                 | Verifica se a presença é de um aluno do usuário.                                                                             | RN-002, passo 2, exceção E3; RNF-002.                                                     |
| **Presenca** — `podeCancelar()`                                   | Verifica se já existe embarque.                                                                                              | RN-006, passo 3, exceção E1.                                                              |
| **Presenca** — `cancelar(dataHora)`                               | Revalida a RN-006 e muda o estado para “ausente” com data/hora.                                                              | RN-006 e RF-005; passo 6; exceção E2 (descoberta na revisão).                             |
| Busca da presença **de novo** antes de cancelar (mensagens 15–16) | Garante que a decisão usa o estado atual, não o lido antes da confirmação.                                                   | Correção da condição de corrida (E2).                                                     |
| **PresencaRepository**                                            | Busca e grava a presença.                                                                                                    | Persistência fora do domínio.                                                             |
| **NotificacaoService** + `ref` UC-I01                             | Identifica o motorista ativo e envia a notificação.                                                                          | RN-007, RN-012, RF-006; «include» obrigatório.                                            |
| **RotaRepository** — `buscarRotaDoDia()`                          | Informa se a rota do dia já existe.                                                                                          | Condição da extensão: “se a rota já foi gerada” (UC-S09).                                 |
| `opt` + **RotaService** + `ref` UC-X02                            | Executa o recálculo apenas quando a condição é verdadeira.                                                                   | RN-008, RF-008; «extend» opcional e condicional.                                          |
| `alt` externo (embarque não registrado / já registrado)           | Separa o caminho de sucesso da exceção E1.                                                                                   | RN-006.                                                                                   |
| `alt` interno (confirma / desiste)                                | Representa a decisão do usuário na confirmação.                                                                              | Fluxo alternativo A1, criado na revisão.                                                  |

# Questão final para discussão

**O Diagrama de Sequência apenas representa graficamente o Caso de Uso ou também ajuda a descobrir problemas e decisões de projeto?**

Ele **também ajuda a descobrir problemas e decisões**. Para desenhar cada mensagem é preciso decidir *quem* a envia, *quem* a recebe, *com quais dados* e *em que momento* — perguntas que o texto do caso de uso permite deixar vagas. Exemplos encontrados nos exercícios:

1.  **Condição de corrida no cancelamento do VanTrack (Ex. 24).** No UC-S04 original, a verificação do embarque acontecia no passo 2 e a gravação no passo 5. Ao desenhar, ficou visível que o motorista (outro ator, em outro dispositivo) pode registrar o embarque entre a pergunta “Deseja cancelar?” e a confirmação. O diagrama obrigou a revalidar a RN-006 no `cancelar()` e gerou a nova exceção **E2**, que voltou para o caso de uso.

2.  **Revalidação da disponibilidade (Ex. 19 e 23).** No UC03 Reservar Sala e no UC03 Agendar Consulta, o texto dizia apenas “verifica disponibilidade” e “registra”. No diagrama, percebeu-se que há um intervalo entre mostrar o horário e confirmá-lo, em que outro usuário pode ocupá-lo. Daí surgiram a mensagem `existeConflito` antes do `salvar`, a exceção “horário ocupado na confirmação” e um RNF de integridade em acessos simultâneos (RNF02).

3.  **Distribuição de responsabilidades (Ex. 21).** O caso de uso não diz onde ficam as regras. Só ao desenhar a `TelaPedido` com todas as mensagens ficou claro que ela concentrava domínio, persistência e e-mail — uma decisão de projeto ruim, que o diagrama tornou visível e permitiu corrigir.

4.  **Comportamentos sem origem (Ex. 22 e VanTrack).** A mensagem `bloquearCliente()` revela uma regra possivelmente não documentada. No VanTrack, o mesmo raciocínio levou a equipe a remover o “Desfazer embarque em até 10 segundos” do UC-R02, que não tinha RN nem RF.

5.  **Atores e requisitos esquecidos (VanTrack).** Ao colocar o `NotificacaoService` e o Serviço de Notificação no diagrama do UC-S04, notou-se que eles não constavam como ator secundário do caso de uso, e que RF-006 e RF-008 não estavam listados entre os requisitos relacionados.

Conclusão: o diagrama de sequência é uma **ferramenta de verificação** da análise. Ele traduz o caso de uso, mas, ao fazer isso, expõe lacunas, ambiguidades e decisões ocultas — que devem **voltar** para as regras, requisitos e casos de uso, mantendo a rastreabilidade nos dois sentidos.
