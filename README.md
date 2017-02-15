# Grafos na Prática

## Introdução

   O objetivo deste repositório é apresentar alguns conceitos básicos, assim como alguns dos algoritmos mais utilizados na área de Teoria dos Grafos. Através da biblioteca [NetworkX](http://networkx.readthedocs.io/en/stable/), da linguagem [Python](https://docs.python.org/3/), que possui ferramentas específicas para trabalhar com grafos e a modelagem de problemas através de grafos, serão apresentados alguns exemplos práticos selecionados, de soluções de problemas comuns em Teoria dos Grafos.

   Para aprofundar os estudos, e para futura referência, estão listados aqui alguns recursos didáticos em _Teoria dos Grafos_, _Python_ e _NetworkX_:

### Teoria dos Grafos

* [Aulas de Teoria dos Grafos do MIT no Youtube](https://www.youtube.com/playlist?list=PL6MpDZWD2gTF3mz26HSufmsIO-COKKb5j)
* [Material de Teoria dos Grafos da Universidade de Budapeste](http://cs.bme.hu/fcs/graphtheory.pdf)
* [Referência completa em Teoria dos Grafos - Wolfram Mathworld](http://mathworld.wolfram.com/topics/GraphTheory.html)


### Python

* [Curso Python para Zumbis] (https://www.pycursos.com/python-para-zumbis/)
* [Curso completo de Python no Youtube (excript)](https://www.youtube.com/playlist?list=PLesCEcYj003QxPQ4vTXkt22-E11aQvoVj)
* [Curso completo de Python no Youtube (Ignorância Zero)] (https://www.youtube.com/playlist?list=PLfCKf0-awunOu2WyLe2pSD2fXUo795xRe)
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
######################################################################################
#Calcula a matriz de probabilidade de transicao P
#Calcula a matriz de adjacencia
A = nx.adjacency_matrix(G)
A = A.toarray()

delta = np.zeros((36,36),dtype='f')
for i in xrange(0,36):
	if G.out_degree(i+1) != 0:
		delta[i,i] = 1.0/G.out_degree(i+1)
#Multiplica delta por A, encontrando assim a matriz de adjacencia
P = np.dot(delta, A)

######################################################################################
#Calculando a distribuicao estacionaria pelo power method (k = 100)
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

```markdown 
##########################################################################
#Importa as bibliotecas necessarias
import networkx as nx
import matplotlib.pyplot as plt
import numpy as np


#########################################################################
#Algortimo de Prim, retorn a MST
def prim(G, r):
	LAMBDA = []
	PI = []
	Q1 = {}
	Q2 = G.nodes()
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
		N = G.neighbors(q)
		for v in N:
			if v in Q1 and LAMBDA[Q2.index(v)] > G[q][v]['weight']:
				LAMBDA[Q2.index(v)] = G[q][v]['weight']
				Q1[v] = G[q][v]['weight']
				PI[Q2.index(v)] = q

	H = nx.Graph()
	for v in Q2:
		H.add_node(v)
	for i in range(0,len(Q2)):
		if PI[i] != None:
			H.add_edge(PI[i],Q2[i],weight=LAMBDA[i])

	return H

	


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
G.add_edge("A","B",weight=4)
G.add_edge("B","C",weight=8)
G.add_edge("C","D",weight=7)
G.add_edge("D","E",weight=9)
G.add_edge("E","F",weight=10)
G.add_edge("F","G",weight=2)
G.add_edge("H","I",weight=7)
G.add_edge("I","C",weight=2)
G.add_edge("H","A",weight=8)
G.add_edge("B","H",weight=11)
G.add_edge("D","F",weight=14)
G.add_edge("I","G",weight=6)
G.add_edge("F","C",weight=4)
G.add_edge("H","G",weight=1)

#desenha o grafo inicial
pos=nx.spring_layout(G)
nx.draw_networkx_nodes(G,pos)
nx.draw_networkx_edges(G,pos)
plt.savefig("Graph1.png", format="PNG")
plt.show()

#chama o algoritmo de prim para encontrar uma mst
H = prim(G,"A")
nx.write_gml(H, "teste.gml")

#desenha a mst obtida
pos=nx.spring_layout(H)
nx.draw_networkx_nodes(H,pos)
nx.draw_networkx_edges(H,pos)
plt.savefig("MST1.png", format="PNG")
plt.show()
```
