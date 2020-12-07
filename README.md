# UFV-GerenciadorDeMemoria
## Link do repositório: https://github.com/the-thiago/UFV-GerenciadorDeMemoria
###### - Leandro Guimarães Oliveira - 4835
###### - Thiago Moreira de Souza - 6011
###### - Gabriel Antunes Fernandes Fonseca - 6039

#### Trabalho executado em graduação pela disciplina de Sistemas Operacionais na Universidade Federal de Viçosa.
#### Prof. Rodrigo Moreira - SIN 351

## SOBRE O PROJETO
É implementando em linguagem C, a partir de materiais suplementares disponibilizados pelo professor, o algoritmo First-in First-out (Primeiro que Entra, Primeiro que Sai) para substituição de páginas (Conteúdo de Memória Virtual). Foi comparado o desempenho do algoritmo implementado com o Random (Aleatório) disponibilizado pelo professor. As principais funcionalidades do código e do algoritmo FIFO são explicadas. Ao final da execução do programa, é exibido a quantidade de falta de páginas para o algoritmo passado como parâmetro, o FIFO ou o Random.

## COMO UTILIZAR
##### COMANDOS
1- Devemos compilar o programa com a seguinte instrução:

`$ gcc -Wall vmm.c -o vmm`

2- Devemos executar o programa passando 3 parâmetros. Nosso programa suporta o primeiro argumento como "random" ou "fifo", o primeiro executa o algoritmo disponibilizado pelo professor e o segundo o desenvolvido por nós. A saída será um número indicando a quantidade de falta de páginas para o algoritmo passando como parâmetro.

`$ ./vmm random 10 < anomaly.dat`

`$ ./vmm fifo 10 < anomaly.dat`

##### EXEMPLO DE UTILIZAÇÃO

![Imagem que mostra a utilização do programa](https://github.com/the-thiago/UFV-GerenciadorDeMemoria/blob/master/ImagensDoReadMe/exemploDeUso.png?raw=true)

##  O ALGORITMO E COMPARAÇÕES

##### Algoritimo First-in First-out (Primeiro que Entra, Primeiro que Sai) - FIFO
O sistema operacional mantém uma lista de páginas correntes na memória, a página (mais antiga), no início da fila, é a primeira ser descartada. Sua principal vantagem é a simplicidade de implementação. No entanto, como não leva em conta o quanto uma página está sendo acessada, pode retirar uma página antiga, mas que é acessada com frequência.

##### Implementação do FIFO

```c
int fifo(int8_t** page_table, int num_pages, int prev_page,
         int fifo_frm, int num_frames, int clock) {

	int i;
	for (i = 0; i < num_pages; i++) {
		// Encontra na tabela de páginas a vítima, aquela que o endereço físico
		// referenciado é o mesmo do armazenado pelo fifo_frm
		if (page_table[i][PT_FRAMEID] == fifo_frm) {
			//printf("Page Fault - Página vítima: %d. Na moldura: %d.\n", i, fifo_frm);
			return i;
		}
	}

	return -1; // Tem alguma coisa errada, através de assert() o programa é encerrado
}

```
A função implementada deve retornar o índice de qual página da tabela de páginas será substituída. Recebe como parâmetros a page_table, fifo_frm e num_pages (entre outros que não são utilizados pela nossa função), o fifo_frm nos indica qual é a moldura mais velha na memória física, ou seja, qual moldura será substituída. Cada página da tabela, no índice PT_FRAMEID definido como 0, indica qual moldura a página está referenciado (-1 quando não mapeia nada na memória física). Basta percorrer a page_table por meio de um loop, com auxílio do num_pages, verificando se a página de índice i referencia a mesma moldura do fifo_frm, retornando qual página deve ser substituída.

Dois printfs nas linhas 49 e 115, que inicialmente estão como comentários, ajudam a entender os momentos em que há falta de páginas. Quando há falta de página e ainda tem memória livre, e quando há falta de página e o FIFO é chamado para liberar memória. Como o exemplo a seguir:

![Imagem mostra a execução com prints](https://github.com/the-thiago/UFV-GerenciadorDeMemoria/blob/master/ImagensDoReadMe/execucaoComPrints.png?raw=true)

##### Comparando o FIFO com o Random
Algoritmo/Execução  | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | Média de Page Faults
------------- | -------------
Random  | 11 | 9 | 8 | 9 | 10 | 8 |  10 | 10 |  8 |  10 | 9,3
FIFO | 9 | 9 | 9 | 9 | 9 | 9 | 9 | 9 | 9 | 9 | 9

A tabela acima nos apresenta 10 execuções de cada algoritmo, Random e FIFO. As médias não são tão diferentes já que "anomaly.dat" não é tão grande. O FIFO gera sempre a mesma resposta para a mesma entrada, como ele não leva em consideração quais páginas estão sendo mais utilizadas, acontece de ele substituir a página que chegou primeiro, mas é a mais utilizada. Já o Random, teve a variação de 8 a 11 page faults, como depende da aleatoriedade pode acontecer de ter uma performance excelente ou péssima, a média ficou próxima do FIFO. Ambos os algoritmos são simples e possuem desempenho pior que de outros algoritmos da literatura.

