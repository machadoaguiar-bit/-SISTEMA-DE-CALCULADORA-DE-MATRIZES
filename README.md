# Calculadora de Matrizes em C

Programa de terminal que cadastra duas ou mais matrizes e executa, em sequência (da esquerda para a direita), adição, subtração, multiplicação ou divisão matricial.

## Regras implementadas

- **Adição e subtração:** exigem matrizes com as mesmas dimensões.
- **Multiplicação:** o número de colunas da primeira matriz deve ser igual ao número de linhas da segunda, em cada etapa.
- **Divisão matricial:** `A / B = A × B⁻¹`. A matriz divisora precisa ser quadrada, inversível e com dimensões compatíveis.
- **Três ou mais matrizes:** operações executadas sequencialmente, da esquerda para a direita: `((M1 op M2) op M3) ...`
- **Matriz singular:** o sistema identifica e informa quando a divisora não possui inversa.
- Mensagens de erro indicam em qual etapa e por que a operação não pôde ser realizada.

## Estrutura do código

- Leitura: `lerInteiro`, `lerQuantidadeMatrizes`, `lerDimensoes`, `lerMatriz`, `cadastrarMatrizes`
- Exibição: `mostrarMatriz`, `mostrarResultado`, `mostrarMenu`, `mostrarErro`
- Validação: `validarSomaSubtracao`, `validarMultiplicacao`, `validarDivisao`
- Operações: `somar`, `subtrair`, `multiplicar`, `inversa` (Gauss-Jordan), `dividir`, `executarOperacao`
- `main()` apenas coordena o fluxo.

## Como compilar e executar

```bash
gcc calculadora_matrizes.c -o calculadora -lm
./calculadora
```

No Windows (MinGW):

```bash
gcc calculadora_matrizes.c -o calculadora.exe -lm
calculadora.exe
```

## Como usar

1. Informe a quantidade de matrizes (mínimo 2).
2. Para cada matriz, informe linhas, colunas e os elementos.
3. Escolha a operação no menu. O resultado (ou o motivo do erro) é exibido.
4. Repita quantas operações quiser ou digite `0` para sair.
