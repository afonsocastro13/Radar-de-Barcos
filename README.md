#include <stdio.h>

#define LINHAS 20
#define COLUNAS 80
#define NUM_BARCOS 5

struct Barco {
    char id;    // 'A', 'B', 'C', 'D' ou 'E'
    int linha;
    int coluna;
};

void lerTabela(char tabela[LINHAS][COLUNAS], const char *ficheiro) {
    FILE *fp = fopen(ficheiro, "r");
    if (fp == NULL) {
        printf("Erro ao abrir o ficheiro %s\n", ficheiro);
        return;
    }

    for (int i = 0; i < LINHAS; i++) {
        for (int j = 0; j < COLUNAS; j++) {
            char c = fgetc(fp);
            if (c == '\n') c = fgetc(fp);
            tabela[i][j] = c;
        }
    }

    fclose(fp);
}

void imprimirTabela(char tabela[LINHAS][COLUNAS]) {
    for (int i = 0; i < LINHAS; i++) {
        for (int j = 0; j < COLUNAS; j++) {
            putchar(tabela[i][j]);
        }
        putchar('\n');
    }
}

void consultarCoordenadas(char tabela1[LINHAS][COLUNAS], char tabela2[LINHAS][COLUNAS]) {
    int escolhaTabela;
    char barco;
    printf("Escolha a tabela (1 ou 2): ");
    scanf("%d", &escolhaTabela);

    if (escolhaTabela != 1 && escolhaTabela != 2) {
        printf("Tabela invalida.\n");
        return;
    }

    printf("Digite a letra do barco (A, B, C, D ou E): ");
    scanf(" %c", &barco);

    char (*tabela)[COLUNAS] = (escolhaTabela == 1) ? tabela1 : tabela2;
    int encontrado = 0;

    for (int i = 0; i < LINHAS; i++) {
        for (int j = 0; j < COLUNAS; j++) {
            if (tabela[i][j] == barco) {
                printf("O barco %c encontra-se na linha %d e na coluna %d\n", barco, i + 1, j + 1);
                encontrado = 1;
            }
        }
    }

    if (!encontrado) {
        printf("Barco nao encontrado!\n");
    }
}

void listarMovimentos(char tabelaAntes[LINHAS][COLUNAS], char tabelaDepois[LINHAS][COLUNAS]) {
    char ids[NUM_BARCOS] = {'A', 'B', 'C', 'D', 'E'};
    struct Barco antes[NUM_BARCOS], depois[NUM_BARCOS];

    for (int i = 0; i < NUM_BARCOS; i++) {
        antes[i] = (struct Barco){ids[i], -1, -1};
        depois[i] = (struct Barco){ids[i], -1, -1};
    }

    for (int i = 0; i < LINHAS; i++) {
        for (int j = 0; j < COLUNAS; j++) {
            for (int k = 0; k < NUM_BARCOS; k++) {
                if (tabelaAntes[i][j] == ids[k]) {
                    antes[k].linha = i;
                    antes[k].coluna = j;
                }
                if (tabelaDepois[i][j] == ids[k]) {
                    depois[k].linha = i;
                    depois[k].coluna = j;
                }
            }
        }
    }

    for (int i = 0; i < NUM_BARCOS; i++) {
        if (antes[i].linha == -1 || depois[i].linha == -1) continue;

        if (antes[i].linha == depois[i].linha && antes[i].coluna == depois[i].coluna) {
            printf("%c: nao se moveu\n", antes[i].id);
        } else if (antes[i].linha > depois[i].linha) {
            printf("%c: cima\n", antes[i].id);
        } else if (antes[i].linha < depois[i].linha) {
            printf("%c: baixo\n", antes[i].id);
        } else if (antes[i].coluna > depois[i].coluna) {
            printf("%c: esquerda\n", antes[i].id);
        } else if (antes[i].coluna < depois[i].coluna) {
            printf("%c: direita\n", antes[i].id);
        }
    }
}

int main() {
    char tabelaAntes[LINHAS][COLUNAS];
    char tabelaDepois[LINHAS][COLUNAS];

    lerTabela(tabelaAntes, "tabela_antes.txt");
    lerTabela(tabelaDepois, "tabela_depois.txt");

    int opcao;

    do {
        printf("\nMenu:\n");
        printf("1 - Imprimir tabela \"antes\"\n");
        printf("2 - Imprimir tabela \"depois\"\n");
        printf("3 - Consultar as coordenadas de um barco\n");
        printf("4 - Listar barcos que se moveram\n");
        printf("5 - Sair\n");
        printf("Escolha uma opcao: ");
        scanf("%d", &opcao);
        printf("\n"); // <-- esta linha garante que "Tabela Antes:" aparece em nova linha

        switch (opcao) {
            case 1:
                printf("Tabela Antes:\n");
                imprimirTabela(tabelaAntes);
                break;
            case 2:
                printf("Tabela Depois:\n");
                imprimirTabela(tabelaDepois);
                break;
            case 3:
                consultarCoordenadas(tabelaAntes, tabelaDepois);
                break;
            case 4:
                listarMovimentos(tabelaAntes, tabelaDepois);
                break;
            case 5:
                printf("A sair...\n");
                break;
            default:
                printf("Opcao invalida. Tente novamente.\n");
        }

    } while (opcao != 5);

    return 0;
}

