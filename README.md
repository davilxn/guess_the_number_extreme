# guess_the_number_extreme
  Este trabalho foi realizado como projeto da etapa final da disciplina de Circuitos Digitais, focada em circuitos sequenciais, ministrada pelo Prof. Dr. Ramon Santos Nepomuceno (ramon.nepomuceno@ufca.edu.br).
  Trabalho desenvolvido pelos estudantes: Davi Santos Alexandrino e Leonardo Pereira Silva.
## Introdução
  O projeto consiste em um jogo, onde dois jogadores devem chutar valores para duas coordenadas x e y que podem variar de -8 a 7, coordenadas essas selecionadas previa e aleatoriamente dentro do circuito. Os jogadores jogam sob a temporização de um cronômetro, que funciona semelhantemente aos relógios de partidas de xadrez, e pode ser ajustado para qualquer intrevalo de tempo inferior a 1 hora, com a diferença de que, caso o tempo de um jogador acabe, o outro ainda pode continuar jogando. A cada chute de um jogador, lhe é informado, como métrica de direcionamento, se a soma das coordenadas x e y chutadas pelo jogador é maior, menor ou igual à soma das coordenadas x e y secretas. 
Se as coordenadas x e y chutadas por um jogador forem iguais às coordenadas secretas, o jogador em questão terá marcado um ponto, que será registrado no placar automaticamente. 
Vence o jogador que atingir mais pontos no intervalo de tempo determinado pelo cronômetro, ou que atingir 15 pontos primeiro.

![main](images/main.png)

## Comparador
  Este circuito recebe 8 entradas, 4 representam um valor chutado por um dos jogadores e 4 representam um número secreto. Para cada uma das saidas temos uma configuração:
    a) Igualdade: quatro portas XNOR com duas entradas, cada uma recebendo um dos bits do valor chutado pelo jogador e um dos bits do número secreto, sendo esses bits de igual significância. Dessa forma, caso sejam iguais, a saida será verdadeira. Após isso há uma AND para as quatro portas XNOR.
    b) Maior: para comparar a magnitude, observa-se os valores dos bits do número chutado pelo jogador e os do número secreto, desde o de maior até o de menor significância, considerando a > b se e somente se a = 1 e b = 0. Para o de menor significancia ainda é necessario que se adicione o fio correspondente do circuito de igualdade das outras magnitudes (mostrando que as outras casas são iguais e comparar apenas a de menor significancia) dentro da porta AND.
    c) Menor: após verificar as demais condições, se estas forem falsa conclui-se que o palpite é menor do que o "numero secreto".

![Comparador](images/Comparador.png)

## Contador SM - Segundos e Minutos
  O contador SM tem como base um registrador e um subtrator, que decrementa em uma unidade o valor que que recebe em suas entradas, variando de 9 a 0, com os pulsos do clock. Se o botão "configurar" estiver acionado, os pulsos do clock não são recebidos pelo registrador, mas sim os sinais enviados pela botão US (se for o contador dos minutos, UM), possibilitando que os jogadores configurem seus cronômetros antes da partida iniciar (a configuração do cronômetro pelo jogador, através do botão configurar e UM não aciona a saída "clock próximo", comentada a seguir). O circuito subtrator, além das 4 saídas que representam o número decrementado, possui uma saída extra, "clock próx", que só é ativada quando registrador chega ao valor 0 e retorna para o 9. Essa saída também está presente no contador DSM e foram criada para serem usadas como pulsos de clock para os contadores subsequentes. Existe ainda um circuito "verificador-FFP", usado para garantir que a saída "clock próximo" só seja ativada nas condições idealizadas, não podendo ser acionada por nenhuma outra interação.

![ContadorDM](images/ContadorDM.png)

## Contador DSM - Dezenas de segundos e de minutos
  Praticamente igual ao contador SM, sendo a única diferença o subtrator utilizado, que só varia de 5 a 0.

![ContadorDSM](images/ContadorDSM.png)

## Mostrador
  Este circuito é a utilização dos contadores para montar o cronômetro de um jogador. Quando todos os contadores atingem o valor 0 simultaneamente, uma saída Z é acionada, utilizada para impossibilitar a passagem do clock para os contadores, parando a contagem de tempo. Após isso, para reiniciar o cronômetro, deve ser apertado o botão "clear" que, no circuito principal do jogo, é o botão "reset".

![Mostrador](images/Mostrador.png)

## Converte
  Transforma um número maior ou igual a 8 em seu equivalente negativo, baseado na representação de números binários negativos a partir do complemento de 2.

![Converte](images/Converte.png)

## Somador(-8:7)
  Faz a soma de dois números de 4 bits quaisquer positivos ou negativos, considerando a conversão realizada pelo circuito "Converte", descrito acima.

## Seleção
  Esse circuito representa toda a interação dos jogadores com o jogo. O raciocínio fundamental é uma máquina de estados de 8 estados, onde 2 são descartados. Os dois primeiros estados representam os chutes de x e de y pelo jogador A e o terceiro representa a verificação dos chutes do jogador A, sendo análogo para os próximos 3 estados (ao fim do sexto estado, retorna-se para o primeiro). A mudança de estados acontece através do botão "Próximo", cujo sinal é recebido pelo circuito "Cont-estados", responsável pela variação de um estado para outro de maneira cíclica. Dentro do circuito "Cont-estados", existe o circuito "maisEstados", que, além das 3 variáveis responsáveis por representar o estado atual e próximo da máquina de estados, recebe mais duas entradas que representam o estado de funcionamento dos cronômetros do jogador A e B (se ainda estão contando ou já zeraram). Esse circuito força a exclusão de estados da máquina de estados, dependendo dos estados dos cronômetros. Exemplo: Se o cronômetro do jogador A estiver zerado, os 3 estados da máquina de estados referentes ao jogador A (2 de chute e 1 de verificação de chute) são "excluídos" e a máquina de estados entra em ciclo somente com os estados referentes ao jogador B.
  As entradas de 4 bits que representam os chutes dos jogadores entram em um multiplexador, que selecionará qual das delas será considerada. Essa seleção ocorre a partir do estado atual da máquina de estados, isto é: nos dois primeiros, a entrada considerada é a do jogador A, enquanto no quarto e quinto estados, a entrada considerada é a do jogador B (no terceiro e sexto estados, nenhuma das entradas é considerada, pois há a verificação de um chute). Após isso, a entrada selecionada é direcionada para um demultiplexador, que a envia para uma das quatro possíveis saídas (registradores): x do jogador A, y do jogador A, x do jogador B e y do jogador B. A saída para qual essa entrada é direcionada é, por sua vez, selecionada também a partir do estado atual da máquina de estados: se o estado atual for o primeiro, a entrada é direcionada para o registrador que recebe o x do jogador A, se for o segundo, então vai para o y do jogador A. Analogamente para os estados 4 e 5. Essa processo representa o chute das duas coordenadas do jogador da vez.
  As quatro saídas do demultiplexador descrito acima entram em dois multiplexadores: um deles recebe o x do jogador A e o x do jogador B e o outro, o y de A e o y de B. A partir do estado atual da máquina de estados, serão selecionados o x e o y do jogador da vez, que entrarão no circuito "Somador(-8:7)" visto anteriormente, que realizará a soma das duas coordenadas e enviará o resultado para o circuito "compara" que utiliza dentro de si o circuito "Comparador" também visto anteriormente, mas que além disso, faz a verificação dos sinais dos números comparados. O x e o y do jogador da vez também são enviados para outros dois circuitos "compara" que, nesse caso específico, só terão como saída a verificação de igualdade, com o objetivo de descobrir se as coordenadas chutadas pelo jogador são iguais às coordenadas secretas estabelecidas previamente no circuito. Caso sejam, é acionada a saída "acertou", que é responsável por enviar o pulso que gera novas coordenadas a serem chutadas e que incrementa o placar do jogador que acertou o chute.

![Seleçãopt1](images/Seleçãopt1.png)
![Seleçãopt2](images/Seleçãopt2.png)

# Outros
  O restante dos circuitos envolvidos no projeto são circuitos combinacionais acessórios com funções simples e, portanto, optamos por omitir a documentação pormenorizada desses circuitos, uma vez que suas operações são autoexplicativas e não requerem análises extensivas.
  
  
