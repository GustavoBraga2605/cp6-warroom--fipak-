# FICHA DE DECISÕES · War Room FiapBank (CP6 · 3 aulas)

**Grupo (nome da equipe plantonista):** FIPAK

**Turma:** 2CCPO **Repo:** `https://github.com/GustavoBraga2605/cp6-warroom--fipak-.git`

**Integrantes (nome + RM):**

| Nome | RM |
|---|---|
| Antonio Lucas | 565516 |
| Bento Donato | 561621 |
| Enzo Ribeiro | 564216 |
| Guilherme Califoni | 565157 |
| Gustavo Braga | 562247 |
| Gustavo Schimith | 564800 |
| Kaio Correa | 563443 |
| Lucas Mendes | 563667 |




---

## 📁 Dossiê técnico do FiapBank (MVP em produção)

**Stack:** Java 17 + Spring Boot + Spring Data JPA + Oracle. API com endpoints em
`/api/contas` e `/api/transferencias` (cenário visto desde a Aula 13).

**Contrato e regras de negócio que o banco prometeu aos clientes e aos reguladores:**

| Regra | Como deve ser |
|---|---|
| Transferência **PIX** | taxa **R$ 0,00** |
| Transferência **TED** | taxa fixa **R$ 5,00** |
| Saldo | **nunca fica negativo**: transferência/saque sem saldo é recusado com erro claro |
| Número de conta | **sequencial e único** (1001, 1002, 1003...), gerado pelo sistema |
| Extrato de transferência | grava **quem pagou** e **quem recebeu**, na ordem certa |
| CPF | **dado sensível**: nunca aparece nas respostas da API |
| Consultas ao banco | sempre parametrizadas, e cada operação **usa e libera** a conexão |
| Suíte de testes | roda antes de todo deploy; **verde** é pré-requisito pra subir |

---

# 📝 REGISTRO DE DECISÕES

*(placar inicial: 🔥 7 · 💰 0 · 🧹 2)*

## 1. Saldo que virou negativo

**Tipo:** rodada · **Voto:** C

**Justificativa:**

O saldo ficou negativo porque a regra de negócio (saldo nunca negativo) não era validada antes do débito. Corrigir a validação **e** escrever o teste automatizado (teste de regressão) prende a regra na suíte, que precisa estar verde para qualquer deploy: se o bug voltar, o pipeline barra antes de ir para produção.

Trade-off: gasta mais tempo do que um hotfix direto (velocidade × segurança), mas evita reincidência e o custo financeiro de repetir o incidente.

**Placar do grupo após esta decisão:** 🔥 8 · 💰 R$ 35 mil · 🧹 1


## 2. SQL Injection funcionando na busca por titlar
 
**Tipo:** rodada · **Voto:** A
 
**Justificativa:**
 
A busca concatenava a entrada do usuário no SQL, então o texto digitado era interpretado como código. A consulta parametrizada (`PreparedStatement`) separa a estrutura do comando dos valores: a entrada vira apenas dado e nunca é executada, o que neutraliza a injeção na raiz, e não só em casos específicos.
 
Trade-off: exige revisar e refatorar todas as consultas do projeto (mais trabalho agora), em troca de proteger dados sensíveis como CPF e saldos, o que um filtro de caracteres não garante.
 
**Placar do grupo após esta decisão:** 🔥 8 · 💰 R$ 35 mil · 🧹 1
 
## 2. Relâmpago
 
**Tipo:** relâmpago · **Voto:** A
 
**Justificativa:**
 
O `try-with-resources` fecha automaticamente a conexão (`AutoCloseable`) ao fim do bloco, mesmo se uma exceção for lançada. Isso cumpre a regra de que cada operação usa e libera a conexão e evita vazamento de conexões, que esgota o pool e derruba a API na Black Friday.
 
Trade-off: pequena mudança de código, com baixo risco, comparada a fechar a conexão manualmente num `finally`, que é mais verboso e fácil de esquecer.
 
**Placar do grupo após esta decisão:** 🔥 8 · 💰 R$ 35 mil · 🧹 1
 
## 3. A noite das contas gêmeas
 
**Tipo:** rodada · **Voto:** B
 
**Justificativa:**
 
Duas contas receberam o mesmo número porque a geração dependia da aplicação, e dois pedidos simultâneos (condição de corrida) podiam ler o mesmo valor. Delegar a geração ao banco (sequence/identity) com restrição `UNIQUE` torna a atribuição atômica e garante número único e sequencial, como o contrato exige.
 
Trade-off: depende do banco e pode deixar lacunas na numeração se uma transação falhar, mas elimina a duplicidade, que é o risco regulatório.
 
**Placar do grupo após esta decisão:** 🔥 8 · 💰 R$ 35 mil · 🧹 2
 
---
 
## 🔎 O caminho do MEU grupo (preencher na 3ª aula, quando o mapa for revelado)
 
| # | Decisão | Nossa letra | Consequência que ELA teria tido |
|---|---|---|---|
| | | | |
| | | | |
| | | | |
 
---
 
# 📋 PÓS-MORTEM · relatório de incidente (montar em sala na 3ª aula)
 
## 1. Linha do tempo da madrugada
 
_______________________________________________________________________________________
 
## 2. Causa raiz de 2 incidentes (aula + mecanismo técnico)
 
**Incidente 1:** _______________________________________________
 
Aula/mecanismo:
 
_______________________________________________________________________________________
 
**Incidente 2:** _______________________________________________
 
Aula/mecanismo:
 
_______________________________________________________________________________________
 
## 3. O que faríamos diferente (2 rodadas + por quê)
 
_______________________________________________________________________________________
 
## 4. A maior lição da equipe
 
_______________________________________________________________________________________
 