#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX 10

// Definição de Cores para o Terminal (Funcionalidade Extra)
#define RESET   "\x1b[0m"
#define VERMELHO "\x1b[31m"
#define VERDE    "\x1b[32m"
#define AMARELO  "\x1b[33m"
#define AZUL     "\x1b[34m"
#define CIANO    "\x1b[36m"

// Campos do Registro
typedef struct {
    int id;
    char arquivo[50];
    int paginas;
    char tipo; // 'N' para normal, 'P' para prioritário
} Trabalho;

// Estrutura da Fila Normal (Fila Simples com Deslocamento)
typedef struct {
    Trabalho dados[MAX];
    int fim;
} FilaNormal;

// Estrutura da Fila Prioritária (Fila Circular)
typedef struct {
    Trabalho dados[MAX];
    int frente;
    int fim;
    int total;
} FilaCircularPrioritaria;

// --- FUNÇÕES DE INICIALIZAÇÃO ---
void inicializarNormal(FilaNormal *f) {
    f->fim = 0;
}

void inicializarPrioritaria(FilaCircularPrioritaria *f) {
    f->frente = 0;
    f->fim = -1;
    f->total = 0;
}

// --- VERIFICAÇÕES DE ESTADO ---
int normalVazia(FilaNormal *f) { return f->fim == 0; }
int normalCheia(FilaNormal *f) { return f->fim == MAX; }
int prioritariaVazia(FilaCircularPrioritaria *f) { return f->total == 0; }
int prioritariaCheia(FilaCircularPrioritaria *f) { return f->total == MAX; }

// --- OPERAÇÃO: ENFILEIRAR ---
void enfileirarNormal(FilaNormal *f, Trabalho t) {
    if (normalCheia(f)) {
        printf(VERMELHO "Erro: Fila Normal cheia!\n" RESET);
        return;
    }
    f->dados[f->fim] = t;
    f->fim++;
    printf(VERDE "Trabalho %d (%s) adicionado à Fila Normal.\n" RESET, t.id, t.arquivo);
}

void enfileirarPrioritaria(FilaCircularPrioritaria *f, Trabalho t) {
    if (prioritariaCheia(f)) {
        printf(VERMELHO "Erro: Fila Prioritária cheia!\n" RESET);
        return;
    }
    f->fim = (f->fim + 1) % MAX;
    f->dados[f->fim] = t;
    f->total++;
    printf(AMARELO "Trabalho %d (%s) adicionado à Fila Prioritária.\n" RESET, t.id, t.arquivo);
}

// --- OPERAÇÃO: PROCESSAR PRÓXIMO ---
void processarProximo(FilaCircularPrioritaria *fp, FilaNormal *fn) {
    if (!prioritariaVazia(fp)) {
        Trabalho t = fp->dados[fp->frente];
        fp->frente = (fp->frente + 1) % MAX;
        fp->total--;
        printf(VERDE "\n[PROCESSANDO] Prioritário -> ID: %d | Arquivo: %s | Páginas: %d\n" RESET, t.id, t.arquivo, t.paginas);
    } else if (!normalVazia(fn)) {
        Trabalho t = fn->dados[0];
        for (int i = 0; i < fn->fim - 1; i++) {
            fn->dados[i] = fn->dados[i + 1];
        }
        fn->fim--;
        printf(VERDE "\n[PROCESSANDO] Normal -> ID: %d | Arquivo: %s | Páginas: %d\n" RESET, t.id, t.arquivo, t.paginas);
    } else {
        printf(VERMELHO "Nenhum trabalho de impressão na fila.\n" RESET);
    }
}

// --- OPERAÇÃO: LISTAR AMBAS AS FILAS ---
void listarFilas(FilaCircularPrioritaria *fp, FilaNormal *fn) {
    printf(CIANO "\n========== FILA PRIORITÁRIA (CIRCULAR) ==========\n" RESET);
    if (prioritariaVazia(fp)) {
        printf("Vazia\n");
    } else {
        int idx = fp->frente;
        for (int i = 0; i < fp->total; i++) {
            printf("[%d] ID: %d | %s | %d pág(s)\n", i + 1, fp->dados[idx].id, fp->dados[idx].arquivo, fp->dados[idx].paginas);
            idx = (idx + 1) % MAX;
        }
    }

    printf(CIANO "========== FILA NORMAL (SIMPLES) ==========\n" RESET);
    if (normalVazia(fn)) {
        printf("Vazia\n");
    } else {
        for (int i = 0; i < fn->fim; i++) {
            printf("[%d] ID: %d | %s | %d pág(s)\n", i + 1, fn->dados[i].id, fn->dados[i].arquivo, fn->dados[i].paginas);
        }
    }
    printf(CIANO "===========================================\n" RESET);
}

// --- OPERAÇÃO: BUSCAR TRABALHO POR ID ---
void buscarTrabalho(FilaCircularPrioritaria *fp, FilaNormal *fn, int id) {
    int idx = fp->frente;
    for (int i = 0; i < fp->total; i++) {
        if (fp->dados[idx].id == id) {
            printf(AMARELO "Encontrado na Fila Prioritária! Posição virtual: %d | Arquivo: %s | %d págs.\n" RESET, i + 1, fp->dados[idx].arquivo, fp->dados[idx].paginas);
            return;
        }
        idx = (idx + 1) % MAX;
    }

    for (int i = 0; i < fn->fim; i++) {
        if (fn->dados[i].id == id) {
            printf(VERDE "Encontrado na Fila Normal! Posição: %d | Arquivo: %s | %d págs.\n" RESET, i + 1, fn->dados[i].arquivo, fn->dados[i].paginas);
            return;
        }
    }

    printf(VERMELHO "Trabalho com ID %d não foi encontrado.\n" RESET, id);
}

// --- OPERAÇÃO: CANCELAR (REMOVER) TRABALHO ---
void cancelarTrabalho(FilaCircularPrioritaria *fp, FilaNormal *fn, int id) {
    for (int i = 0; i < fn->fim; i++) {
        if (fn->dados[i].id == id) {
            for (int j = i; j < fn->fim - 1; j++) {
                fn->dados[j] = fn->dados[j + 1];
            }
            fn->fim--;
            printf(VERDE "Trabalho %d cancelado com sucesso da Fila Normal.\n" RESET, id);
            return;
        }
    }

    int idx = fp->frente;
    for (int i = 0; i < fp->total; i++) {
        if (fp->dados[idx].id == id) {
            FilaCircularPrioritaria aux;
            inicializarPrioritaria(&aux);
            
            int curr = fp->frente;
            for (int k = 0; k < fp->total; k++) {
                if (fp->dados[curr].id != id) {
                    enfileirarPrioritaria(&aux, fp->dados[curr]);
                }
                curr = (curr + 1) % MAX;
            }
            *fp = aux;
            printf(VERDE "Trabalho %d cancelado com sucesso da Fila Prioritária.\n" RESET, id);
            return;
        }
        idx = (idx + 1) % MAX;
    }

    printf(VERMELHO "Não foi possível cancelar: ID %d não encontrado.\n" RESET, id);
}

// --- SISTEMA DE ARQUIVOS: SALVAR EM CSV ---
void salvarCSV(FilaCircularPrioritaria *fp, FilaNormal *fn) {
    FILE *arquivo = fopen("fila_impressao.csv", "w");
    if (arquivo == NULL) {
        printf(VERMELHO "Erro ao abrir o arquivo para salvar!\n" RESET);
        return;
    }

    int idx = fp->frente;
    for (int i = 0; i < fp->total; i++) {
        fprintf(arquivo, "%d;%s;%d;%c\n", fp->dados[idx].id, fp->dados[idx].arquivo, fp->dados[idx].paginas, fp->dados[idx].tipo);
        idx = (idx + 1) % MAX;
    }

    for (int i = 0; i < fn->fim; i++) {
        fprintf(arquivo, "%d;%s;%d;%c\n", fn->dados[i].id, fn->dados[i].arquivo, fn->dados[i].paginas, fn->dados[i].tipo);
    }

    fclose(arquivo);
    printf(VERDE "Dados salvos com sucesso em 'fila_impressao.csv'.\n" RESET);
}

// --- SISTEMA DE ARQUIVOS: CARREGAR DO CSV ---
void carregarCSV(FilaCircularPrioritaria *fp, FilaNormal *fn) {
    FILE *arquivo = fopen("fila_impressao.csv", "r");
    if (arquivo == NULL) {
        printf(AMARELO "Aviso: Nenhum arquivo de dados prévio encontrado. Iniciando filas limpas.\n" RESET);
        return;
    }

    inicializarNormal(fn);
    inicializarPrioritaria(fp);

    Trabalho t;
    // CORREÇÃO AQUI: Adicionado o '%' que faltava antes do [^;] e validação correta de leitura
    while (fscanf(arquivo, "%d;%49[^;];%d; %c\n", &t.id, t.arquivo, &t.paginas, &t.tipo) == 4) {
        if (t.tipo == 'P' || t.tipo == 'p') {
            t.tipo = 'P';
            enfileirarPrioritaria(fp, t);
        } else {
            t.tipo = 'N';
            enfileirarNormal(fn, t);
        }
    }

    fclose(arquivo);
    printf(VERDE "Dados carregados com sucesso do arquivo CSV!\n" RESET);
}

// --- PROGRAMA PRINCIPAL ---
int main() {
    FilaNormal filaNormal;
    FilaCircularPrioritaria filaPrioritaria;

    inicializarNormal(&filaNormal);
    inicializarPrioritaria(&filaPrioritaria);

    carregarCSV(&filaPrioritaria, &filaNormal);

    int opcao;
    do {
        printf("\n" CIANO "=== SISTEMA DE FILA DE IMPRESSÃO ===" RESET "\n");
        printf("1. Enfileirar Trabalho\n");
        printf("2. Processar Próximo Trabalho\n");
        printf("3. Listar Ambas as Filas\n");
        printf("4. Buscar Trabalho por ID\n");
        printf("5. Cancelar Trabalho por ID\n");
        printf("6. Salvar Dados (CSV)\n");
        printf("0. Sair do Programa\n");
        printf("Escolha uma opção: ");
        
        if (scanf("%d", &opcao) != 1) {
            printf(VERMELHO "Entrada inválida! Digite um número.\n" RESET);
            while (getchar() != '\n'); 
            continue;
        }

        switch (opcao) {
            case 1: {
                Trabalho t;
                printf("Digite o ID do trabalho: ");
                scanf("%d", &t.id);
                printf("Nome do arquivo (máx 50 carac.): ");
                scanf("%s", t.arquivo);
                printf("Quantidade de páginas: ");
                scanf("%d", &t.paginas);
                printf("Tipo [N - Normal / P - Prioritário]: ");
                scanf(" %c", &t.tipo);

                if (t.tipo == 'P' || t.tipo == 'p') {
                    t.tipo = 'P';
                    enfileirarPrioritaria(&filaPrioritaria, t);
                } else if (t.tipo == 'N' || t.tipo == 'n') {
                    t.tipo = 'N';
                    enfileirarNormal(&filaNormal, t);
                } else {
                    printf(VERMELHO "Tipo inválido! Operação abortada.\n" RESET);
                }
                break;
            }
            case 2:
                processarProximo(&filaPrioritaria, &filaNormal);
                break;
            case 3:
                listarFilas(&filaPrioritaria, &filaNormal);
                break;
            case 4: {
                int idBusca;
                printf("Digite o ID para busca: ");
                scanf("%d", &idBusca);
                buscarTrabalho(&filaPrioritaria, &filaNormal, idBusca);
                break;
            }
            case 5: {
                int idCancel;
                printf("Digite o ID do trabalho a ser cancelado: ");
                scanf("%d", &idCancel);
                cancelarTrabalho(&filaPrioritaria, &filaNormal, idCancel);
                break;
            }
            case 6:
                salvarCSV(&filaPrioritaria, &filaNormal);
                break;
            case 0:
                salvarCSV(&filaPrioritaria, &filaNormal);
                printf(AMARELO "Encerrando o sistema e salvando dados. Até mais!\n" RESET);
                break;
            default:
                printf(VERMELHO "Opção inválida! Tente novamente.\n" RESET);
        }
    } while (opcao != 0);

    return 0;
}
