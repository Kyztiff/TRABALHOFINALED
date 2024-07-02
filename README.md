#include <stdio.h> //colocamos esse include pq ele tem funções para entrada e saída de dados, como printf e scanf
#include <stdlib.h> // esse segundo include nos da funções para alocação de memória dinâmica como o malloc e free, 
//alem dos controles de processos (exit)
#include <string.h>// esse include foi colocado pq tem funções pra manipulação de strings como strcpy e o strncpy

// aqui é o começo do codigo que a gente começou montando a estrutura de uma tarefa
typedef struct Tarefa {
    int id;
    char descricao[100];
    int prazo;
    int concluida; // alocamos 0 para não concluida e 1 para concluida
    struct Tarefa* prox; // colocamos Tarefa* prox pra ser um ponteiro pra a próxima tarefa na lista
    struct Tarefa* ant;  // e aqui colocamos Tarefa* ant pra ser o outro ponteiro do codigo, que aponta pra a tarefa anterior na lista
} Tarefa;

// aqui fizemos o inicio do codigo em si, que é a criação da função que cria uma nova tarefa
Tarefa* criarTarefa(int id, const char* descricao, int prazo) {
    Tarefa* novaTarefa = (Tarefa*)malloc(sizeof(Tarefa)); // alocamos a memória para a nova tarefa
    if (novaTarefa == NULL) { // essa linha esta verificando se a alocação foi bem sucedida
        fprintf(stderr, "Erro na criação da tarefa (problema com a alocação de memoria).\n"); // e aqui é a saida do codigo caso a alocação nao de certo
        exit(1);
    }
    strncpy(novaTarefa->descricao, descricao, sizeof(novaTarefa->descricao) - 1); // essa linha esta copiando uma descrição para a nova tarefa
    novaTarefa->descricao[sizeof(novaTarefa->descricao) - 1] = '\0'; // e aqui ta garantindo que a string termine de forma correta
    novaTarefa->id = id; // aqui estamos definindo o ID da tarefa
    novaTarefa->prazo = prazo; // aqui, definindo o prazo da tarefa EM HORAS!!!
    novaTarefa->concluida = 0; // e aqui ta alocando o estado da tarefa que inicialmente, é alocada como ativa, e nao concluida
    novaTarefa->prox = NULL; // o próximo inicialmente é NULL que é a última tarefa na lista
    novaTarefa->ant = NULL; // e o anterior inicialmente é NULL que é a primeira tarefa na lista
    return novaTarefa; // aqui ta simplesmente retornando a nova tarefa criada
}

// aqui criamos uma outra função pra adicionar uma nova tarefa na lista
void adicionarTarefa(Tarefa** lista, int id, const char* descricao, int prazo) {
    Tarefa* novaTarefa = criarTarefa(id, descricao, prazo); // essa linha esta criando uma tarefa nova
    if (*lista == NULL) {
        *lista = novaTarefa; // caso a lista esteja vazia, a nova tarefa passa a ser a primeira da lista
    } else {
        Tarefa* temp = *lista;
        while (temp->prox != NULL) {
            temp = temp->prox; // essa linha faz a função percorrer ate o final da lista
        }
        temp->prox = novaTarefa; // aqui adicionamos a nova tarefa no final da lista
        novaTarefa->ant = temp; // e depois definimos o nó anterior da nova tarefa como o temporario
    }
}

// criamos outra função pra buscar uma tarefa na lista pelo ID
Tarefa* buscarTarefa(Tarefa* lista, int id) {
    while (lista != NULL) {
        if (lista->id == id) {
            return lista; // aqui retorna a tarefa se ela for encontrada pelo ID
        }
        lista = lista->prox; // ja essa linha avança pro próximo nó na lista
    }
    return NULL; // e por fim retorna NULL se a tarefa não for encontrada
}

// criamos mais uma função pra exibir todas as tarefas ativas
void exibirTarefasAtivas(Tarefa* lista) {
    int encontrou = 0;
    while (lista != NULL) {
        if (!lista->concluida) {
            printf("ID: %d\nDescricao: %s\nPrazo: %d horas\nStatus: Ativa\n\n",
                   lista->id, lista->descricao, lista->prazo); // esse print dentro do if exibe informações da tarefa ativa
            encontrou = 1;
        }
        lista = lista->prox; // e aqui avança pro proximo nó na lista
    }
    if (!encontrou) {
        printf("Nao existem tarefas ativas.\n"); // essa mensagem aparece apenas se não existirem tarefas ativas
    }
}

// essa função foi criada pra exibir todas as tarefas concluídas
void exibirTarefasConcluidas(Tarefa* lista) {
    int encontrou = 0;
    while (lista != NULL) {
        if (lista->concluida) {
            printf("ID: %d\nDescricao: %s\nPrazo: %d horas\nStatus: Concluida\n\n",
                   lista->id, lista->descricao, lista->prazo); // essa parte é basicamente igual a da função de cima, que exibe informações da tarefa concluída
            encontrou = 1;
        }
        lista = lista->prox; // e aqui novamente avança para o próximo nó na lista
    }
    if (!encontrou) {
        printf("Nao existem tarefas concluidas.\n"); // novamente, essa mensagem deve aparecer apenas se nao existirem tarefas concluidas
    }
}

// mais uma função feita pra exibir todas as tarefas (ativas e concluídas)
void exibirTodasTarefas(Tarefa* lista) {
    if (lista == NULL) {
        printf("Nao existem tarefas ativas ou concluidas.\n"); // essa mensagem vai aparecer se nao tiver nenhuma tarefa na lista
        return;
    }
    while (lista != NULL) {
        printf("ID: %d\nDescricao: %s\nPrazo: %d horas\nStatus: %s\n\n",
               lista->id, lista->descricao, lista->prazo,
               lista->concluida ? "Concluida" : "Ativa"); //essa é uma forma mais simples de se escrever um if com menos linhas aqui criamos um sistema dentro do quile que mostra informacoes da tarefa baseado no status
        lista = lista->prox; // e por fim, essa função tambem avança pro proximo nó na lista
    }
}

// outra função que foi criada pra marcar uma tarefa como concluida
void completarTarefa(Tarefa* lista, int id) {
    Tarefa* tarefa = buscarTarefa(lista, id); // aqui colocamos o ponteiro e a lista com id embutido pra fazer a busca de tarefas na lista pelo ID
    if (tarefa) {
        if (tarefa->concluida) {
            printf("Tarefa ja marcada como concluida.\n"); // essa mensagem aparece se a tarefa já tiver sido concluida
        } else {
            tarefa->concluida = 1; // nessa linha marcamos a tarefa como concluida
            printf("Tarefa marcada como concluida.\n");
        }
    } else {
        printf("Tarefa nao encontrada.\n"); // por fim, essa mensagem vai aparecas so se a tarefa não for encontrada
    }
}


// essa função foi feita pra editar uma tarefa existente, nao era um topico obrigatorio no trabalho, mas achamos interessante que o usuario consiga editar as tarefas sem alterar o ID
void editarTarefa(Tarefa* lista, int id) {
    Tarefa* tarefa = buscarTarefa(lista, id); // nessa linha estamos buscando a tarefa na lista pelo ID
    if (tarefa) {
        char novaDescricao[100];
        int novoPrazo;
        printf("Digite a nova descrição: ");
        scanf(" %[^\n]", novaDescricao); // aqui o codigo ta lendo a nova descrição da tarefa, a parte  scanf(" %[^\n]", permite que ousuario insira uma descrição usando a barra
        //de espaço, pois fica lendo a entrada do texto ate encontrar um \n 
        printf("Digite o novo prazo (em horas): ");
        scanf("%d", &novoPrazo); // e aqui esta lendo o novo prazo da tarefa
        strncpy(tarefa->descricao, novaDescricao, sizeof(tarefa->descricao) - 1); // usamos strncpy pra atualizar a descrição da tarefa
        tarefa->descricao[sizeof(tarefa->descricao) - 1] = '\0'; // e aqui tamo garantindo que a string seja terminada de forma correta
        tarefa->prazo = novoPrazo; // aqui estamos atualizando o prazo da tarefa
        printf("Tarefa editada com sucesso.\n");
    } else {
        printf("Tarefa nao encontrada.\n"); // e por fim, essa mensagem so vai aparecer caso a tarefa a ser editada nao seja encontrada 
    }
}

// essa função foi feita pra excluir uma tarefa da lista
void excluirTarefa(Tarefa** lista, int id) {
    Tarefa* tarefa = buscarTarefa(*lista, id); // aqui usamos o ponteiro tarefa pra buscar a tarefa na lista pelo ID
    if (tarefa) {
        if (tarefa->ant != NULL) {
            tarefa->ant->prox = tarefa->prox; // criamos um if pra atualizar os ponteiros para excluir a tarefa
        } else {
            *lista = tarefa->prox; // e um else pra atualizar a cabeça da lista se a tarefa for a primeira
        }
        if (tarefa->prox != NULL) {
            tarefa->prox->ant = tarefa->ant; //ele atualiza os ponteiros pra poder excluir a tarefa
        }
        free(tarefa); // serve para liberar a memória da tarefa excluída
        printf("Tarefa excluida com sucesso.\n");
    } else {
        printf("Tarefa nao encontrada.\n"); // esse print é executado caso a tarefa não for encontrada
    }
}

// função pra liberar a memória de todas as tarefas na lista
void liberarTarefas(Tarefa* lista) {
    while (lista != NULL) {
        Tarefa* temp = lista;
        lista = lista->prox; // a lista aponta pro próximo nó
        free(temp); // e aqui é liberada a memória da tarefa atual
    }
}

// essa função foi feita pra imprimir o menu de opções pro usuário
void imprimirMenu() {
    printf("\nMenu de Opcoes:\n");
    printf("1. Adicionar tarefa\n");
    printf("2. Visualizar todas as tarefas\n");
    printf("3. Visualizar tarefas ativas\n");
    printf("4. Visualizar tarefas concluidas\n");
    printf("5. Concluir tarefa\n");
    printf("6. Excluir tarefa\n");
    printf("7. Editar tarefa\n");
    printf("8. Sair\n");
    printf("Escolha uma opcao: ");
}// cada print representa uma opção, sendo elas 8 no total, uma foi colocada extra pois nao era obrigatoria no trabalho, a "editar tarefa"

// por fim, essa é a função principal do programa, feita com int main
int main() {
    Tarefa* lista = NULL; // aqui a gente inicia a lista de tarefas sem nada
    int opcao, id, prazo;
    char descricao[100];

    do {
        imprimirMenu(); // logo em seguida mostramos o menu de opções pro usuario
        scanf("%d", &opcao); // e com esse scanf se lê a opção escolhida pelo usuário

        switch (opcao) {
            case 1:
                printf("Digite o ID da tarefa (Apenas numeros naturais diferentes de 0): ");
                scanf("%d", &id);
                if (buscarTarefa(lista, id) != NULL) {
                    printf("ID ja existe, tente novamente.\n");
                    break;
                }
                printf("Digite a descricao da tarefa: ");
                scanf(" %[^\n]", descricao); // aqui o codigo ta lendo a nova descrição da tarefa, a parte  scanf(" %[^\n]", permite que ousuario insira uma descrição usando a barra
        //de espaço, pois fica lendo a entrada do texto ate encontrar um \n 
                printf("Digite o prazo (em horas): ");
                scanf("%d", &prazo); // esse scanf ta lendo o prazo da tarefa
                adicionarTarefa(&lista, id, descricao, prazo); // e esse permite adicionar a nova tarefa à lista
                printf("Tarefa adicionada com sucesso.\n");
                break;
            case 2:
                printf("\nTarefas:\n");
                exibirTodasTarefas(lista); // aqui estamos permitindo que o programa exiba todas as tarefas na lista
                break;
            case 3:
                printf("\nTarefas Ativas:\n");
                exibirTarefasAtivas(lista); //aqui que exiba todas as tarefas ativas 
                break;
            case 4:
                printf("\nTarefas Concluidas:\n");
                exibirTarefasConcluidas(lista); // e aqui, que exiba todas as tarefas concluídas
                break;
            case 5:
                printf("Digite o ID da tarefa a ser concluida (Apenas numeros naturais diferentes de 0): ");
                scanf("%d", &id);
                completarTarefa(lista, id); // marca uma tarefa como concluída
                break;
            case 6:
                printf("Digite o ID da tarefa a ser excluida (Apenas numeros naturais diferentes de 0): ");
                scanf("%d", &id);
                excluirTarefa(&lista, id); // exclui uma tarefa da lista
                break;
            case 7:
                printf("Digite o ID da tarefa a ser editada (Apenas numeros naturais diferentes de 0): ");
                scanf("%d", &id);
                editarTarefa(lista, id); // aqui podemos editar uma tarefa existente na lista, sem poder editar o ID dela
                break;
            case 8:
                printf("Saindo.\n");
                break;
                
            default:
                printf("Opcao invalida, tente novamente.\n"); // e essa mensagem sai no terminal caso a opção escolhida for invalida
        }
    } while (opcao != 8); // o loop desse menu vai continuar até que o usuário aperte o numero 8 que é a opção que sai do codigo

    liberarTarefas(lista); // aqui antes de acabar liberamos a memória de todas as tarefas na lista
    return 0; // e por fim colocamos o programa pra retornar 0 pra indicar que o programa foi executado com sucesso
}
