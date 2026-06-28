ROTEIRO DE ESTUDOS — PROGRAMAÇÃO

Roteiro estruturado para que o membro consiga contribuir tanto com o firmware dos microcontroladores quanto com o software de estratégia em Python.

## MÓDULO 1 — Fundamentos

*Objetivo: dominar as ferramentas e conceitos base usados no projeto.*

### Git e GitHub

- Diferença entre Git e GitHub.
- Comandos: git clone, add, commit, push, pull, branch, merge.
- Fluxo: branch para nova feature → commits atômicos → Pull Request.
- Resolução de conflitos de merge.
- Prática: fork do repositório NeonFC, clonar e fazer primeira contribuição.

Exercícios:

    1. Crie um repositório vazio (sem README) na sua conta pessoal do github

    2. Para todos os exercícios seguintes suba as soluções nesse repositório

    3. Adicione um arquivo README localmente e suba no remoto

### Programação Orientada a Objetos (Python)

- Conceitos: Classes, Objetos, Atributos, Métodos.
- Encapsulamento, Herança e Polimorfismo — focar em conceitos antes de código.
- Exercício prático: criar uma classe Robot com posição, velocidade e métodos de movimentação.
- Aprofundamento (opcional): Dunder Methods, Classes abstratas (ABC), SOLID Principles, Dataclasses.

#### Fontes para POO
- [Principais conceitos de POO](https://dev.to/luisdev07/understanding-object-oriented-programming-oop-a-comprehensive-guide-for-developers-2ch4) (focar mais em conceitos do que em código)
- [Guia detalhado de POO para Python](https://realpython.com/python3-object-oriented-programming/)
- [Videoaula para entendimento prático](https://www.youtube.com/watch?v=Ej_02ICOIgs)
- [Python online IDE](https://www.onlineide.pro/playground/python)
- [Classes abstratas](https://www.datacamp.com/tutorial/python-abstract-classes)
- [SOLID Principles](https://arjancodes.com/blog/solid-principles-in-python-programming/)
- [Data classes](https://blog.naveenpn.com/data-classes-in-python-the-ultimate-guide)

### Ambiente de Desenvolvimento

- Python 3.10+ e ambientes virtuais (venv).
- IDE recomendada: VSCode com extensão Python e Pylance.
- Arduino IDE: configurar para STM32 via stm32duino.
- Leitura obrigatória: Getting Started do NeonFC (link no repositório do time).

### Boas Práticas

#### Nomenclatura
Não temos padrões fixos para nomenclatura de funções e variáveis na Neon, busque manter o padrão utilizado no código/módulo que você estiver mexendo. Não fuja de nomes longos e leve em consideração o contexto que sua variável/função esta inserido.

Busque manter:
- variaveis: em lower case
- Funcoes: capitalizadas
- CONSTANTES: em uppercase

Na Neon buscamos sempre manter nosso código aberto e colaborar tanto com a comunidade nacional com internacional então mantenha nomenclatura e comentários em inglês.

#### Type Hinting
Em python você não é obrigado a declarar tipos, porém busque utilizar o type hints para facilitar que outras pessoas e as IDEs entendam o seu código. Ex:

```python
# -- Bad Implementation --------- #
def foo(bar):
	return 2*bar


# -- Good Implementation --------- #
def foo(bar: int) -> int:
	return 2*bar
```
#### Fluxo de desenvolvimento
Antes de adicionar uma nova função crie um documento curto de descrição dela. Esse documento não precisa seguir um padrão especifico. Um exemplo de como fazer um documento desses pode ser encontrado em [Exemplo 1](https://www.notion.so/DOC-Neon-FC-V2-88b70ed90c1a4693b60193efe26b779c?source=copy_link) ou [Exemplo 2](https://www.notion.so/Ball-Holder-1ee10d877f468028bddee9fe061411b1?source=copy_link).

Após terminar o design da sua feature peça para alguém revisar, síncrona ou assincronamente, antes de começar a implementar. Antes de inserir sua função junto com o resto do código, pode ser útil fazer uma PoC (proof of concept) em um ambiente separado, como um jupyter notebook.

Para implementar o código junto com o restante do codebase primeiro crie uma branch com o nome da feature que você vai implementar. Após terminar de implementar e testar abra um PR e peça para alguém revisar sua feature. Com a aprovação, faça o merge da sua branch, com ou sem squash dos commits, e depois delete a branch.

## MÓDULO 2 — Firmware e Robótica

*Objetivo: entender e contribuir com o firmware e o controle do robô.*

### Programação de Microcontroladores (C/C++)

- Diferença de programação em MCU: memória limitada, sem OS, loop infinito.
- Funções básicas Arduino: `setup()`, `loop()`, `pinMode()`, `digitalWrite()`, `analogWrite()`.
- Comunicação UART no STM32: `Serial.begin()`, `read()`, `write()`.
- Interrupções: o que são, quando usar, como configurar no STM32.
- Timers e PWM: como o STM32 gera sinais PWM para o driver DRV8313.

### SimpleFOClibrary — Estudo Aprofundado

- Ler documentação completa: docs.simplefoc.com.
- `BLDCMotor`: parâmetros de inicialização (pares de pólos, resistência, KV).
- `BLDCDriver3PWM`: mapeamento de pinos PWM do STM32.
- Modos de controle: `velocity_openloop`, `velocity` (closed-loop), `angle`, `torque`.
- Adicionar encoder: usar `MagneticSensorI2C` com MT6701CT.
- Tuning de PID: ajustar P, I, D para controle suave de velocidade.

### Cinemática Inversa — Implementação

- Implementar a matriz cinemática: dados \([v_x, v_y, \omega]\), calcular \([\omega_a, \omega_b, \omega_c, \omega_d]\).
- Usar `numpy.linalg.pinv` para a pseudo-inversa.
- Validar: simular no computador antes de rodar no hardware.
- Converter velocidades angulares de rad/s para os comandos SimpleFOC.
- Referência: seção 2.3 deste documento e tese RoboCIn.

## MÓDULO 3 - PARALELISMO E CONCORRÊNCIA

### Definição

Em ciência da computação paralelismo/concorrência é a área que estuda algoritmos que permitem executar o mesmo código mais de uma vez ao mesmo tempo, dois códigos diferentes ao mesmo tempo ou algoritmos para simular esse comportamento. 

Em um computador cada núcleo (core) da CPU consegue executar um comando de cada vez. CPUs modernas tem vários núcleos o que permite que programas diferentes rodem ao mesmo tempo, ex. usar o navegador enquanto o spotify toca musica.

Outra maneira de atingir o paralelismo é “simulando” esse comportamento. Durante a execução de um código é muito comum haver esperas por eventos de I/O, ex. o navegador esperando o conteúdo da pagina ser baixado pela internet, ou um programa esperando o usuário digitar algo. Nesses momentos, em que um programa espera um dispositivo de I/O, a CPU fica ociosa, logo um outro programa pode ser executado enquanto a operação de I/O não termina.

### Multitasking

A seção acima explica como diferentes programas podem rodar em paralelo, porem muitas vezes um mesmo programa pode querer rodar duas partes do seu código em paralelo. Ex. o spotify toca uma musica enquanto você busca por uma outra musica na interface. Ou um certo software de robótica calcula a velocidade de múltiplos robôs ao mesmo tempo.

O multitasking pode ser executado por meio de 3 ferramentas dependendo do sistema operacional e do compilador (da linguagem que você estiver usando). Multiprocessing, kernel multithreading, user multithreading. As diferenças entre esses tipos de ferramentas não importa muito por enquanto, mas é bom saber que elas são coisas diferentes.

    Importante: O uso de multitasking não vai sempre resultar em um aumento de performance, nem toda tarefa é paralelizável. Além disso ao executar código em paralelo você adiciona um nível a mais de complexidade (e de problemas que você deve evitar) que nem sempre valem a performance ganha.


### Ref. extras
- [Link 1](https://www.youtube.com/watch?v=vwWHlIX9W6s)
- [Link 2](https://medium.com/contentsquare-engineering-blog/multithreading-vs-multiprocessing-in-python-ece023ad55a)

## MÓDULO 4 - COMUNICAÇÃO ENTRE PROCESSOS
### Definição

Outro problema comum na ciência do computação é como comunicar dois processos rodando em computadores diferentes, ou em alguns casos como comunicar dois processos rodando no mesmo computador.

A área de redes de computadores é extremamente complexa e depende de vários conceitos que não são tão importantes no nosso contexto. Para o VSSS/SSL é importante entender sobre sockets e UDP/TCP.

### UDP x TCP

UDP e TCP são protocolos de transportes de dados usados na internet. Ex. Um site http usa o TCP para enviar o conteúdo da pagina para os usuários.  Um serviço de streaming ou de vídeo chamadas usam o UDP para enviar as informações.

**TCP:** O TCP é um protocolo que te garante que seu pacote vai chegar ao destino. Isso implica que antes de começar a troca de mensagens o TCP realizar uma conexão entre os processos e vai saber caso essa conexão seja quebrada. Além disso ele te garante que todos os pacotes enviado chegarão em ordem ao destino e caso não cheguem (ou sejam corrompidos no processo) ele automaticamente garante o reenvio pacotes.

**UDP:** diferente do TCP o UDP não garante a integridade e ordenação dos pacotes. Isso porem permite que ele seja bem mais rápido que o TCP.

Essas diferenças implicam diretamente na utilização exemplificada acima. No exemplo do site, quando você acessa um site você espera que a pagina chegue corretamente, mesmo que ela leve mais tempo para chegar. Já no exemplo do streaming, quando você vê um vídeo streaming você espera receber sempre o frame mais recente o mais rápido possível e mesmo que você perca alguns frames é melhor receber um frame mais novo do que reenviar um antigo.

### Sockets

Tipicamente na internet as interações entre processos são curtas e iniciados pelo usuário. Ou seja, o usuário faz requisição para o servidor que retorna uma resposta, esse conexão então fecha até a próxima requisição do usuário, onde esse processo recomeça. 

Porém em alguns casos nós queremos que essa conexão fique aberta por tempo indeterminado e/ou que tanto o server quanto o cliente possam enviar informações a qualquer momento. Para esses casos existe os Berkeley sockets ou só sockets. 

Podemos tomar como exemplo o NeonFC, nosso software de inteligência, que sabe da posição dos robôs e tomas as decisões, rodando e se comunicando com a interface, que roda em outro programa, por meio dos sockets.

### Caveat: Multicast

Um conceito que você vai se deparar no ecossistema do VSSS/SSL é o multicast. O multicast é uma implementação do UDP onde o server define uma porta por onde ele vai enviar seus pacotes e os clientes “assinam” que querem receber os pacotes dessa porta. Diferente dos sockets de TCP e UDP, o multicast depende de um roteador na rede para saber como direcionar esses pacotes, mesmo que todos os processos estejam rodando dentro da mesma máquina.

Um exemplo do multicast no VSSS/SSL é o software de visão SSL Vision que envia pacotes para os dois times jogando. Para isso cada time cria um socket UDP client e se inscreve na porta de multicast da visão.

    Exercicios

    1. Crie dois programas, cliente e servidor, e troca mensagens de um para outro usando sockets TCP
    
    2. Faça a mesma coisa usando UDP

## MÓDULO 5 — Estratégia, IA e Visão

*Objetivo: entender e contribuir com o software de alto nível de estratégia e percepção.*

### SSL-Vision e Protocolo de Comunicação

- Como o SSL-Vision funciona: câmeras → protobuf → Ethernet UDP.
- Receber pacotes UDP em Python usando `socket` e decodificar protobuf.
- SSL-Game-Controller: interpretar mensagens (kickoff, free kick, halt).

### Planejamento de Trajetória e Pathfinding

- Algoritmos: A*, RRT, Bug Algorithm, Potential Fields.
- DrunkWalk (implementação atual): entender o código e seus limites.
- Algoritmo Húngaro: atribuição ótima de N robôs a N tarefas.

### Controle de Posição e Filtros

- Controlador PID de posição: dado erro, calcular velocidade.
- Filtro de Kalman: predição + atualização para posição da bola.
- Estimativa de aceleração da bola com derivada numérica filtrada.