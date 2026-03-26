# Avaliação — Engenharia de Software
**Sistema Integrado de Gestão de Farmácia — MVP Definido pelo Estudante**

Aluno: João Vitor Franco Moraes  
RA: 25001068  
Data: 25/03/2026  
---

# 1. Definição do MVP

Meu MVP inclui o processo de venda da identificação e cadastro de um cliente até a emissão do comprovante da venda, também inclui a verificação do estoque e tratamente de exceções como estoque insuficiente e cliente não cadastrado.

**Dentro do MVP:**
- Cadastro e identificação de clientes
- Pesquisa de produtos
- Verificação de estoque
- Registro de venda à vista e a prazo
- Emissão de comprovante

**Fora do MVP:**
- Módulo de compras e fornecedores
- Contas a pagar
- Relatórios gerenciais
- Transferência de estoque entre unidades

**Justificativa:** O processo de venda é o principal elemento da operação da farmácia. Se esse processo for modelado, os principais problemas apontados serão resolvidos, também conectando clientes, produtos, estoque e financeiro.

---

# 2. Regras de Negócio

**RN01 —** Um produto só pode ser vendido se houver quantidade disponível no estoque da unidade.

**RN02 —** Toda venda concluída deve reduzir automaticamente o estoque dos itens vendidos.

**RN03 —** Vendas com pagamento a prazo só podem ser realizadas para clientes cadastrados no sistema.

**RN04 —** Toda venda a prazo finalizada deve gerar automaticamente um lançamento em contas a receber com status "Aberta".

**RN05 —** Toda venda finalizada deve gerar um comprovante contendo itens, valores, forma de pagamento, data/hora e identificação da unidade.

---

# 3. Requisitos Funcionais

**RF01 —** O sistema deve permitir pesquisar produtos por nome, código de barras ou fabricante.

**RF02 —** O sistema deve permitir cadastrar um novo cliente durante o atendimento.

**RF03 —** O sistema deve permitir identificar um cliente já cadastrado por CPF ou nome.

**RF04 —** O sistema deve permitir adicionar produtos à venda e validar o estoque a cada item.

**RF05 —** O sistema deve permitir selecionar a forma de pagamento: à vista ou a prazo.

**RF06 —** O sistema deve registrar a venda e atualizar o estoque ao finalizar.

**RF07 —** O sistema deve gerar e exibir o comprovante quando cada venda for finalizada.

**RF08 —** O sistema deve permitir o cancelamento de uma venda em andamento sem alterar estoque ou financeiro.

---

# 🛡 4. Requisitos Não Funcionais

**RNF01 —** O sistema deve responder a qualquer operação em no máximo 2 segundos.

**RNF02 —** O acesso às funcionalidades deve ser restrito por perfil de usuário (Atendente, Gerente, Financeiro, Administrador).

**RNF03 —** O sistema deve garantir que venda, estoque e financeiro são atualizados juntos ou nenhum é alterado.

**RNF04 —** O sistema deve registrar log de todas as operações críticas (vendas, cancelamentos, alterações de estoque) com usuário, data e hora.

---

# 5. Casos de Uso

## Diagrama de Casos de Uso

<img width="786" height="375" alt="image" src="https://github.com/user-attachments/assets/c66fe135-63e9-49f1-a83f-948a7f9ccb55" />


---

# 6. Documentação dos Casos de Uso

---

## **UC01 — Realizar Venda**
**Ator(es):** Atendente  
**Descrição:** Registra a venda de produtos ao cliente, da escolha do produto até a emissão do comprovante.  
**Pré-condições:** Atendente autenticado e ao menos um produto com estoque disponível.  
**Pós-condições:** Venda registrada, estoque atualizado e comprovante emitido.

### Fluxo Principal
1. Atendente inicia nova venda
2. Sistema executa UC02 — Identificar Cliente
3. Atendente executa UC04 — Pesquisa o produto e informa a quantidade
4. Atendente repete o passo 3 para cada item escolhido
5. Atendente seleciona forma de pagamento e finaliza a venda
6. Sistema atualiza o estoque
7. Sistema executa UC07 — Emitir Comprovante

### Fluxos Alternativos / Exceções
- **FA01 —** Sem estoque: sistema bloqueia adição do item e informa saldo atual do estoque.
- **FA02 —** Pagamento a prazo: sistema estende para UC06 — Registrar Venda a Prazo.
- **FA03 —** Cancelamento: atendente aciona UC08 antes da finalização.

### Relacionamentos
- **Include:** UC02 — Identificar Cliente; UC04 — Pesquisar Produto; UC07 — Emitir Comprovante
- **Extend:** UC06 — Registrar Venda a Prazo; UC08 — Cancelar Venda

### Diagrama de Atividades — UC01

<img width="436" height="1025" alt="image" src="https://github.com/user-attachments/assets/6830a05f-47ba-463e-8f6b-e509e4cf2004" />


---

## **UC02 — Identificar Cliente**
**Ator(es):** Atendente  
**Descrição:** Localiza um cliente cadastrado para vinculação à venda pelo CPF ou nome.  
**Pré-condições:** Venda iniciada.  
**Pós-condições:** Cliente vinculado à venda ou venda prosseguindo sem identificação (somente à vista).

### Fluxo Principal
1. Sistema solicita identificação do cliente
2. Atendente informa CPF ou nome
3. Sistema localiza e exibe os dados do cliente
4. Atendente confirma
5. Cliente é vinculado à venda

### Fluxos Alternativos / Exceções
- **FA01 —** Cliente não encontrado: sistema informa e estende para UC03 se atendente desejar cadastrá-lo.
- **FA02 —** Sem identificação: atendente pode prosseguir sem identificar (somente vendas à vista).

### Relacionamentos
- **Include:** Nenhum
- **Extend:** UC03 — Cadastrar Cliente (quando cliente não encontrado)

### Diagrama de Atividades — UC02

<img width="786" height="564" alt="image" src="https://github.com/user-attachments/assets/846c9093-2919-40a2-a3e7-aa4698020d48" />


---

## **UC03 — Cadastrar Cliente**
**Ator(es):** Atendente  
**Descrição:** Registra um novo cliente no sistema durante o atendimento.  
**Pré-condições:** Cliente buscado em UC02 e não encontrado.  
**Pós-condições:** Novo cliente salvo e vinculado à venda.

### Fluxo Principal
1. Sistema exibe formulário de cadastro
2. Atendente preenche nome, CPF e telefone
3. Atendente confirma
4. Sistema valida o CPF
5. Sistema salva o cliente e o vincula à venda

### Fluxos Alternativos / Exceções
- **FA01 —** CPF inválido: sistema exibe erro e solicita correção.
- **FA02 —** CPF já cadastrado: sistema exibe cadastro existente e redireciona para UC02.

### Relacionamentos
- **Include:** Nenhum
- **Extend:** UC02 — Identificar Cliente (este UC estende UC02)

### Diagrama de Atividades — UC03

<img width="513" height="501" alt="image" src="https://github.com/user-attachments/assets/16ecbb46-6671-4c62-b4b4-034a34cdd977" />


---

## **UC04 — Pesquisar Produto**
**Ator(es):** Atendente  
**Descrição:** Localiza produtos ativos para adição à venda.  
**Pré-condições:** Venda iniciada.  
**Pós-condições:** Produto selecionado retornado ao fluxo de venda.

### Fluxo Principal
1. Atendente digita nome, código de barras ou fabricante
2. Sistema filtra e lista produtos ativos com preço e saldo
3. Atendente seleciona o produto desejado
4. Produto é retornado ao fluxo de UC01

### Fluxos Alternativos / Exceções
- **FA01 —** Nenhum resultado: sistema exibe "Produto não encontrado" e permite nova busca.
- **FA02 —** Produto sem estoque: aparece na lista com indicador "sem estoque" e não pode ser adicionado.

### Relacionamentos
- **Include:** UC05 — Verificar Estoque (executado ao exibir cada produto)
- **Extend:** Nenhum

### Diagrama de Atividades — UC04

<img width="695" height="556" alt="image" src="https://github.com/user-attachments/assets/39aef3bf-55b8-4b95-b8da-b895f93bc87d" />


---

## **UC05 — Verificar Estoque**
**Ator(es):** Sistema  
**Descrição:** Verifica se a quantidade solicitada está disponível no estoque da unidade.  
**Pré-condições:** Produto selecionado e quantidade informada.  
**Pós-condições:** Disponibilidade confirmada ou adição bloqueada.

### Fluxo Principal
1. Sistema recebe produto e quantidade desejada
2. Sistema consulta saldo no estoque da unidade
3. Se suficiente, confirma disponibilidade
4. Se insuficiente, bloqueia e informa o saldo atual

### Fluxos Alternativos / Exceções
- **FA01 —** Estoque insuficiente: sistema exibe alerta com saldo disponível e bloqueia a adição.

### Relacionamentos
- **Include:** Nenhum
- **Extend:** Nenhum

### Diagrama de Atividades — UC05

<img width="376" height="367" alt="image" src="https://github.com/user-attachments/assets/cb66113e-9704-4d9a-891c-990324acbcc5" />


---

## **UC06 — Registrar Venda a Prazo**
**Ator(es):** Atendente  
**Descrição:** Estende a venda para registrar lançamento em contas a receber quando o pagamento é a prazo.  
**Pré-condições:** Venda com cliente identificado; pagamento selecionado como "a prazo".  
**Pós-condições:** Lançamento criado em contas a receber com status "Aberta".

### Fluxo Principal
1. Atendente seleciona pagamento a prazo
2. Sistema verifica se há cliente identificado
3. Atendente informa a data de vencimento
4. Sistema cria lançamento em contas a receber

### Fluxos Alternativos / Exceções
- **FA01 —** Sem cliente: sistema bloqueia e redireciona para UC02.
- **FA02 —** Data inválida: sistema exibe erro e solicita nova data.

### Relacionamentos
- **Include:** Nenhum
- **Extend:** UC01 — Realizar Venda (este UC estende UC01)

### Diagrama de Atividades — UC06

<img width="764" height="431" alt="image" src="https://github.com/user-attachments/assets/6e2b6291-d895-4ee6-95ac-6a4339cb4a9c" />


---

## **UC07 — Emitir Comprovante**
**Ator(es):** Sistema  
**Descrição:** Gera e exibe o comprovante ao término de uma venda finalizada.  
**Pré-condições:** Venda concluída e registrada.  
**Pós-condições:** Comprovante exibido e disponível para impressão.

### Fluxo Principal
1. Sistema coleta dados da venda: itens, valores, pagamento, cliente, data/hora e unidade
2. Sistema gera o comprovante formatado
3. Comprovante é exibido na tela
4. Atendente pode imprimir ou encerrar

### Fluxos Alternativos / Exceções
- **FA01 —** Falha na geração: sistema registra log e permite nova tentativa; venda permanece registrada.

### Relacionamentos
- **Include:** UC01 — Realizar Venda (este UC é incluído por UC01)
- **Extend:** Nenhum

### Diagrama de Atividades — UC07

<img width="478" height="464" alt="image" src="https://github.com/user-attachments/assets/1ea261cf-f10b-4838-8877-4851ac3ba006" />


---

## **UC08 — Cancelar Venda**
**Ator(es):** Atendente  
**Descrição:** Cancela uma venda em andamento sem registrar alterações em estoque ou financeiro.  
**Pré-condições:** Venda iniciada e não finalizada.  
**Pós-condições:** Venda descartada; estoque e financeiro inalterados.

### Fluxo Principal
1. Atendente aciona "Cancelar Venda"
2. Sistema solicita confirmação
3. Atendente confirma
4. Sistema descarta os itens da venda
5. Sistema retorna à tela inicial

### Fluxos Alternativos / Exceções
- **FA01 —** Atendente não confirma: sistema retorna à venda em andamento.

### Relacionamentos
- **Include:** Nenhum
- **Extend:** UC01 — Realizar Venda (este UC estende UC01)

### Diagrama de Atividades — UC08

<img width="631" height="367" alt="image" src="https://github.com/user-attachments/assets/842d468b-77b7-454b-91fb-f003ab2bbcb5" />


---

## **UC09 — Registrar Devolução**
**Ator(es):** Atendente  
**Descrição:** Registra a devolução de itens de uma venda anterior, revertendo estoque e financeiro quando aplicável.  
**Pré-condições:** Atendente autenticado; venda original localizada.  
**Pós-condições:** Devolução registrada e estoque revertido.

### Fluxo Principal
1. Atendente localiza a venda original pelo número ou CPF
2. Sistema exibe os itens da venda
3. Atendente seleciona itens e quantidades a devolver
4. Atendente confirma a devolução
5. Sistema reverte o estoque e registra a devolução

### Fluxos Alternativos / Exceções
- **FA01 —** Venda não encontrada: sistema exibe mensagem de erro.
- **FA02 —** Venda era a prazo: sistema ajusta o lançamento em contas a receber.

### Relacionamentos
- **Include:** Nenhum
- **Extend:** Nenhum

### Diagrama de Atividades — UC09

<img width="469" height="625" alt="image" src="https://github.com/user-attachments/assets/9908f4b0-cdc1-4666-b92e-25e02d5542ad" />


---

## **UC10 — Gerenciar Produto**
**Ator(es):** Gerente  
**Descrição:** Permite cadastrar, editar e ativar/desativar produtos no catálogo.  
**Pré-condições:** Usuário autenticado com perfil Gerente.  
**Pós-condições:** Produto cadastrado, atualizado ou com status alterado.

### Fluxo Principal
1. Gerente acessa o módulo de produtos
2. Gerente escolhe: cadastrar, editar ou desativar
3. Preenche ou altera os dados necessários
4. Confirma a operação
5. Sistema valida e salva as alterações

### Fluxos Alternativos / Exceções
- **FA01 —** Código de barras duplicado: sistema exibe erro e bloqueia o salvamento.
- **FA02 —** Campos obrigatórios em branco: sistema destaca os campos e bloqueia.

### Relacionamentos
- **Include:** Nenhum
- **Extend:** Nenhum

### Diagrama de Atividades — UC10

<img width="587" height="559" alt="image" src="https://github.com/user-attachments/assets/9aaf324f-471f-48bf-9bd6-74e203a1a311" />


---

*Fim do documento.*
