#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct fun Fun;

struct fun {
  int cod;
  char nome[100];
  char cargo[25];
  char nasc[11];
  char cpf[12];
  char sexo;
  char status;
};

int menuPrincipal(void);
void cadastraFun(void);
void buscaFun(void);
void editaFun(void);
void excluiFun(void);
void listaFun(void);
void exibeFuncionario(Fun*);
void gravaFun(Fun*);

int main(void) {
  int opcao;
  opcao = menuPrincipal();
  while (opcao != 0) {
    switch (opcao) {
      case 1 :  cadastraFun();
                break;
      case 2 :  buscaFun();
                break;
      case 3 :  editaFun();
                break;
      case 4 :  excluiFun();
                break;
      case 5 :  listaFun();
                break;
    }
    opcao = menuPrincipal();
  }
  return 0;
}

int menuPrincipal(void) {
  int op;
  printf("\n\n");
  printf("= = = = = = = S I G Staff = = = = = = = = = =\n");
  printf("= Sistema de Gerenciamento de Funcionários  = \n");
  printf("= = = = = = = = = = = = = = = = = = = = = = = \n");
  printf("\nMenu Principal\n");
  printf("1 - Cadastrar funcionário\n");
  printf("2 - Pesquisar funcionário\n");
  printf("3 - Alterar funcionário\n");
  printf("4 - Excluir funcionário\n");
  printf("5 - Listar todos os funcionários\n");
  printf("0 - Encerrar programa\n");
  printf("Escolha sua opção: ");
  scanf("%d", &op);
  return op;
}


void cadastraFun(void) {
  Fun* funcionario;
  printf("\n\n");
  printf("= = = = = = = = = = == ==  \n");
  printf("= Cadastrar Funcionário = \n");
  printf("= = = = = = = = = = = = =\n");
  funcionario = (Fun*) malloc(sizeof(Fun));
  printf("\nInforme o código do funcionário: ");
  scanf("%d", &funcionario->cod);
  printf("Informe o nome do funcionário: ");
  scanf(" %99[^\n]", funcionario->nome);
  printf("Informe o cargo do funcionário: ");
  scanf(" %24[^\n]", funcionario->cargo);
  printf("Informe a data de nascimento do funcionário (dd/mm/aaaa): ");
  scanf(" %10[^\n]", funcionario->nasc);
  getchar();
  printf("Informe o sexo do funcionario: ");
  scanf("%c", &funcionario->sexo);
  funcionario->status = '1';
  printf("###############################\n");
  exibeFuncionario(funcionario);
  printf("###############################\n");
  gravaFun(funcionario);
}


void buscaFun(void) {
  FILE* fp;
  Fun* funcionario;
  int achou;
  char procurado[15];
  fp = fopen("funcionario.dat", "rb");
  if (fp == NULL) {
    printf("Ops! Ocorreu um erro na abertura do arquivo!\n");
    printf("Não é possível continuar o programa...\n");
    exit(1);
  }
  printf("\n\n");
  printf("= = = = = = = = = = == = =  \n");
  printf("= = Buscar Funcionário = = \n");
  printf("= = = = = = = = = = == = =  \n");
  printf("Informe o nome do funcionário a ser buscado: ");
  scanf(" %99[^\n]", procurado);
  funcionario = (Fun*) malloc(sizeof(Fun));
  achou = 0;
  while((!achou) && (fread(funcionario, sizeof(Fun), 1, fp))) {
   if ((strcmp(funcionario->nome, procurado) == 0) && (funcionario->status == '1')) {
     achou = 1;
   }
  }
  fclose(fp);
  if (achou) {
    exibeFuncionario(funcionario);
  } else {
    printf("O Funcionário %s não foi encontrado...\n", procurado);
  }
  free(funcionario);
}


void editaFun(void) {
  FILE* fp;
  Fun* funcionario;
  int achou;
  char resp;
  char procurado[15];
  fp = fopen("funcionario.dat", "r+b");
  if (fp == NULL) {
    printf("Ops! Ocorreu um erro na abertura do arquivo!\n");
    printf("Não é possível continuar o programa...\n");
    exit(1);
  }
  printf("\n\n");
  printf("= = = = = = = = = = = = = = \n");
  printf("= = Editar Funcionário = = \n");
  printf("= = = = = = = = = = = = = = \n");
  printf("Informe o nome do funcionário a ser alterado: ");
  scanf(" %99[^\n]", procurado);
  funcionario = (Fun*) malloc(sizeof(Fun));
  achou = 0;
  while((!achou) && (fread(funcionario, sizeof(Fun), 1, fp))) {
   if ((strcmp(funcionario->nome, procurado) == 0) && (funcionario->status == '1')) {
     achou = 1;
   }
  }
  if (achou) {
    exibeFuncionario(funcionario);
    getchar();
    printf("Deseja realmente editar este funcionario (s/n)? ");
    scanf("%c", &resp);
    if (resp == 's' || resp == 'S') {
      printf("\nInforme o código do funcionario: ");
      scanf("%d", &funcionario->cod);
      printf("Informe o nome do Funcionário: ");
      scanf(" %99[^\n]", funcionario->nome);
      printf("Informe o cargo do funcionário: ");
      scanf(" %24[^\n]", funcionario->cargo);
      printf("Informe a data de nascimento do funcionário (dd/mm/aaaa): ");
      scanf(" %10[^\n]", funcionario->nasc);
      getchar();
      printf("Informe o sexo do funcionario: ");
      scanf("%c", &funcionario->sexo);
      funcionario->status = '1';
      fseek(fp, (-1)*sizeof(Fun), SEEK_CUR);
      fwrite(funcionario, sizeof(Fun), 1, fp);
      printf("\nFuncionário editado com sucesso!!!\n");
    } else {
      printf("\nOk, os dados não foram alterados\n");
    }
  } else {
    printf("O funcionário %s não foi encontrado...\n", procurado);
  }
  free(funcionario);
  fclose(fp);
}


void excluiFun(void) {
  FILE* fp;
  Fun* funcionario;
  int achou;
  char resp;
  char procurado[15];
  fp = fopen("funcionario.dat", "r+b");
  if (fp == NULL) {
    printf("Ops! Ocorreu um erro na abertura do arquivo!\n");
    printf("Não é possível continuar o programa...\n");
    exit(1);
  }
  printf("\n\n");
  printf("= = = = = = = = = = == = = \n");
  printf("= = Apagar Funcionário = = \n");
  printf("= = = = = = = = = = == = = \n");
  printf("Informe o nome do funcionário a ser apagado: ");
  scanf(" %99[^\n]", procurado);
  funcionario = (Fun*) malloc(sizeof(Fun));
  achou = 0;
  while((!achou) && (fread(funcionario, sizeof(Fun), 1, fp))) {
   if ((strcmp(funcionario->nome, procurado) == 0) && (funcionario->status == '1')) {
     achou = 1;
   }
  }

  if (achou) {
    exibeFuncionario(funcionario);
    getchar();
    printf("Deseja realmente apagar este funcionario (s/n)? ");
    scanf("%c", &resp);
    if (resp == 's' || resp == 'S') {
      funcionario->status = '0';
      fseek(fp, (-1)*sizeof(Fun), SEEK_CUR);
      fwrite(funcionario, sizeof(Fun), 1, fp);
      printf("\nFuncionário excluído com sucesso!!!\n");
     } else {
       printf("\nOk, os dados não foram alterados\n");
     }
  } else {
    printf("O funcionário %s não foi encontrado...\n", procurado);
  }
  free(funcionario);
  fclose(fp);
}


void listaFun(void) {
  FILE* fp;
  Fun* funcionario;
  fp = fopen("funcionario.dat", "rb");
  if (fp == NULL) {
    printf("Ops! Ocorreu um erro na abertura do arquivo!\n");
    printf("Não é possível continuar o programa...\n");
    exit(1);
  }
  printf("\n\n");
  printf("= = = = = = = = = = == = = \n");
  printf("= = Exibe Funcionário = = \n");
  printf("= = = = = = = = = = == = = \n");
  funcionario = (Fun*) malloc(sizeof(Fun));
  while(fread(funcionario, sizeof(Fun), 1, fp)) {
    if (funcionario->status == '1') {
      exibeFuncionario(funcionario);
    }
  }
  fclose(fp);
  free(funcionario);
}


void gravaFun(Fun* funcionario) {
  FILE* fp;
  fp = fopen("funcionario.dat", "ab");
  if (fp == NULL) {
    printf("Ops! Ocorreu um erro na abertura do arquivo!\n");
    printf("Não é possível continuar o programa...\n");
    exit(1);
  }
  fwrite(funcionario, sizeof(Fun), 1, fp);
  fclose(fp);
}


void exibeFuncionario(Fun* funcionario) {
  printf("Código: %d\n", funcionario->cod);
  printf("Nome: %s\n", funcionario->nome);
  printf("Sexo: %c\n", funcionario->sexo);
  printf("Cargo: %s\n", funcionario->cargo);
  printf("Nascimento: %s\n", funcionario->nasc);
  printf("Status: %c\n", funcionario->status);
  printf("\n");
}

