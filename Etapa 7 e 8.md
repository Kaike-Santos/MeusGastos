# ARQUITETURA.md

1. Se amanhã o app precisar buscar os gastos de uma API em vez da memória, quais arquivos precisam mudar? E quais com certeza NÃO mudam?
R: O único arquivo que precisaria mudar seria o `GastoRepository.kt`, onde eu teria que criar uma nova implementação (tipo `GastoRepositoryAPI`) respeitando a interface[cite: 1]. Os arquivos da pasta `ui/` e o `GastosViewModel.kt` com certeza não mudam[cite: 1].

2. Por que o total é calculado e não guardado? Descreva um bug concreto que apareceria se ele fosse uma variável atualizada manualmente.
R: Ele é calculado para não dessincronizar[cite: 1]. Se ele fosse uma variável, um bug concreto seria: eu poderia esquecer de atualizar essa variável dentro da função `remover()`. Aí a pessoa apagaria um gasto, o item sumiria da tela, mas o total continuaria mostrando o valor antigo[cite: 1].

3. O FormularioGasto não recebe o ViewModel. O que isso te permitiu fazer que não seria possível se ele recebesse?
R: Me permitiu criar um `@Preview` para o FormularioGasto e ver como a tela de erro fica sem precisar rodar o aplicativo inteiro ou depender do ViewModel[cite: 1].

---

# ESTRAGOS.md

**Estrago 1**
*   **Previsão:** Ao usar `GastosViewModel()` em vez de `viewModel()`, a tela vai perder o estado toda vez que girar ou mudar algo[cite: 1].
*   **O que aconteceu:** Ao digitar na descrição, a tela se reconstruiu e a lista de gastos que eu tinha acabado de adicionar sumiu, porque um ViewModel totalmente novo foi criado[cite: 1].

**Estrago 2**
*   **Previsão:** Se eu usar uma lista comum em vez de `mutableStateListOf`, a tela não vai perceber quando a lista mudar[cite: 1].
*   **O que aconteceu:** Eu adicionei o gasto, mas ele não apareceu na lista. Quando digitei uma letra na descrição (o que causou uma recomposição por causa do estado do texto), aí sim a lista atualizou e o item apareceu do nada[cite: 1].

**Estrago 3**
*   **Previsão:** O total vai ficar errado se eu não lembrar de atualizar ele em todas as funções[cite: 1].
*   **O que aconteceu:** O total aumentou certo quando eu adicionei os três gastos, mas quando eu removi um, o total continuou com o mesmo valor (mostrando o gasto apagado)[cite: 1].

**Estrago 4**
*   **Previsão:** O código vai ficar mais acoplado e dar erro de tipagem na hora de tentar mudar pra API ou pro Demo[cite: 1].
*   **O que aconteceu:** Quando tentei trocar o `= GastoRepositoryMemoria()` por `= GastoRepositoryDemo()`, o Kotlin acusou erro de tipo, porque as duas classes não tinham a interface `GastoRepository` como "pai" em comum garantindo as funções[cite: 1].

**Estrago 5**
*   **Previsão:** A regra vai ficar presa nessa tela e vou ter que duplicar o código em outras partes[cite: 1].
*   **O que aconteceu:** Funcionou pra essa tela, mas se eu tivesse que cadastrar gasto numa segunda tela do app, eu teria que copiar esse mesmo `if (vm.descricao.isNotBlank())` pra lá, espalhando as regras de negócio pela UI[cite: 1].