# Grafos na Prática

## Introdução

   O objetivo deste repositório é apresentar alguns conceitos básicos, assim como alguns dos algoritmos mais utilizados na área de Teoria dos Grafos. Através da biblioteca [NetworkX](http://networkx.readthedocs.io/en/stable/), da linguagem [Python](https://docs.python.org/3/), que possui ferramentas específicas para trabalhar com grafos e a modelagem de problemas através de grafos, serão apresentados alguns exemplos práticos selecionados, de soluções de problemas comuns em Teoria dos Grafos.

   Para aprofundar os estudos, e para futura referência, estão listados aqui alguns recursos didáticos em _Teoria dos Grafos_, _Python_ e _NetworkX_:

### Teoria dos Grafos

*
*
*
*

### O Jogo Snakes and Ladders

```markdown
######################################################################################
#Bibliotecas necessarias
import networkx as nx
import numpy as np
import matplotlib.pyplot as plt
import scipy

######################################################################################
#Criando o grafo que representa o tabuleiro do jogo

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

######################################################################################
#Desenhando o grafo
#posiciona o grafo conforme o tabuleiro
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

#Desenha os vertices
nx.draw_networkx_nodes(G, pos)
#Desenha as arestas
nx.draw_networkx_edges(G, pos, arrows=True)
#mostra e salva o desenho
plt.savefig("Graph.png", format="PNG")
plt.show()


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


