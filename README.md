# Grafos na Prática

## Introdução

   O objetivo deste repositório é apresentar alguns conceitos básicos, assim como alguns dos algoritmos mais utilizados na área de Teoria dos Grafos. Através da biblioteca [NetworkX](http://networkx.readthedocs.io/en/stable/), da linguagem [Python](https://docs.python.org/3/), que possui ferramentas específicas para trabalhar com grafos e a modelagem de problemas através de grafos, serão apresentados alguns exemplos práticos selecionados, de soluções de problemas comuns em Teoria dos Grafos.

   Para aprofundar os estudos, e para futura referência, estão listados aqui alguns recursos didáticos em _Teoria dos Grafos_, _Python_ e _NetworkX_:

### Teoria dos Grafos

* [Aulas de Teoria dos Grafos do MIT no Youtube](https://www.youtube.com/playlist?list=PL6MpDZWD2gTF3mz26HSufmsIO-COKKb5j)
* [Material de Teoria dos Grafos da Universidade de Budapeste](http://cs.bme.hu/fcs/graphtheory.pdf)
* [Referência completa em Teoria dos Grafos - Wolfram Mathworld](http://mathworld.wolfram.com/topics/GraphTheory.html)


### Python

* [Curso Python para Zumbis](https://www.pycursos.com/python-para-zumbis/)
* [Curso completo de Python no Youtube (excript)](https://www.youtube.com/playlist?list=PLesCEcYj003QxPQ4vTXkt22-E11aQvoVj)
* [Curso completo de Python no Youtube (Ignorância Zero)](https://www.youtube.com/playlist?list=PLfCKf0-awunOu2WyLe2pSD2fXUo795xRe)
* [Introdução ao Python do MIT OpenCourseWare](https://ocw.mit.edu/courses/electrical-engineering-and-computer-science/6-01sc-introduction-to-electrical-engineering-and-computer-science-i-spring-2011/python-tutorial/)
* [Tutorial de Teoria dos Grafos em Python](http://www.python-course.eu/graphs_python.php)

### NetworkX

* [Tutorial sobre NetworkX no Youtube](https://www.youtube.com/playlist?list=PLbu9W4c-C0iC-f1ByMHSa07OSgEQQ-rAk)
* [Palestra sobre Análise de Redes Sociais com NetworkX](https://www.youtube.com/watch?v=7fsreJMy_pI)
* [Palestra sobre visualização de Grafos em 3D com NetworkX](https://www.youtube.com/watch?v=7fsreJMy_pI)
 
## O Jogo Snakes and Ladders

### Descrição do problema

   O jogo Snakes and Ladders é usualmente abordado nos cursos de Teoria dos Grafos e, portanto, é considerado um problema clássico. Isso se deve ao fato de ser uma aplicação interessante do conceito de Cadeia de Markov em grafos.
   O jogo, que surgiu há milhares de anos na Índia, possui regras bem simples. O objetivo do jogo é sair da posição inicial e chegar na posição final do tabuleiro. O número de movimentos do jogador é decidido por um dado, e escadas e cobras, fixadas no tabuleiro, oferecem ligação direta entre dois pontos, as escadas fazer o jogador avançar no caminho até a posição final, enquanto que as cobras fazem o jogador voltar no caminho.
   O que torna este jogo, de simples regras, em uma aplicação matemática interessante é que todo tabuleiro de Snakes and Ladders, independentemente das posições das cobras e escadas, pode ser representado exatamente por uma Cadeia de Markov, isto é, as probabilidades de se mover para qualquer casa no tabuleiro são fixas e independentes. Isto faz com que a vitória no jogo dependa apenas das combinações no dado, não envolvendo qualquer habilidade.
   O desafio portanto é representar o tabuleiro do jogo como um grafo e em seguida, implementar o algoritmo para descobrir a distribuição da Cadeia de Markov.
   
### Solução

O primeiro passo para solucionar o problema é importar as bibliotecas que serão utilizadas no ambiente Python. Serão utilizadas
as bibliotecas _NetworkX_ , _Numpy_, _matplotlib_ e _scipy_:

```markdown
import networkx as nx
import numpy as np
import matplotlib.pyplot as plt
import scipy
```
O próximo passo é gerar o grafo que representará o tabuleiro do jogo:

```markdown
#Cria o grafo
G = nx.DiGraph()
#Cria 36 Vertices
G.add_nodes_from([1,36])
#Cria as arestas
G.add_edges_from([(1,15),(1,3)])
G.add_edges_from([(2,3),(2,4)])
G.add_edges_from([(3,4),(3,7)])
G.add_edges_from([(4,7),(4,6)])
G.add_edges_from([(5,6),(5,7)])
G.add_edges_from([(6,7),(6,8)])
G.add_edges_from([(7,8),(7,27)])
G.add_edges_from([(8,27),(8,10)])
G.add_edges_from([(9,10),(9,11)])
G.add_edges_from([(10,11),(10,12)])
G.add_edges_from([(11,12),(11,13)])
G.add_edges_from([(12,13),(12,14)])
G.add_edges_from([(13,14),(13,15)])
G.add_edges_from([(14,15),(14,16)])
G.add_edges_from([(15,16),(15,4)])
G.add_edges_from([(12,13),(12,14)])
G.add_edges_from([(16,4),(16,29)])
G.add_edges_from([(17,29),(17,19)])
G.add_edges_from([(18,19),(18,6)])
G.add_edges_from([(19,6),(19,21)])
G.add_edges_from([(20,21),(20,22)])
G.add_edges_from([(21,22),(21,23)])
G.add_edges_from([(22,23),(22,16)])
G.add_edges_from([(23,16),(23,35)])
G.add_edges_from([(24,35),(24,26)])
G.add_edges_from([(25,26),(25,27)])
G.add_edges_from([(26,27),(26,28)])
G.add_edges_from([(27,28),(27,29)])
G.add_edges_from([(28,29),(28,30)])
G.add_edges_from([(29,30),(29,31)])
G.add_edges_from([(30,31),(30,30)])
G.add_edges_from([(31,30),(31,33)])
G.add_edges_from([(32,33),(32,12)])
G.add_edges_from([(33,12),(33,35)])
G.add_edges_from([(34,35),(34,36)])
G.add_edges_from([(35,36)])
G.add_edges_from([(36,36)])
```

O próximo passo é desenhar o grafo, posicionando-o conforme o tabuleiro:

```markdown
pos = nx.shell_layout(G)
i = 1
uneven = True
for y in xrange(1,7):
	for x in xrange(1,7):
		if uneven == True:
			pos[i] = ([(-1.0 + (3.0*(x-1))/6),(-1.0 + (3.0*(y-1))/6)])
			i=i+1
		else:
			pos[i] = ([(1.5 - (3.0*(x-1))/6),(-1.0 + (3.0*(y-1))/6)])
			i=i+1
	uneven = ~uneven
```
Agora, desenhamos os vértices, as arestas e exibimos a imagem com o resultado:

```markdown
#Desenha os vertices
nx.draw_networkx_nodes(G, pos)
#Desenha as arestas
nx.draw_networkx_edges(G, pos, arrows=True)
#mostra e salva o desenho
plt.savefig("Graph.png", format="PNG")
plt.show()
```
 
 
 **Imagem do grafo resultante:

![grafo snakes and ladders](https://github.com/alerabi/grafosnapratica/blob/7be6f80097e0abfd737ff80fd06706d2693542f9/T2/Graph.png)



Agora calculamos a matriz de probabilidade de transição P e a matriz de adjacência:

```markdown 
A = nx.adjacency_matrix(G)
A = A.toarray()

delta = np.zeros((36,36),dtype='f')
for i in xrange(0,36):
	if G.out_degree(i+1) != 0:
		delta[i,i] = 1.0/G.out_degree(i+1)
#Multiplica delta por A, encontrando assim a matriz de adjacencia
P = np.dot(delta, A)
```
Por fim, calculamos a distribuicao estacionaria através do algoritmo conhecido como power method( (k = 100)

```markdown
#Basta fazer w0*P^k, onde w0 e o vetor de probabilidade inicial

#definindo w0
w0 = np.zeros(36)
w0[0] = 1

#calculando P^k
Pk = P
for i in xrange(0,100):
	Pk = np.dot(Pk,P)

wk = np.dot(w0, Pk)
print(wk)	

#Salva w0 em um arquivo (importante para representar a cadeia de Markov)
np.savetxt("w0.txt",w0,fmt='%d') 

#Salva a matriz de probabilidade de transicao P
np.savetxt("P.txt",P,fmt='%.2f') 

#salva o resultado final apos o power method
np.savetxt("wk.txt",wk,fmt='%.10f')
```

## O Algoritmo de Prim

### Descrição do problema

   Outro problema clássico em Teoria dos Grafos é o de encontrar caminhos mínimos dentro de um grafo. Deseja-se saber qual é o menor caminho (ou o caminho de menor custo em um grafo ponderado) para se conectar dois pontos em um grafo.
   O algoritmo de Prim é um algoritmo guloso (greedy algorithm) que tem como objetivo contrar uma árvore geradora mínima (minimal spanning tree) num grafo ponderado e não direcionado. Isso significa que o algoritmo encontra um subgrafo do grafo de origem no qual a soma total das arestas é minimizada e todos os vértices estão interligados. 
   A solução do algoritmo é ótima, e portanto podemos sempre extrair uma árvore geradora mínima, dado um grafo G ponderado e não direcionado.
   Nosso próximo desafio consiste em gerar uma árvore geradora mínima, utilizando o Algoritmo de Prim, a partir de um dataset representado em um arquivo .gml
   
   
### Solução

A partir do código abaixo, utilizando as bibliotecas _NetworkX_, _Numpy_ e matplotlib, é possível implementar o Algoritmo de Prim em um dataset importado a partir de um arquivo .gml:

O primeiro passo é importar as bibliotecas necessárias para o projeto:

```markdown
import networkx as nx
import matplotlib.pyplot as plt
import numpy as np
```
O próximo passo é definir uma função que irá realizar o Algoritmo de Prim e retornar o grafo H resultante:

```markdown
#Algortimo de Prim, retorn a MST
def prim(Z, r):
	LAMBDA = []
	PI = []
	Q1 = {}
	Q2 = Z.nodes()
	for v in Q2:
		if v == r:
			LAMBDA.append(0)
			Q1[v] = 0
		else:
			LAMBDA.append(100000000)
			Q1[v] = 100000000
		PI.append(None)
	S = []
	while len(Q1) != 0:
		minimo = min(Q1, key = Q1.get)
		position = Q2.index(minimo)
		q = Q2[position]
		del Q1[minimo]
		S.append(q)
		N = Z.neighbors(q)
		for v in N:
			if v in Q1 and LAMBDA[Q2.index(v)] > Z[q][v]['value']:
				LAMBDA[Q2.index(v)] = Z[q][v]['value']
				Q1[v] = Z[q][v]['value']
				PI[Q2.index(v)] = q

	H = nx.Graph()
	for v in Q2:
		H.add_node(v)
	for i in range(0,len(Q2)):
		if PI[i] != None:
			H.add_edge(PI[i],Q2[i],value=LAMBDA[i])

	return H 	
```

Agora iremos testar a função com um grafo teste, antes de importar o dataset. Portanto, criamos um novo grafo:

```markdown 
##########################################################################
#Cria um grafo de teste
G = nx.Graph()
G.add_node("A")
G.add_node("B")
G.add_node("C")
G.add_node("D")
G.add_node("E")
G.add_node("F")
G.add_node("G")
G.add_node("H")
G.add_node("I")

#adiciona as arestas com peso
G.add_edge("A","B",value=4)
G.add_edge("B","C",value=8)
G.add_edge("C","D",value=7)
G.add_edge("D","E",value=9)
G.add_edge("E","F",value=10)
G.add_edge("F","G",value=2)
G.add_edge("H","I",value=7)
G.add_edge("I","C",value=2)
G.add_edge("H","A",value=8)
G.add_edge("B","H",value=11)
G.add_edge("D","F",value=14)
G.add_edge("I","G",value=6)
G.add_edge("F","C",value=4)
G.add_edge("H","G",value=1)
```

Agora, iremos fazer o plot deste grafo inicial:

```markdown
#desenha o grafo inicial
pos=nx.spring_layout(G)
nx.draw_networkx_nodes(G,pos)
nx.draw_networkx_edges(G,pos)
#Coloca na imagem o peso total
plt.text(0,1,str(G.size(weight='value')), fontdict = None)
plt.savefig("Graph1.png", format="PNG")
plt.show()
```


**O resultado na imagem é o grafo descrito:

![grafo prim inicial](https://github.com/alerabi/grafosnapratica/blob/3041695e5e30469ebf59179a9133a6df739db660/T3/Graph1.png)



Agora, chamamos a função anteriormente definida para encontrar a MST a partir do Algoritmo de Prim:

```markdown 
#chama o algoritmo de prim para encontrar uma mst
H = prim(G,"A")
nx.write_gml(H, "teste.gml")
```

A seguir, fazemos o plot do resultado do algoritmo:

```markdown
#desenha a mst obtida
pos=nx.spring_layout(H)
nx.draw_networkx_nodes(H,pos)
nx.draw_networkx_edges(H,pos)
#Desenha o peso total da mst obtida
plt.text(0,1,str(H.size(weight='value')), fontdict = None)
plt.savefig("MST1.png", format="PNG")
plt.show()
```


**Grafo resultante da função que implementa o Algoritmo de Prim:

![grafo prim mst](https://github.com/alerabi/grafosnapratica/blob/3041695e5e30469ebf59179a9133a6df739db660/T3/MST1.png)


Agora, após a verificação da validade da função projetada, extraímos um grafo de um dataset, representado em um arquivo .gml, e plotamos esse grafos:

```markdown 
#Obtem um grafo de um arquivo gml e o desenha
#coloca no desenho o valor do peso total do grafo
G1 = nx.read_gml('lesmis.gml')
pos=nx.spring_layout(G1)
nx.draw_networkx_nodes(G1,pos)
nx.draw_networkx_edges(G1,pos)
plt.text(0,1,str(G1.size(weight='value')), fontdict = None)
plt.savefig("lesmis.png", format="PNG")
plt.show()
```



** A imagem a seguir mostra o grafo que descreve o dataset importado:

![grafo dataset](https://github.com/alerabi/grafosnapratica/blob/3041695e5e30469ebf59179a9133a6df739db660/T3/lesmis.png)



Por fim, realizamos a função do Algoritmo de Prim, e exibimos a imagem da MST resultante:
```markdown 
H1 = prim(G1, G1.nodes()[0])

#desenha a mst obtida como peso total
pos=nx.spring_layout(H1)
nx.draw_networkx_nodes(H1,pos)
nx.draw_networkx_edges(H1,pos)
plt.text(0,1,str(H1.size(weight='value')), fontdict = None)
plt.savefig("lesmis-mst.png", format="PNG")
plt.show()
```


** A imagem mostra a MST resultante da execução do Algoritmo de Prim no dataset:
![grafo dataset MST](https://github.com/alerabi/grafosnapratica/blob/3041695e5e30469ebf59179a9133a6df739db660/T3/lesmis-mst.png)



## Sudoku e Coloração de Grafos

### Descrição do problema

Em grafos, os algoritmos de coloração são muito utilizados para resolver problemas de incompatibilidades, isso é feito ligando os vertices que são incompatíveis, e os colorindo com uma cor que ainda não está presente em nenhum de seus vizinhos, formando assim partições dentro do grafo que eliminam as incompatibilidades.

Um Exemplo de aplicação de coloração em grafos é um algoritmo para resolver o jogo Sudoku.

O sudoku é um jogo cuja regra básica é: preencha os números de forma que não haja repetição na mesma linha, coluna ou quadrado (3 x 3).
Utilizando o algoritmo de coloração podemos descrever as incompatibilidades de quadrante, linha e coluna do Sudoku e assim fazer um código que resolve o jogo.

### Solução

A partir do código abaixo, utilizando as bibliotecas _NetworkX_, _Numpy_ e matplotlib, é possível implementar o Algoritmo de Coloração que resolve o jogo de Sudoku:

Primeiramente, importamos as bibliotecas necessárias para a implementação do projeto:

```markdown 
import networkx as nx
import matplotlib.pyplot as plt
import numpy as np
```

Agora, definimos uma função auxiliar para imprimir uma tabela de sudoku a partir de um grafo:

```markdown
def drawSudoku(graphcolorList1):
	#desenha 9 linhas verticais e 9 linhas horizontais, a cada 3 linhas tem uma mais grossa
	for i in xrange(0, 10):
		if i%3 == 0:
			k = 4
		else:
			k = 2
		plt.plot([70 + i*10, 70 + i*10], [100, 190], 'k-', lw=k)
		plt.plot([70, 160], [100 + i*10, 100+i*10], 'k-', lw=k)

	#configura a area de visao do tabuleiro
	axes = plt.gca()
	axes.set_xlim([60,170])
	axes.set_ylim([90,200])

	#Escreve os numeros que ja foram escritos, de acordo com graphClorList
	for i in xrange(1,10):
		for j in xrange(1,10):
			if "n"+str(i+(9*(j-1))) in graphcolorList1:
				plt.text(64 + i*10,193 - j*10,graphcolorList1["n"+str(i+(9*(j-1)))], fontdict = None)

	plt.show()
	return
```

Uma nova função auxiliar vai imprimir um tabuleiro de sudoku preenchido com os respectivos vértices:

```markdown 
def drawSudokuNodes():
	#desenha 9 linhas verticais e 9 linhas horizontais, a cada 3 linhas tem uma mais grossa
	for i in xrange(0, 10):
		if i%3 == 0:
			k = 4
		else:
			k = 2
		plt.plot([70 + i*10, 70 + i*10], [100, 190], 'k-', lw=k)
		plt.plot([70, 160], [100 + i*10, 100+i*10], 'k-', lw=k)

	axes = plt.gca()
	axes.set_xlim([60,170])
	axes.set_ylim([90,200])

	#Escreve os numeros correspondente aos nos
	for i in xrange(1,10):
		for j in xrange(1,10):
			plt.text(64 + i*10,193 - j*10,"n"+str(i+((j-1)*9)), fontdict = None)

	plt.show()
	return
```

Agora, temos o algoritmo que resolve o Sudoku. Ele recebe um dicionário dos nós já coloridos. Os que não possuem cor ainda não estão no dicionário. O algoritmo retorna uma cor para cada nó, quando possível:

```markdown 
def solveSudoku(coloredNodesList):
	uncolored_nodes = []
	for i in xrange(1,82):
		if ("n"+str(i) not in coloredNodesList):
			uncolored_nodes.append("n"+str(i))

	solution = 1
	while len(uncolored_nodes) > 0:
		
	possibleColors = {}
		for n in uncolored_nodes:
			colors = [1,2,3,4,5,6,7,8,9]
			for v in G1.neighbors(n):
				if v in coloredNodesList:
					if coloredNodesList[v] in colors:
						colors.remove(coloredNodesList[v])
			if len(colors) == 1:
				coloredNodesList[n] = colors[0]
				uncolored_nodes.remove(n)
				solution = 1
				possibleColors = {}
				break
			elif len(colors) == 0:
				solution = 0
			else:
				possibleColors[n] = len(colors)


		if(len(possibleColors) > 0):
			n = min(possibleColors, key = possibleColors.get)
			colors = [1,2,3,4,5,6,7,8,9]
			for v in G1.neighbors(n):
				if v in coloredNodesList:
					if coloredNodesList[v] in colors:
						colors.remove(coloredNodesList[v])
			coloredNodesList[n] = min(colors)
			uncolored_nodes.remove(n)
		

		if solution == 0:
			print("O algoritmo nao encontrou nenhuma solucao")
			break
	return coloredNodesList	
```

Agora criamos o grafo que representará o tabuleiro, com suas incompatibilidades:

```markdown
G1 = nx.Graph()
#adicionando os vertices (n1 - n81)
for i in xrange(1,82):
	G1.add_node("n"+str(i))

#adicionando vertices de inconpatibilidadade
for k in xrange(0,9):
	for i in xrange(1+(9*k),10+(9*k)):
		for j in xrange(i+1,10+(9*k)):
			G1.add_edge("n"+str(i),"n"+str(j))

for k in xrange(0,9):
	for i in xrange(1+k,82+k,9):
		for j in xrange(i+9,82+k,9):
			G1.add_edge("n"+str(i),"n"+str(j))

little_square1 = ["n1","n2","n3", "n10", "n11", "n12", "n19", "n20", "n21"]
little_square2 = ["n4","n5","n6", "n13", "n14", "n15", "n22", "n23", "n24"]
little_square3 = ["n28","n29","n30", "n37", "n38", "n39", "n46", "n47", "n48"]
little_square4 = ["n31","n32","n33", "n40", "n41", "n42", "n49", "n50", "n51"]
little_square5 = ["n34","n35","n36", "n43", "n44", "n45", "n52", "n53", "n54"]
little_square6 = ["n55","n56","n57", "n64", "n65", "n66", "n73", "n74", "n75"]
little_square7 = ["n58","n59","n60", "n67", "n68", "n69", "n76", "n77", "n78"]
little_square8 = ["n61","n62","n63", "n70", "n71", "n72", "n79", "n80", "n81"]
little_square9 = ["n7","n8","n9", "n16", "n17", "n18", "n25", "n26", "n27"]


for i in xrange(0,9):
	for j in xrange(i+1,9):
		if G1.has_edge(little_square1[i], little_square1[j]) == False:
			G1.add_edge(little_square1[i],little_square1[j])

for i in xrange(0,9):
	for j in xrange(i+1,9):
		if G1.has_edge(little_square2[i], little_square2[j]) == False:
			G1.add_edge(little_square2[i],little_square2[j])

for i in xrange(0,9):
	for j in xrange(i+1,9):
		if G1.has_edge(little_square3[i], little_square3[j]) == False:
			G1.add_edge(little_square3[i],little_square3[j])

for i in xrange(0,9):
	for j in xrange(i+1,9):
		if G1.has_edge(little_square4[i], little_square4[j]) == False:
			G1.add_edge(little_square4[i],little_square4[j])

for i in xrange(0,9):
	for j in xrange(i+1,9):
		if G1.has_edge(little_square5[i], little_square5[j]) == False:
			G1.add_edge(little_square5[i],little_square5[j])

for i in xrange(0,9):
	for j in xrange(i+1,9):
		if G1.has_edge(little_square6[i], little_square6[j]) == False:
			G1.add_edge(little_square6[i],little_square6[j])

for i in xrange(0,9):
	for j in xrange(i+1,9):
		if G1.has_edge(little_square7[i], little_square7[j]) == False:
			G1.add_edge(little_square7[i],little_square7[j])

for i in xrange(0,9):
	for j in xrange(i+1,9):
		if G1.has_edge(little_square8[i], little_square8[j]) == False:
			G1.add_edge(little_square8[i],little_square8[j])

for i in xrange(0,9):
	for j in xrange(i+1,9):
		if G1.has_edge(little_square9[i], little_square9[j]) == False:
			G1.add_edge(little_square9[i],little_square9[j])
```





Criamos agora, a primeira instância completa de exemplo da execução do algoritmo:

```markdown 
colorList1 = {}
colorList1["n1"]  = 9
colorList1["n2"]  = 5
colorList1["n6"]  = 1
colorList1["n9"]  = 8
colorList1["n11"] = 2
colorList1["n12"] = 1
colorList1["n13"] = 8
colorList1["n14"] = 9
colorList1["n16"] = 6
colorList1["n19"] = 3
colorList1["n23"] = 4
colorList1["n24"] = 2
colorList1["n25"] = 1
colorList1["n26"] = 5
colorList1["n27"] = 9
colorList1["n28"] = 2
colorList1["n29"] = 4
colorList1["n32"] = 7
colorList1["n33"] = 8
colorList1["n36"] = 1
colorList1["n37"] = 1
colorList1["n39"] = 9
colorList1["n40"] = 3
colorList1["n41"] = 2
colorList1["n44"] = 6
colorList1["n45"] = 5
colorList1["n46"] = 8
colorList1["n49"] = 1
colorList1["n53"] = 7
colorList1["n54"] = 2
colorList1["n55"] = 4
colorList1["n56"] = 9
colorList1["n59"] = 1
colorList1["n61"] = 5
colorList1["n62"] = 8
colorList1["n64"] = 6
colorList1["n65"] = 8
colorList1["n66"] = 2
colorList1["n67"] = 9
colorList1["n72"] = 4
colorList1["n76"] = 4
colorList1["n77"] = 8
colorList1["n78"] = 3
colorList1["n80"] = 9
colorList1["n81"] = 6
```
Agora para resolver o problema, utilizamos as funções auxiliares para desenhar primeiro o tabuleiro. Depois aplicamos a função que resolve o jogo. Por fim, desenhamos novamente o grafo.

```markdown 

drawSudoku(colorList1)

colorList1 = solveSudoku(colorList1)

drawSudoku(colorList1)
```

Criando a instância do problema exemplo com dois elementos a menos em cada linha e, portanto, aumentando o grau de dificuldade:

```markdown 
colorList2 = {}
colorList2["n2"]  = 5
colorList2["n6"]  = 1
colorList2["n11"] = 2
colorList2["n12"] = 1
colorList2["n14"] = 9
colorList2["n19"] = 3
colorList2["n23"] = 4
colorList2["n25"] = 1
colorList2["n26"] = 5
colorList2["n29"] = 4
colorList2["n32"] = 7
colorList2["n33"] = 8
colorList2["n37"] = 1
colorList2["n40"] = 3
colorList2["n41"] = 2
colorList2["n44"] = 6
colorList2["n46"] = 8
colorList2["n49"] = 1
colorList2["n55"] = 4
colorList2["n56"] = 9
colorList2["n61"] = 5
colorList2["n64"] = 6
colorList2["n66"] = 2
colorList2["n72"] = 4
colorList2["n76"] = 4
colorList2["n77"] = 8
colorList2["n80"] = 9

drawSudoku(colorList2)

colorList2 = solveSudoku(colorList2)

drawSudoku(colorList2)
```

Criando a instancia do problema com apenas 1 elemento em cada linha:

```markdown 
colorList3 = {}
colorList3["n2"]  = 5
colorList3["n14"] = 9
colorList3["n25"] = 1
colorList3["n29"] = 4
colorList3["n40"] = 3
colorList3["n54"] = 2
colorList3["n61"] = 5
colorList3["n64"] = 6
colorList3["n80"] = 9

drawSudoku(colorList3)

colorList3 = solveSudoku(colorList3)

drawSudoku(colorList3)
```
