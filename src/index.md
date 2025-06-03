
# Algoritimo Knuth-Morris-Pratt


## O Problema da Busca por Padrões Textuais 


Em um mundo ditado por grandes volumes de dados, a capacidade de encontrar informações em grandes textos de forma rápida e acurada 
se torna uma habilidade algorítmica cada vez mais necessária, seja para resolver problemas da computação ou para lidar com desafios 
relacionados à biotecnologia e à segurança da informação.

Esse tipo de abordagem pode abranger desde problemas muito comuns — como contar o número de vezes que determinado termo aparece em 
um texto — até desafios mais complexos, como encontrar características únicas dentro de uma sequência de DNA.

À primeira vista, esse problema pode parecer trivial, especialmente quando pensamos em contextos simples e cotidianos. No entanto, 
recentemente, a busca por padrões foi aplicada em um feito antes inimaginável: a recriação dos lobos terríveis, realizada pela empresa 
Colossal Biosciences, por meio de uma tecnologia de bioengenharia avançada chamada CRISPR.

![](Direwolf.jpeg)


O método utilizado consistiu em edição genômica — ou seja, a identificação de fragmentos específicos na cadeia de DNA de uma espécie e 
a alteração de determinadas bases por outras possíveis, como substituir um “A” por um “T” ou um “C” por um “G”.No caso dos lobos terríveis, 
o DNA de lobos comuns foi modificado com base em informações obtidas de um fóssil preservado. Com apenas 14 alterações em 20 genes, os 
cientistas conseguiram reconstruir uma sequência genética idêntica à da espécie extinta.



## Solucionando o problema: Os Algoritimos de Knuth-Morris-Pratt e o algoritimo ingênuo


Para encontrar essas sequências específicas no DNA, é necessário utilizar algoritmos de busca por padrões. O mais simples deles é o algoritmo 
ingênuo, que compara o padrão desejado com cada parte do texto (ou DNA), letra por letra, até encontrar uma correspondência. Já o algoritmo 
Knuth-Morris-Pratt (KMP) melhora esse processo ao evitar comparações repetidas, usando informações do próprio padrão para avançar mais 
rapidamente no texto. Enquanto o método ingênuo pode ser lento em casos grandes, o KMP é muito mais eficiente, especialmente quando lidamos 
com sequências longas e repetitivas — como ocorre em genomas.

Dessa forma, precisamos entender o princípio por trás desses algoritimos para solucionarmos o problema,e, por isso, começaremos com a força
bruta do algoritimo ingênuo.

## Aprofundando no Algoritimo ingênuo


Tendo visto o problema como um todo, vamos entender o princípio básico de busca por padrões, a **"força bruta"**.
Pensemos, primeiro, no princípio mais alto nível de busca por padrões. 

??? Testando para ver se você está acordado

Dado que você quer contar quantas vezes aparece uma sílaba em um texto quaisquer, de quais informações você precisa para realizar a busca? 




::: Gabarito


A Resposta é óbvia: Precisamos apenas do **texto e do padrão buscado**. No caso do algoritimo em C, ele precisa de uma informação a mais: **O Tamanho do texto e do padrão textual buscado**



:::

???


Logo, iniciando a contrução do código em C, começamos com algo como:

``` c

void algoritimo_ingenuo(char string[], char substring[], int n, int m){
    // ???
}


```

??? Exercício Conceitual 1

Antes de inicarmos a construção do loop do código, precisamos exercitar o nosso pensamento. Dessa forma, dado as seguintes sequências de caracteres, identifique o padrão sequencial presente: 

**1.AAABBBAAABBB** <br>
**2.ABBAABBA** <br>
**3.XYYXYYYXYYY** <br> 


::: Gabarito

**Resposta 1:** AAABBB<br> 
**Resposta 2:** AAABBB<br>
**Resposta 3:** XYYY<br>

:::

???


Mas o nosso objetivo aqui obviamente não é te fazer saber indentificar padrões, na verdade, é pensar em como traduzir esse 
raciocínio para o algoritimo. 

??? Exercício Conceitual 2

Dessa forma, além de pensar no padrão, quero que pense: A partir de qual linha de raciocínio podemos achar padrões da maneira 
mais acurada e demorada? 


**A)** Observar apenas se os primeiros e últimos caracteres da sequência se repetem, como em ACBA <br>
**B)** Contar quantas vezes cada letra aparece e escolher a que aparece mais <br>
**C)** Testar todas as formas possíveis de dividir a sequência em blocos e ver quais se repetem ao longo dela <br>
**D)** Olhar apenas pares de letras vizinhas, como AB, BC, CD, e tentar construir o padrão a partir deles <br>


::: Gabarito

**C)** Testar todas as formas possíveis de dividir a sequência em blocos e ver quais se repetem ao longo dela <br> 

:::

???

O Algoritimo ingênuo atua justamente nessa abordagem **força bruta**, o qual ele irá: 
- Percorrer todo o texto dado; 
- Comparar o caractere percorrido com o primeiro caractere da substring;
- Caso os caracteres sejam iguais, compara o segundo caractere da substring com o caractere percorrido, e assim sucessivamente;
- Caso o padrão da substring seja interrompido, volta ao caractere percorrido antes de encontrar alguma string da sequência. 

A Partir dessa estrutura, temos um pensamento em alto nível parecido com: 

``` c

void algoritimo_ingenuo(char string[], char substring[], int n, int m){
    // para cada i em (0, 1, 2, ..., n - m)
    //     verifica se a substring ocorre a partir da posição i
    //     para cada j em (0, 1, 2, ..., m - 1)
    //         se string[i + j] ≠ substring[j]
    //             interrompe a verificação (não é uma ocorrência)
    //     se todos os caracteres da substring foram verificados com sucesso
    //         registra a posição i como ocorrência do padrão
}


```

??? Exercício Conceitual 3

Beleza, prepare o papel e o lápis e vamos passar por uma tarefa um pouco mais trabalhosa. Dado a seguinte sequência **AABABBABA**, e o padrão desejado **BBA**,
simule os valores de i e j para cada uma das iterações do **loop interno**.

::: Gabarito

```plaintext
1:   0 0 
2:   1 0
3:   2 0
4:   2 1
5:   3 0
6:   4 0
7:   4 1
8:   4 2
9:   5 1
10:  6 0  
```
Perceba que, como a nossa sequência tem 3 caracteres, e, quando chegamos em i = 2 = m - 1, encontramos a única presença do padrão nesse trecho!

:::

???


Ao ilustrar essas iterações, seria algo como: 

:ingenuo_01


Ok, já vimos que esse algoritimo entrega acurácia, mesmo que com construções demoradas, mas então, qual o problema dele? 


??? Exercício Conceitual 4

A melhor forma de explicar isso é elevando o nível de  dificuldade. Dessa forma, dado a sequência **AAAABAAAAAABBAAB** e o padrão desejado **AAABA**, estime apenas o 
**número de iterações do loop interno**

::: Gabarito

Nessa situação teríamos **38 iterações** do loop interno, isso para um sequência de apenas 16 caracteres, o que já resultaria em um loop interno mais extenso.

:ingenuo_02

:::






??? 

Se para uma sequência de poucos caracteres já parece exaustivo, imagine para casos mais complexos, como sequências de DNA, por exemplo! Isso nos leva para o principal 
problema do algoritimo: sua complexidade no tempo. O que o torna uma alternativa pouco versátil para soluções mais robustas. 

??? Exercício


Para concretizar essa linha de pensamento, a partir da descrição em alto nível, qual a complexidade do algoritimo ingênuo? 

``` c

void algoritimo_ingenuo(char string[], char substring[], int n, int m){
    // para cada i em (0, 1, 2, ..., n - m)
    //     verifica se a substring ocorre a partir da posição i
    //     para cada j em (0, 1, 2, ..., m - 1)
    //         se string[i + j] ≠ substring[j]
    //             interrompe a verificação (não é uma ocorrência)
    //     se todos os caracteres da substring foram verificados com sucesso
    //         registra a posição i como ocorrência do padrão
}


```

::: Gabarito

Como o algoritmo ingênuo compara cada posição da string principal, e em cada posição tenta casar a substring. Se a string principal tem tamanho **n** e a substring tem tamanho **m**, a complexidade no pior caso é **O(n*m)**.



:::
???



Percebe-se então, um problema bem aparente neste algoritimo, a sua redundância nas comparações. Quando uma incompatibilidade (mismatch) ocorre, o algoritmo simplesmente avança para a próxima posição na string principal e reinicia a comparação da substring do início, revisitando caracteres que já foram analisados.

Aqui esta mais uma animação para reforçar mais ainda esse defeito:

:imgs-kmp-ingenuo-1

Bom, dessa forma, fica claro que existe um problema de eficiência neste algoritimo, o que nos faz pensar, como será que podemos melhorar para que ele seja menos redundante?

##  O KMP

Bom, já ficou bem claro que o problema do Ingênuo é que ele demora porque sempre precisa ficar voltando quando da mismatch dentro da mesma string. É exatamente nesse ponto em que o KMP entra: como podemos fazer o algoritmo não ficar voltando quando não precisa? (dica: a resposta é bem simples)

??? Exercicio

Como podemos fazer o algoritmo não perder progresso?

**A)** Pulando os caracteres que o algoritmo já sabe que estão corretos, usando informações do padrão.

**B)** Repetindo a comparação desde o início do padrão sempre que houver erro, garantindo que nenhum caractere seja ignorado.

**C)** Adicionando caracteres aleatórios ao padrão para tentar encontrar correspondências mais rapidamente.

**D)**  Ignorando todos os prefixos do padrão após um erro e iniciando a busca diretamente do próximo caractere do texto.

::: Gabarito
**A)** Pulando os caracteres que o algoritmo já sabe que estão corretos, usando informações do padrão
:::

???

Para ficar um pouco mais fácil de visualizar esse defeito:

:imgs-kmp-ingenuo-1

Agora ficou bem claro qual é a primeira etapa do KMP: pular "casas" quando ele sabe que não vai ocorrer match.

Como superar as limitações do algoritmo ingênuo? A ideia central do KMP é pré-processar o padrão para criar uma estrutura que permita "pular" comparações redundantes, otimizando a busca pelo padrão ao longo da string .

Agora vamos olhar para uma versão de alto nível do código do KMP:

``` c

void kmp(char string[],int n, char substring[], int m){
    enquanto i for menor que n, continue o processamento {
        se string[i] for igual a substring[j]{
            avance contador i e j
        }
        se j for igual a m{
            sabemos que o padrao foi encontrado porque o tamanho maximo da substring foi atingido
            zere j para continuar a busca
        }
        se i for menor que n e string[i] for diferente de substring[j]{
            se j for igual a 0{
                avanca i mantendo j em 0
            }caso contrario,{
                reinicia j, mas mantem o valor de i para nao perder o progresso
            }
        }
    }
}
            
```



??? Exercício


A partir dessa descricao de alto nivel do KMP, tente escrever como seu codigo em C ficaria



``` c

void kmp(char string[], int n, char substring[], int m){

    ...
    
}
        
```



::: Gabarito


``` c

void kmp(char* string, int n, char* substring, int m) {
    // passo 1: inicialize os indices
    int i = 0; // indice para o string
    int j = 0; // indice para a substring
    
    // passo 2: inicie um laco na string
    while (i < n) {
        // passo 3: compare caracteres
        if (string[i] == substring[j]) {
            // passo 4: se coincidem
            i++;
            j++;
        }
        
        // passo 5: se o padrao foi encontrado
        if (j == m) {
            printf("padrao encontrado na posicao %d\n", i - j);
            j = 0; // reinicia j para buscar outras ocorrencias
        }
        // passo 6: se ha um mismatch
        else if (i < n && string[i] != substring[j]) {
            // passo 6a: se j eh zero
            if (j == 0) {
                i++; // avanca i, mantendo j em 0
            }
            // passo 6b: Se j eh maior que zero
            else {
                j = 0; // reinicia j, mas mantem i
            }
        }
    }
}
        
```


:::
???


Essa abordagem é mais eficiente que a busca ingênua, pois evita retroceder no texto, mas ainda realiza 
 comparações redundantes, já que reinicia j para 0 em cada mismatch. Sua complexidade no pior caso pode 
 se aproximar de O(n * m), onde n é o tamanho do texto e m é o tamanho do padrão.


## Retirando a Redundância: O Vetor de LPS


O vetor Longest Proper Prefix which is also Suffix (LPS) serve é o Coração do algoritimo KMP. A Abordagem que ele adota de indentificar
 o padrão é, como o nome já sugere, entender até que ponto do comprimento o prefixo é igual ao sufixo de uma string. Essa padronização é 
 utilizada para direcionar a eficiência do algoritimo que, ao invés de retroceder, agora, só volta até a parte onde ainda não temos padrões.
 Mas como eu vou entender padrões comparando prefixos com sufixos? É aí que a mágica vem... 
 




??? Exercício


Suponhas as seguintes sequências de caracteres


1.ABCABCABCABC


2.ABABACABABACABABA


3.ABCABDABCABEABCABDABCABEABCABD


Qual o Padrão textual presente em cada uma delas?




::: Gabarito

1.ABC - Note que aqui a sequência é simples, só temos que ver quando o caractere A se repete novamente


2.ABABA - Aqui, já vemos um grau de ruptura, com a letra "c" entre alguns padrões


3.ABCABDABCABE - Já temos padrões grandes e difíceis de se indentificar visualmente, com grandes quebras claras



:::
???

Quando tentamos encontrar padrões em uma sequência, o nosso pensamento segue três etapas naturais. Primeiro, reconhecemos pequenas repetições locais, comparando trechos que já vimos com o que estamos lendo agora. Depois, procuramos uma regularidade, tentando identificar se essas repetições seguem um tamanho ou ritmo constante. Por fim, condensamos essas repetições em uma ideia única para economizar esforço, enxergando, por exemplo, "ABC" repetido, em vez de cada letra separada. Esse raciocínio leva naturalmente a buscar partes do início (prefixos) que também aparecem no final (sufixos), pois eles já foram confirmados e podem ser reutilizados.Logo, o LPS sintetiza esse raciocínio em uma lógica intuitiva.
 



``` c

void lps(char padrao[], int m, int* lps){
                
    inicie o comprimento do maior prefixo próprio e sufixo em 0, ou seja, aqui você está olhando para o maior padrão
                
    defina lps[0] como 0, porque um único caractere não tem prefixo nem sufixo
            
    inicie o contador i em 1
            
        enquanto i for menor que m, continue o processamento {
            se padrao[i] for igual a padrao[comprimento] {
                incremente o comprimento
                atribua lps[i] como o novo comprimento
                avance i
            }
            caso contrário {
                se comprimento for diferente de 0 {
                    atualize o comprimento para o valor de lps[comprimento - 1]
                    não avance i ainda, pois vamos tentar casar um prefixo menor
                }
                se comprimento for igual a 0 {
                    defina lps[i] como 0
                    avance i
                }
            }
        }
    }
            
```



??? Exercício


A partir dessa descricao de alto nivel do LPS, tente escrever como seu codigo em C ficaria



``` c

        void lps(char padrao[], int m, int* lps){
            
            ...

        }
        
```



::: Gabarito


``` c

void lps(char padrao[], int m, int lps[]){
    
    // Passo 1: Inicializa comprimento como 0 e o primeiro caractere do vetor também, já que ele nao tem prefixo e nem sufixo
    int comprimento = 0;
    lps[0] = 0;

    // Passo 3: Começa a análise do segundo caractere
    int i = 1;

    // Passo 4: Percorre o padrão até o final
    while (i < m) {

        // Passo 5: Se os caracteres combinam, atualiza comprimento e LPS
        
        if (padrao[i] == padrao[comprimento]) {
            comprimento++;
            lps[i] = comprimento;
            i++;
        }

        // Passo 6: Se os caracteres não combinam
        else {
            if (comprimento != 0) {
                comprimento = lps[comprimento - 1];
            } else {
                lps[i] = 0;
                i++;
            }
        }
    }
}
        
```
:::
???
## Otimizando o KMP

Utilizando o LPS como uma função auxiliar para o KMP, podemos otimizar ele muito, de maneira a reduzir a redundância ao extremo. Vamos olhar como fica o código do KMP agora:

``` c

void kmp(char string[], char substring[], int n, int m) {
    int lps[m];
    lps(substring, m, lps);

    int i = 0; 
    int j = 0;

    while (i < n) {
        if (string[i] == substring[j]) {
            i++;
            j++;
        }
        if (j == m) {
            printf("Padrão encontrado na posição %d\n", i - j);
            j = lps[j - 1]; 
        } else if (i < n && string[i] != substring[j]) {
            if (j != 0) {
                j = lps[j - 1];
            } else {
                i++;
            }
        }
    }
}
        
```


??? Exercício


A partir do código fornecido, tente estimar a nova complexidade do algoritmo KMP.



::: Gabarito

Agora que estamos usando o lps, o KMP alcança uma complexidade de \(O(n + m)\). Isso ocorre porque a construção do vetor LPS, que pré-processa o padrão, é realizada em \(O(m)\), e a busca na string principal é feita em \(O(n)\), sem retrocessos desnecessários.

:::
???

Vamos olhar agora para uma animação, demonstrando o funcionamento desta versão do KMP:

:KMP