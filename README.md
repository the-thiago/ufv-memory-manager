# ufv-memory-manager
Contributors
###### - Leandro Guimarães Oliveira - 4835
###### - Thiago Moreira de Souza - 6011
###### - Gabriel Antunes Fernandes Fonseca - 6039

#### Project developed in undergraduate course of Operating Systems at the Federal University of Viçosa.
#### Prof. Rodrigo Moreira - SIN 351

## ABOUT THE PROJECT
It is implementing in C language, from supplementary materials provided by the teacher, the First-in First-out algorithm to replace pages (Virtual Memory Content). The performance of the implemented algorithm was compared with the Random one provided by the teacher. The main features of the FIFO code and algorithm are explained. At the end of the program execution, the number of missing pages for the algorithm passed as a parameter, FIFO or Random, is displayed.

## HOW TO USE
##### COMMANDS
1- We must compile the program with the following instruction:

`$ gcc -Wall vmm.c -o vmm`

2- We must run the program passing 3 parameters. Our program supports the first argument as "random" or "fifo", the first one executes the algorithm provided by the teacher and the second one developed by us. The output will be a number indicating the amount of missing pages for the algorithm passing as a parameter.

`$ ./vmm random 10 < anomaly.dat`

`$ ./vmm fifo 10 < anomaly.dat`

##### EXAMPLES OF USE

![Image showing the use of the program](https://github.com/the-thiago/UFV-GerenciadorDeMemoria/blob/master/ImagensDoReadMe/exemploDeUso.png?raw=true)

##  THE ALGORITHM AND COMPARISONS

##### First-in First-out Algorithm (First In, First Out) - FIFO
The operating system maintains a list of current pages in memory, the (oldest) page at the head of the queue is the first to be discarded. Its main advantage is the simplicity of implementation. However, as it doesn't take into account how often a page is being accessed, it can remove an old page that is accessed frequently.

##### FIFO implementation

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
The implemented function must return the index of which page of the page table will be replaced. It takes as parameters page_table, fifo_frm and num_pages (among others that are not used by our function), fifo_frm tells us which is the oldest frame in physical memory, that is, which frame will be replaced. Each page of the table, at index PT_FRAMEID set to 0, indicates which frame the page is referenced (-1 when it does not map anything in physical memory). Just go through the page_table through a loop, with the help of num_pages, checking if the index page i references the same frame as fifo_frm, returning which page should be replaced.

Two printfs on lines 49 and 115, which are initially commented out, help to understand when pages are missing. When there is a page fault and there is still free memory, and when there is a page fault and FIFO is called to free memory. Like the following example:

![Image shows running with prints](https://github.com/the-thiago/UFV-GerenciadorDeMemoria/blob/master/ImagensDoReadMe/execucaoComPrints.png?raw=true)

##### Comparing FIFO with Random
![Image shows running with prints](https://github.com/the-thiago/UFV-GerenciadorDeMemoria/blob/master/ImagensDoReadMe/tabela.png?raw=true)

The table above presents us with 10 executions of each algorithm, Random and FIFO. The averages are not that different since "anomaly.dat" is not that big. FIFO always generates the same response for the same entry, as it does not take into account which pages are being used the most, it happens to replace the page that arrived first, but is the most used. Random, on the other hand, had a variation of 8 to 11 page faults, depending on randomness, it can happen to have an excellent or terrible performance, the average was close to the FIFO. Both algorithms are simple and perform worse than other algorithms in the literature.
