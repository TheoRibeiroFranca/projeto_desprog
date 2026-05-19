O Algoritmo de Bentley-Ottmann
==============================

Detectando interseções entre segmentos de reta
----------------------------------------------

 O objetivo dessa aula será apresentar um novo algoritmo, dessa vez voltado para resolver problemas geométricos em larga escala.

Imagine um conjunto de segmentos de reta no plano, e que nosso objetivo é encontrar todos os pontos onde eles se cruzam. Esse problema aparece no projeto de circuitos impressos (para detectar trilhas que se tocam indevidamente numa PCB) e em computação gráfica (para calcular sobreposição entre polígonos).

A solução ingênua é testar todos os pares de segmentos independentemente de quantas interseções existem de fato. O algoritmo de Bentley-Ottmann surge para resolver o mesmo problema de forma mais eficaz. A técnica que ele usa, a **varredura linear**, é um dos pilares da geometria computacional e será aprofundada na aula de hoje.

Neste handout, vamos construir esse algoritmo do zero.


## O problema

Formalmente:

Antes de pensar nos algoritmos, precisamos definir claramente o problema com base em suas entradas e saídas:

* **Entrada:** Um conjunto de $n$ segmentos de reta, onde cada segmento é determinado pelas coordenadas de seus dois extremos: $(x_1, y_1)$ e $(x_2, y_2)$.
* **Saída:** A quantidade $k$ de interseções encontradas e, para cada uma delas, as coordenadas $(x, y)$ do ponto de cruzamento.

!!! Definição
Dados $n$ segmentos de reta no plano, encontrar todos os pontos onde dois ou mais desses segmentos se cruzam.
!!!

Chamamos de $k$ o número de interseções encontradas.

??? Atividade 1

Quais são os valores mínimo e máximo possíveis de $k$, em função de $n$?

::: Gabarito
O mínimo é $k = 0$, quando nenhum par se cruza.

O máximo acontece quando todo par de segmentos se cruza. Um exemplo: $n/2$ segmentos quase-horizontais e $n/2$ quase-verticais, todos passando por uma mesma região. Cada horizontal cruza cada vertical uma vez, dando $(n/2) \times (n/2) = n^2/4$ interseções. Repare que horizontais entre si não se cruzam (são quase paralelos), e verticais entre si também não.

O ponto é que $k$ varia muito: de zero até a ordem de $n^2$. O tamanho da saída não é fixo em função do tamanho da entrada.
:::

???



## A abordagem ingênua

??? Atividade 2

Considere que o algoritmo ingênuo precisa testar todos os pares possíveis de segmentos. Se denotarmos o número total de pares únicos como uma combinação de $n$ elementos tomados de 2 em 2, como podemos expressar analiticamente a quantidade total de testes realizados em função de $n$? Demonstre como isso nos leva à complexidade de tempo final.

::: Gabarito
Para encontrar o número total de pares únicos, utilizamos a combinação simples:

$$\binom{n}{2} = \frac{n(n-1)}{2} = \frac{n^2 - n}{2}$$

Para valores muito grandes de $n$, o termo $n^2$ domina a expressão. Assim, o número de pares únicos é aproximadamente $n^2/2$.

Como cada teste individual de interseção entre dois segmentos é feito em tempo constante, ou seja, $O(1)$, multiplicamos o número de testes pelo custo de cada um:

$$\text{Tempo Total} = \frac{n^2 - n}{2} \times O(1) = O(n^2)$$

Portanto, a complexidade de tempo total da abordagem ingênua é $O(n^2)$.
:::

???

![](demonstracao-ingenuo.png)

??? Atividade 3

Observando a imagem, com aproximadamente 35 trilhas e poucas interseções visíveis, por que podemos afirmar que o método ingênuo é ineficiente para esse caso?

**Dica**: quantos pares de trilhas o algoritmo precisa testar no total? Desses pares, quantos de fato se cruzam?

::: Gabarito
Com $n \approx 35$ trilhas, o método ingênuo testa todos os pares possíveis, ou seja, $\binom{35}{2} = 595$ testes. Olhando a imagem, porém, vemos apenas algumas poucas interseções reais. A grande maioria dos testes é desperdiçada em pares de trilhas que estão em regiões completamente diferentes da placa e que obviamente não se cruzam.

Agora imagine essa mesma situação em uma escala muito maior: uma placa com centenas de milhares de trilhas, espaçadas de forma tão densa que nem conseguiríamos identificar todas as interseções a olho nu. O número de testes do método ingênuo cresce com $n^2$, enquanto o número de interseções reais tende a crescer de forma muito mais lenta. A desproporção entre testes feitos e testes úteis fica cada vez pior.

Um olho humano descartaria de longe os pares que estão em regiões distantes, sem fazer conta nenhuma. A pergunta natural é: como um algoritmo pode fazer algo parecido, ou seja, evitar testar pares que claramente não se cruzam?
:::

???


## A ideia da varredura linear

Para fazer melhor que $O(n^2)$, precisamos de uma forma de **evitar testar pares que claramente não se cruzam**. A varredura linear faz isso imaginando uma linha vertical que percorre o plano da esquerda para a direita. Em cada instante, essa linha, chamada **sweep line**, intersecta algum subconjunto dos segmentos, e podemos listá-los de cima para baixo. Essa é a **ordem vertical** dos segmentos naquele instante.

:demonstracao-alto

??? Atividade 4

Olhe para a animação acima e observe os pontos onde segmentos se cruzam. O que os segmentos envolvidos em cada cruzamento têm em comum na ordem vertical da sweep line, um pouco antes do cruzamento acontecer?

**Dica**: analise pelo menos dois cruzamentos diferentes e procure por um padrão que se repita.

::: Gabarito
Em todo cruzamento, imediatamente antes dele, os dois segmentos envolvidos estão **adjacentes** na ordem vertical da sweep line. Nenhum outro segmento está entre eles. O padrão se repete para qualquer cruzamento que você analise.
:::

???

??? Atividade 5

O padrão que você observou na atividade anterior não é coincidência. Tente justificar por que ele sempre acontece.

**Dica**: pense no que acontece com a posição relativa de dois segmentos antes e depois de eles se cruzarem.

::: Gabarito
Dois segmentos que se cruzam trocam de posição na ordem vertical: antes do cruzamento, um está acima do outro; depois, a posição se inverte.

Suponha que haja um terceiro segmento $c$ entre $a$ e $b$ no instante anterior ao cruzamento. A ordem seria $a, c, b$. Para $a$ e $b$ trocarem, $a$ teria que ultrapassar $c$ primeiro, ou seja, cruzar $c$ antes. Mas aí o cruzamento de $a$ e $c$ viria primeiro na varredura, contradizendo a suposição.

Logo, imediatamente antes de um cruzamento, os dois segmentos envolvidos precisam ser vizinhos.
:::

???

Essa é a observação central do algoritmo: **só precisamos testar pares que se tornem vizinhos em algum momento da varredura**.

:slides

Resta um problema prático: uma sweep line contínua é uma ficção. Computadores são discretos.

??? Atividade 6

A ordem vertical dos segmentos na sweep line muda ao longo do tempo, mas ela não muda o tempo todo. Há instantes específicos em que a ordem se altera. Tente identificar que tipos de acontecimento fazem a ordem mudar.

**Dica**: Olhando a animação anterior, pense em três situações distintas em que a fila da ordem vertical realmente muda.

::: Gabarito
A ordem muda em três tipos de acontecimento, que chamaremos de **eventos**:

1. **Extremo esquerdo**: a sweep line atinge o extremo esquerdo de um segmento, e ele entra na ordem.

2. **Extremo direito**: a sweep line atinge o extremo direito de um segmento, e ele sai da ordem.

3. **Interseção**: a sweep line passa por um ponto onde dois segmentos se cruzam, e eles trocam de posição na ordem.

Entre dois eventos consecutivos, a ordem não muda. Basta pular de evento em evento, em vez de varrer continuamente.
:::

???


## Juntando tudo: a estratégia do algoritmo

Com as atividades anteriores, a estratégia fica clara:

1. Mantemos uma lista de **eventos futuros**, inicialmente com os $2n$ extremos dos segmentos.

2. Processamos os eventos em ordem da esquerda para a direita.

3. Em cada evento, atualizamos a ordem vertical dos segmentos na sweep line.

4. Sempre que dois segmentos se tornam vizinhos, testamos se eles se cruzam. Se sim, inserimos o cruzamento como um novo evento futuro.

5. Quando chegamos a um evento de interseção, reportamos o ponto e trocamos a posição dos dois segmentos na ordem.

Note que os eventos de interseção não são conhecidos desde o início: eles são **descobertos durante a varredura**, conforme vizinhanças vão surgindo. A lista de eventos cresce dinamicamente como podemos ver a seguir.

:simulacao-exata

??? Atividade 7

Por que é seguro detectar interseções apenas quando segmentos se tornam vizinhos? Como temos certeza de que nenhum cruzamento é perdido?

::: Gabarito
Pela Atividade 5, todo cruzamento é precedido por um momento de vizinhança entre os dois segmentos envolvidos. Como o algoritmo testa cruzamento sempre que uma vizinhança surge, todo cruzamento real será descoberto antes de a sweep line chegar nele.
:::

???


## A eficiência do algoritmo

De onde vem o $O((n + k) \log n)$? Precisamos de duas coisas: quantos eventos o algoritmo processa, e quanto custa processar cada um.

??? Atividade 8

Em função de $n$ e $k$, quantos eventos existem ao todo?

::: Gabarito
Cada segmento contribui com um extremo esquerdo e um direito, dando $2n$ eventos de extremo. Cada interseção gera um evento, dando $k$ eventos de interseção. Total: $2n + k$.
:::

???

Para processar um evento rapidamente, a ordem vertical é mantida em uma **árvore binária de busca balanceada**. Inserções, remoções e consultas aos vizinhos custam $O(\log n)$.

A lista de eventos futuros usa uma **fila de prioridade**, que entrega o próximo evento e permite inserir novos cruzamentos descobertos. As operações também custam $O(\log n)$.

??? Atividade 9

Qual é a complexidade total do algoritmo?

**Dica**: multiplique o número de eventos pelo custo por evento.

::: Gabarito
$$(2n + k) \times O(\log n) = O((n + k) \log n)$$
:::

???

![](pior-cenario.png)

??? Atividade 10

Em que situações o Bentley-Ottmann é realmente melhor que o ingênuo? Existe algum caso em que o ingênuo é competitivo?

**Dica**: compare as complexidades em dois extremos, $k = O(n)$ e $k = O(n^2)$ e pense sobre a imagem.

::: Gabarito
Com $k = O(n)$, o Bentley-Ottmann roda em $O(n \log n)$. Contra $O(n^2)$ do ingênuo, é uma melhoria gigante: para $n = 10\,000$, são $\sim 10^5$ operações contra $\sim 10^8$. Mil vezes mais rápido.

Com $k = O(n^2)$, o Bentley-Ottmann fica $O(n^2 \log n)$, um fator $\log n$ **pior** que o ingênuo.

Conclusão: o Bentley-Ottmann vence quando $k$ é pequeno em relação a $n^2$, o caso típico na prática. Quando quase todo par se cruza, o problema é inerentemente caro e o ingênuo é competitivo.
:::

???


## Resumo

1. **O problema**: dados $n$ segmentos de reta no plano, encontrar todos os pontos de interseção.

2. **Aplicações**: projeto de circuitos impressos (detecção de curtos) e computação gráfica (sobreposição de polígonos), entre outras.

3. **Por que esse algoritmo**: o ingênuo é $O(n^2)$ mesmo com poucas interseções; o Bentley-Ottmann é sensível a $k$, rodando em $O((n + k) \log n)$.

4. **Ideia central**: uma sweep line percorre o plano da esquerda para a direita. Só pares que se tornam vizinhos na ordem vertical podem se cruzar, então o algoritmo testa cruzamentos apenas entre vizinhos. A varredura salta entre eventos discretos: extremos de segmentos e interseções descobertas.

5. **Eficiência**: $2n + k$ eventos, cada um com complexidade $O(\log n)$, totalizando $O((n + k) \log n)$.