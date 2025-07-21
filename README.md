# Projeto PythoCcompiler

## Integrantes

Caio Lucas Lelis Borges
Matrícula: 211062900
turma: 18h

Amanda Alves Campos
Matrícula: 190083590

Erick Santos
Matrícula: 211061672

## Visão Geral

O `PythoCcompiler` é um projeto que implementa um compilador e uma máquina virtual para uma linguagem de script customizada, semelhante a C, utilizando Python puro. O projeto é dividido em duas partes principais:

1.  **Compilador (`main.py`):** Responsável por analisar o código-fonte da linguagem customizada e traduzi-lo para um bytecode intermediário.
2.  **Máquina Virtual (`pyc_vm.py`):** Responsável por executar o bytecode gerado pelo compilador.

## Análise Léxica e Sintática (Parser)

A análise léxica e sintática é realizada no arquivo `main.py`, principalmente pelas classes `token_parser` e `l_parser`.

### Análise Léxica (`token_parser`)

A classe `token_parser` é o analisador léxico (ou _scanner_). Sua função é ler o código-fonte como uma string e dividi-lo em uma sequência de _tokens_.

- **Tokens:** Os tokens representam as unidades fundamentais da linguagem, como números, strings, identificadores e palavras-chave. Os tipos de tokens são definidos na classe `token` (ex: `TK_NUM_INT`, `TK_ID`, `TK_IF`).
- **Processo:** O método `next()` da classe `token_parser` avança pelo código-fonte caractere por caractere, agrupando-os para formar tokens. Ele consegue identificar:
  - Números (inteiros e de ponto flutuante).
  - Strings (delimitadas por aspas duplas).
  - Identificadores (nomes de variáveis e funções).
  - Palavras-chave reservadas (`if`, `else`, `for`, etc.).
  - Operadores (`+`, `-`, `*`, `/`, `==`, etc.).
  - Símbolos diversos (`(`, `)`, `{`, `}`, `;`, `,`).

### Análise Sintática (`l_parser`)

A classe `l_parser` é o analisador sintático (ou _parser_). Ela recebe a sequência de tokens do `token_parser` e constrói uma estrutura de dados hierárquica, geralmente uma Árvore de Sintaxe Abstrata (AST), que representa a estrutura gramatical do código.

- **Gramática:** O parser implementa uma gramática _top-down_ com recursão descendente para analisar a estrutura do código. Os métodos da classe (`expr`, `statement`, `ifstat`, etc.) correspondem às regras da gramática da linguagem.
- **Precedência de Operadores:** A lógica no método `expr` lida com a precedência e associatividade dos operadores aritméticos e lógicos, garantindo que expressões como `1 + a * 5` sejam avaliadas corretamente.
- **Estruturas de Controle:** O parser reconhece e estrutura construções como:
  - **Expressões:** Atribuições, chamadas de função, operações aritméticas.
  - **Declarações:** `if-else`, `while`, `for`.
  - **Definições de Funções:** `nome_funcao(arg1, arg2) { ... }`.
  - **Variáveis Globais:** `global var1;`.
- **Saída:** O resultado do parser é uma lista de nós (`parser_list`) que representa o programa de forma estruturada, pronta para a próxima fase.

## Análise Semântica e Geração de Código (`l_compiler`)

A classe `l_compiler` realiza a análise semântica e a geração do código intermediário (bytecode).

### Análise Semântica

- **Gerenciamento de Variáveis:** O compilador diferencia variáveis locais e globais. Ele mantém um registro das variáveis declaradas (`local_vars`, `global_vars`) para garantir que elas sejam usadas corretamente.
- **Resolução de Nomes:** Durante a compilação, o `l_compiler` resolve os nomes de variáveis e funções, associando-os a endereços ou índices.
- **Verificação de Tipos (Limitada):** A verificação de tipos é dinâmica e ocorre principalmente em tempo de execução na VM. O compilador não realiza uma verificação de tipos estática rigorosa.

### Geração de Código

- **Bytecode:** O compilador traduz a AST gerada pelo parser em uma lista de operações de máquina virtual (`op_list`). Cada operação é uma instância da classe `vm_op`.
- **Operações da VM (`vm_op`):** As operações incluem:
  - Aritméticas: `OP_ADD`, `OP_SUB`, `OP_MUL`, `OP_DIV`.
  - Lógicas: `OP_EQ`, `OP_GT`, `OP_AND`, `OP_OR`.
  - Controle de Fluxo: `OP_JZ` (salto se zero), `OP_JMP` (salto incondicional).
  - Acesso à Memória: `OP_PUSH` (empilhar na pilha), `OP_ASSIGN` (atribuir valor).
  - Chamada de Função: `OP_FUN`, `OP_RET`.
- **Registradores Virtuais:** O compilador utiliza um sistema de registradores virtuais (`freereg`) para armazenar resultados intermediários de expressões, otimizando a execução.

## Máquina Virtual (`pyc_vm.py`)

A classe `py2c_vm` é a máquina virtual que executa o bytecode.

- **Arquitetura baseada em Pilha:** A VM utiliza uma arquitetura baseada em pilha (`stacks`) para gerenciar dados, argumentos de função e variáveis locais.
- **Ciclo de Execução:** O método `run()` da VM entra em um loop infinito, onde a cada iteração:
  1.  Busca a próxima instrução de bytecode no endereço apontado pelo contador de programa (`pc`).
  2.  Decodifica a instrução.
  3.  Executa a operação correspondente (ex: realiza uma soma, desvia o fluxo de execução, etc.).
  4.  Incrementa o `pc` para a próxima instrução.
- **Gerenciamento de Chamadas de Função:** A VM gerencia o fluxo de chamadas de função usando uma pilha de chamadas (`call_infos`), salvando o estado (como o `pc` de retorno) antes de pular para o código da função.
- **Funções Importadas:** A VM pode interagir com o ambiente Python através de funções importadas (como `printf`, `list`, `len`), permitindo que a linguagem de script execute operações de I/O e manipule estruturas de dados do Python.
## Como Rodar o Projeto

O projeto não requer dependências externas além do Python. Para executá-lo, siga os passos abaixo:

1. **Clone o repositório (ou tenha os arquivos `main.py` e `pyc_vm.py` no mesmo diretório).**

2. **Execute o arquivo principal:**

   O código de exemplo já está incluído no final do arquivo `main.py`. Para executá-lo, basta rodar o script diretamente no seu terminal:

   ```bash
   python main.py
   ```
3. ***Saída esperada***
        4 3 11