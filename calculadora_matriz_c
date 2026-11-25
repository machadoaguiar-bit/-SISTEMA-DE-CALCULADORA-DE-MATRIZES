#include <stdio.h>
#include <stdlib.h>
#include <math.h>

#define EPSILON 1e-10

typedef struct {
    int linhas;
    int colunas;
    double *dados;
} Matriz;

// Resultado das validacoes e operacoes
typedef enum {
    OK = 0,
    ERRO_DIMENSOES_IGUAIS,      // soma/subtracao
    ERRO_DIMENSOES_MULT,        // multiplicacao
    ERRO_DIVISOR_NAO_QUADRADA,  // divisao: divisora nao e quadrada
    ERRO_DIVISAO_INCOMPATIVEL,  // divisao: dimensoes incompativeis
    ERRO_DIVISOR_SINGULAR       // divisao: divisora sem inversa
} Status;

// Acessa um elemento da matriz
#define E(m, i, j) ((m).dados[(i) * (m).colunas + (j)])

/* ===================== MEMORIA ===================== */

Matriz criarMatriz(int linhas, int colunas) {
    Matriz m;

    m.linhas = linhas;
    m.colunas = colunas;
    m.dados = calloc((size_t)linhas * colunas, sizeof(double));

    if (m.dados == NULL) {
        printf("Erro ao alocar memoria!\n");
        exit(1);
    }

    return m;
}

void liberarMatriz(Matriz *m) {
    free(m->dados);
    m->dados = NULL;
}

Matriz copiarMatriz(Matriz m) {
    Matriz r = criarMatriz(m.linhas, m.colunas);

    for (int i = 0; i < m.linhas; i++) {
        for (int j = 0; j < m.colunas; j++) {
            E(r, i, j) = E(m, i, j);
        }
    }

    return r;
}

/* ===================== LEITURA ===================== */

int lerInteiro(const char *mensagem) {
    int valor;

    printf("%s", mensagem);
    if (scanf("%d", &valor) != 1) {
        printf("Entrada invalida!\n");
        exit(1);
    }

    return valor;
}

int lerQuantidadeMatrizes(void) {
    int quantidade;

    do {
        quantidade = lerInteiro("\nQuantas matrizes deseja cadastrar? ");

        if (quantidade < 2) {
            printf("Digite pelo menos duas matrizes!\n");
        }
    } while (quantidade < 2);

    return quantidade;
}

void lerDimensoes(int *linhas, int *colunas) {
    do {
        *linhas = lerInteiro("Quantidade de linhas: ");
        *colunas = lerInteiro("Quantidade de colunas: ");

        if (*linhas <= 0 || *colunas <= 0) {
            printf("As dimensoes devem ser positivas!\n");
        }
    } while (*linhas <= 0 || *colunas <= 0);
}

void lerMatriz(Matriz *m, int numero) {
    printf("\n--- MATRIZ %d ---\n", numero);

    for (int i = 0; i < m->linhas; i++) {
        for (int j = 0; j < m->colunas; j++) {
            printf("Digite o elemento [%d][%d]: ", i + 1, j + 1);

            if (scanf("%lf", &E((*m), i, j)) != 1) {
                printf("Valor invalido!\n");
                exit(1);
            }
        }
    }
}

void cadastrarMatrizes(Matriz *matrizes, int quantidade) {
    for (int i = 0; i < quantidade; i++) {
        int linhas, colunas;

        printf("\nCadastro da matriz %d\n", i + 1);
        lerDimensoes(&linhas, &colunas);

        matrizes[i] = criarMatriz(linhas, colunas);
        lerMatriz(&matrizes[i], i + 1);
    }
}

/* ===================== EXIBICAO ===================== */

void mostrarMatriz(Matriz m) {
    for (int i = 0; i < m.linhas; i++) {
        for (int j = 0; j < m.colunas; j++) {
            printf("%10.2lf ", E(m, i, j));
        }
        printf("\n");
    }
}

void mostrarResultado(Matriz m) {
    printf("\n====================================\n");
    printf("         MATRIZ RESULTANTE\n");
    printf("====================================\n");
    mostrarMatriz(m);
}

void mostrarMenu(void) {
    printf("\n====================================\n");
    printf("          MENU DE OPERACOES\n");
    printf("====================================\n");
    printf("1 - Adicao\n");
    printf("2 - Subtracao\n");
    printf("3 - Multiplicacao\n");
    printf("4 - Divisao matricial (A x B^-1)\n");
    printf("0 - Sair\n");
    printf("Escolha uma opcao: ");
}

// Informa ao usuario por que a operacao nao pode ser realizada
void mostrarErro(Status status, int etapa, Matriz a, Matriz b) {
    printf("\nErro na etapa %d: ", etapa);

    switch (status) {
        case ERRO_DIMENSOES_IGUAIS:
            printf("adicao e subtracao exigem matrizes com as mesmas dimensoes "
                   "(%dx%d e %dx%d).\n",
                   a.linhas, a.colunas, b.linhas, b.colunas);
            break;
        case ERRO_DIMENSOES_MULT:
            printf("o numero de colunas da primeira matriz (%d) deve ser igual "
                   "ao numero de linhas da segunda (%d).\n",
                   a.colunas, b.linhas);
            break;
        case ERRO_DIVISOR_NAO_QUADRADA:
            printf("a matriz divisora (%dx%d) nao e quadrada, "
                   "portanto nao possui inversa.\n",
                   b.linhas, b.colunas);
            break;
        case ERRO_DIVISAO_INCOMPATIVEL:
            printf("dimensoes incompativeis para divisao: colunas de A (%d) "
                   "diferente de linhas de B (%d).\n",
                   a.colunas, b.linhas);
            break;
        case ERRO_DIVISOR_SINGULAR:
            printf("a matriz divisora e singular (determinante nulo) "
                   "e nao possui inversa.\n");
            break;
        default:
            break;
    }
}

/* ===================== VALIDACAO ===================== */

Status validarSomaSubtracao(Matriz a, Matriz b) {
    if (a.linhas != b.linhas || a.colunas != b.colunas) {
        return ERRO_DIMENSOES_IGUAIS;
    }
    return OK;
}

Status validarMultiplicacao(Matriz a, Matriz b) {
    if (a.colunas != b.linhas) {
        return ERRO_DIMENSOES_MULT;
    }
    return OK;
}

// Valida quadrada e compatibilidade (singularidade e verificada na inversa)
Status validarDivisao(Matriz a, Matriz b) {
    if (b.linhas != b.colunas) {
        return ERRO_DIVISOR_NAO_QUADRADA;
    }
    if (a.colunas != b.linhas) {
        return ERRO_DIVISAO_INCOMPATIVEL;
    }
    return OK;
}

/* ===================== OPERACOES ===================== */

Matriz somar(Matriz a, Matriz b) {
    Matriz r = criarMatriz(a.linhas, a.colunas);

    for (int i = 0; i < a.linhas; i++) {
        for (int j = 0; j < a.colunas; j++) {
            E(r, i, j) = E(a, i, j) + E(b, i, j);
        }
    }

    return r;
}

Matriz subtrair(Matriz a, Matriz b) {
    Matriz r = criarMatriz(a.linhas, a.colunas);

    for (int i = 0; i < a.linhas; i++) {
        for (int j = 0; j < a.colunas; j++) {
            E(r, i, j) = E(a, i, j) - E(b, i, j);
        }
    }

    return r;
}

Matriz multiplicar(Matriz a, Matriz b) {
    Matriz r = criarMatriz(a.linhas, b.colunas);

    for (int i = 0; i < a.linhas; i++) {
        for (int j = 0; j < b.colunas; j++) {
            for (int k = 0; k < a.colunas; k++) {
                E(r, i, j) += E(a, i, k) * E(b, k, j);
            }
        }
    }

    return r;
}

// Inversa por Gauss-Jordan. Retorna OK, ou ERRO_DIVISOR_SINGULAR
// (tambem ERRO_DIVISOR_NAO_QUADRADA se a matriz nao for quadrada).
Status inversa(Matriz a, Matriz *inv) {
    if (a.linhas != a.colunas) {
        return ERRO_DIVISOR_NAO_QUADRADA;
    }

    int n = a.linhas;
    int largura = 2 * n;

    double *aug = calloc((size_t)n * largura, sizeof(double));

    if (aug == NULL) {
        printf("Erro de memoria!\n");
        exit(1);
    }

    #define G(i, j) aug[(i) * largura + (j)]

    // Monta [A | I]
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            G(i, j) = E(a, i, j);
        }
        G(i, n + i) = 1.0;
    }

    for (int i = 0; i < n; i++) {
        // Pivoteamento parcial
        int pivo = i;

        for (int k = i + 1; k < n; k++) {
            if (fabs(G(k, i)) > fabs(G(pivo, i))) {
                pivo = k;
            }
        }

        // Pivo nulo: matriz singular
        if (fabs(G(pivo, i)) < EPSILON) {
            free(aug);
            return ERRO_DIVISOR_SINGULAR;
        }

        if (pivo != i) {
            for (int j = 0; j < largura; j++) {
                double temp = G(i, j);
                G(i, j) = G(pivo, j);
                G(pivo, j) = temp;
            }
        }

        double divisor = G(i, i);

        for (int j = 0; j < largura; j++) {
            G(i, j) /= divisor;
        }

        for (int k = 0; k < n; k++) {
            if (k != i) {
                double fator = G(k, i);

                for (int j = 0; j < largura; j++) {
                    G(k, j) -= fator * G(i, j);
                }
            }
        }
    }

    *inv = criarMatriz(n, n);

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            E((*inv), i, j) = G(i, n + j);
        }
    }

    #undef G

    free(aug);
    return OK;
}

// A / B = A x B^-1
Status dividir(Matriz a, Matriz b, Matriz *resultado) {
    Matriz inv;
    Status status = validarDivisao(a, b);

    if (status != OK) {
        return status;
    }

    status = inversa(b, &inv);

    if (status != OK) {
        return status;
    }

    *resultado = multiplicar(a, inv);
    liberarMatriz(&inv);

    return OK;
}

// Executa a operacao sequencialmente, da esquerda para a direita:
// ((M1 op M2) op M3) op ...
// Retorna 1 em caso de sucesso (resultado preenchido) ou 0 em caso de erro.
int executarOperacao(int opcao, Matriz *matrizes, int quantidade, Matriz *resultado) {
    *resultado = copiarMatriz(matrizes[0]);

    for (int i = 1; i < quantidade; i++) {
        Matriz novo;
        Status status = OK;

        if (opcao == 1 || opcao == 2) {
            status = validarSomaSubtracao(*resultado, matrizes[i]);
            if (status == OK) {
                novo = (opcao == 1) ? somar(*resultado, matrizes[i])
                                    : subtrair(*resultado, matrizes[i]);
            }
        } else if (opcao == 3) {
            status = validarMultiplicacao(*resultado, matrizes[i]);
            if (status == OK) {
                novo = multiplicar(*resultado, matrizes[i]);
            }
        } else {
            status = dividir(*resultado, matrizes[i], &novo);
        }

        if (status != OK) {
            mostrarErro(status, i, *resultado, matrizes[i]);
            liberarMatriz(resultado);
            return 0;
        }

        liberarMatriz(resultado);
        *resultado = novo;
    }

    return 1;
}

/* ===================== MAIN ===================== */

int main(void) {
    int quantidade, opcao;

    printf("====================================\n");
    printf("     CALCULADORA DE MATRIZES\n");
    printf("====================================\n");

    quantidade = lerQuantidadeMatrizes();

    Matriz *matrizes = malloc((size_t)quantidade * sizeof(Matriz));

    if (matrizes == NULL) {
        printf("Erro de memoria!\n");
        return 1;
    }

    cadastrarMatrizes(matrizes, quantidade);

    // Permite realizar novas operacoes ate o usuario escolher sair
    do {
        mostrarMenu();

        if (scanf("%d", &opcao) != 1) {
            printf("Entrada invalida!\n");
            break;
        }

        if (opcao == 0) {
            printf("\nEncerrando o programa...\n");
            break;
        }

        if (opcao < 1 || opcao > 4) {
            printf("\nOpcao invalida!\n");
            continue;
        }

        Matriz resultado;

        if (executarOperacao(opcao, matrizes, quantidade, &resultado)) {
            mostrarResultado(resultado);
            liberarMatriz(&resultado);
        }

    } while (1);

    for (int i = 0; i < quantidade; i++) {
        liberarMatriz(&matrizes[i]);
    }

    free(matrizes);

    printf("\nPrograma finalizado!\n");

    return 0;
}
