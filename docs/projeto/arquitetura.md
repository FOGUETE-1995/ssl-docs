# ARQUITETURA DO ROBÔ

O robô do time é composto por três subsistemas principais: Locomoção, Kicker e Dribler (em desenvolvimento). Esta seção apresenta como cada sistema está estruturado e como eles se integram.

## Diagrama de Blocos Geral

O sistema eletrônico é organizado de forma modular, com os seguintes blocos principais implementados atualmente:

| Componente | Função |
|---|---|
| Bateria LiPo (14,8 V) | Fonte de energia principal do robô |
| ESP32 | Microcontrolador principal — recebe dados da visão via rádio e coordena os STM32 |
| STM32 (×2) | Cada STM32 controla 2 motores BLDC via SimpleFOC + Driver DRV8313 |
| Driver DRV8313 (×4) | Aciona as fases do motor BLDC usando interface 3×PWM |
| Motor BLDC GBM2804H (×4) | Motores das rodas suecas |
| Step-up | Eleva tensão da bateria para alimentação dos motores e kicker |

### Componentes previstos (ainda não implementados)

- Medidor de bateria — monitoramento do nível de carga.
- Encoder MT6701CT (×4) — leitura de posição angular via I2C.
- Dribler — motor com rolo para controle da bola.
- Kicker de alta tensão com LT3751 e IGBTs (evolução do kicker atual).

## Locomoção Omnidirecional

O robô utiliza 4 rodas suecas 90° para locomoção omnidirecional, permitindo movimento em qualquer direção independente da orientação do robô.

### Disposição das Rodas

- Rodas traseiras: ângulo de 90° entre si.
- Rodas dianteiras: ângulo de 120° entre si.
- Ângulo de 75° das rodas dianteiras em relação ao eixo X do robô.

### Montagem da Roda Sueca 90°

Cada roda é composta por:

- Roda Parte A — corpo principal com canal para o arame e 24 espaços para rodinhas.
- Roda Parte B — tampa sem canal, fixada por parafusos no motor.
- Arame bitola 1,5 mm² — eixo das rodinhas.
- 24 rodinhas (roldanas) com oring de borracha como pneu.
- Parafusos de fixação.

### Suporte de Fixação do Motor

O suporte acompanha as furações do motor e possui:

- Furos de fixação no chassi para facilitar manutenção.
- Furos para a placa do encoder na parte traseira.
- Furo central para o imã do encoder.

## Cinemática do Robô

A cinemática do robô descreve matematicamente como as velocidades angulares de cada motor se traduzem em movimento do robô no espaço. Para um robô omnidirecional com 4 rodas suecas, esse mapeamento envolve dois sistemas de referência e operações com matrizes.

### Sistemas de Referência

Dois sistemas de referência são utilizados na análise:

- **Base Global** \((X_G, Y_G)\): sistema fixo no chão, do ponto de vista de um observador externo estático. É neste referencial que a câmera do SSL-Vision fornece a posição do robô.
- **Base do Robô** \((X_R, Y_R)\): sistema de referência móvel preso ao centro do robô. Acompanha o robô em sua rotação. É neste referencial que os motores atuam.

Para converter vetores de velocidade entre as duas bases utiliza-se a matriz de rotação \(R(\theta)\), onde \(\theta\) é o ângulo de orientação atual do robô em relação ao eixo \(X_G\):

\[
R(\theta) =
\begin{bmatrix}
\cos\theta & -\sin\theta & 0 \\
\sin\theta & \cos\theta & 0 \\
0 & 0 & 1
\end{bmatrix}
\]

### Contribuição de Cada Roda

Cada roda sueca gera uma velocidade linear tangencial perpendicular ao seu eixo de giro. A contribuição dessa velocidade nos eixos \(X_R\) e \(Y_R\) do robô depende do ângulo \(\alpha_n\) que a roda faz com o eixo \(X_R\). Para as 4 rodas \((a, b, c, d)\):

| Roda | Ângulo \((\alpha_n)\) | Contribuição em \(X_R\) | Contribuição em \(Y_R\) |
|---|---|---|---|
| a (dianteira esq.) | \(\alpha_a\) | \(-\sin(\alpha_a)\, v_a\) | \(\cos(\alpha_a)\, v_a\) |
| b (traseira esq.) | \(\alpha_b\) | \(-\sin(\alpha_b)\, v_b\) | \(-\cos(\alpha_b)\, v_b\) |
| c (traseira dir.) | \(\alpha_b\) | \(\sin(\alpha_b)\, v_c\) | \(-\cos(\alpha_b)\, v_c\) |
| d (dianteira dir.) | \(\alpha_a\) | \(\sin(\alpha_a)\, v_d\) | \(\cos(\alpha_a)\, v_d\) |

Sendo \(v_n = \omega_n \cdot r\) (velocidade linear = velocidade angular do motor × raio da roda).

### Equação de Cinemática Direta

Reunindo as contribuições de todos os motores na forma matricial, obtemos a velocidade do robô em sua própria base \((X'_R, Y'_R, \theta')\) em função das velocidades angulares dos 4 motores:

\[
\begin{bmatrix}
X'_R \\
Y'_R \\
\theta'
\end{bmatrix}
=
\begin{bmatrix}
-\sin(\alpha_a) & -\sin(\alpha_b) & \sin(\alpha_b) & \sin(\alpha_a) \\
\cos(\alpha_a) & -\cos(\alpha_b) & -\cos(\alpha_b) & \cos(\alpha_a) \\
\frac{1}{2L} & \frac{1}{2L} & \frac{1}{2L} & \frac{1}{2L}
\end{bmatrix}
\cdot
\begin{bmatrix}
r & 0 & 0 & 0 \\
0 & r & 0 & 0 \\
0 & 0 & r & 0 \\
0 & 0 & 0 & r
\end{bmatrix}
\cdot
\begin{bmatrix}
\omega_a \\
\omega_b \\
\omega_c \\
\omega_d
\end{bmatrix}
\]

Onde \(L\) é a distância do centro de cada roda ao centro do robô, e \(r\) é o raio da roda.

### Conversão para Referencial Global

Para obter as velocidades no referencial global, necessário para navegar pelo campo usando as posições fornecidas pelo SSL-Vision, aplica-se a matriz de rotação:

\[
\begin{bmatrix}
X'_G \\
Y'_G \\
\theta'
\end{bmatrix}
=
R(\theta)
\cdot
\begin{bmatrix}
X'_R \\
Y'_R \\
\theta'
\end{bmatrix}
\]

### Cinemática Inversa — O Problema Real do Controle

Durante um jogo, o software de estratégia determina a velocidade desejada do robô no referencial global \([V_{XG}, V_{YG}, \omega_{robô}]\). O firmware precisa converter esses valores em velocidades angulares para cada motor \([\omega_a, \omega_b, \omega_c, \omega_d]\). Isso é a cinemática inversa.

Como a matriz cinemática é \(3 \times 4\) (sistema sobredeterminado), não existe inversa direta. Usa-se a pseudo-inversa de Moore-Penrose:

\[
\begin{bmatrix}
\omega_a \\
\omega_b \\
\omega_c \\
\omega_d
\end{bmatrix}
=
\frac{1}{r} \cdot M^{+} \cdot R(\theta)^{-1}
\cdot
\begin{bmatrix}
V_{XG} \\
V_{YG} \\
\omega_{robô}
\end{bmatrix}
\]

Onde \(M^{+}\) é a pseudo-inversa da matriz cinemática. Essa operação pode ser computada offline e armazenada como uma matriz constante, já que \(\alpha_a\), \(\alpha_b\) e \(L\) são fixos no robô, sendo apenas \(R(\theta)^{-1}\) recalculada a cada ciclo de controle com o \(\theta\) atual.

### Resumo do Fluxo de Controle Cinemático

1. SSL-Vision envia posição e orientação \((x, y, \theta)\) do robô.
2. O software de estratégia calcula a velocidade desejada \((V_{XG}, V_{YG}, \omega)\) no referencial global.
3. Aplica \(R(\theta)^{-1}\) para converter para a base do robô \((V_{XR}, V_{YR}, \omega)\).
4. Aplica a pseudo-inversa \(M^{+}\) para obter as velocidades angulares \((\omega_a, \omega_b, \omega_c, \omega_d)\).
5. Envia \(\omega_a \ldots \omega_d\) para os STM32, que executam o controle FOC de cada motor.

## Motor e Driver

| Especificação | Valor — Motor GBM2804H-100T |
|---|---|
| Tipo | Brushless DC (BLDC) — Gimbal Motor |
| Dimensões | 35 mm × 15 mm |
| Configuração | 12N14P (12 entalhos, 14 pólos) |
| Resistência de fase | 11,2 Ω |
| Tensão máxima | 14,8 V |
| Corrente máxima | 5 A |
| Velocidade máxima | 2.180 RPM |
| Peso | 41,5 g |

| Especificação | Valor — Driver DRV8313 |
|---|---|
| Arquitetura | Integrated FET (3 half-bridges) |
| Interface de controle | 3×PWM |
| Corrente de pico máx. | 3 A |
| Tensão de alimentação | 8 V – 65 V |
| Temperatura de operação | -40 °C a +125 °C |
| Resistência ON (HS+LS) | 480 mΩ |

### SimpleFOC — Controle de Campo Orientado

O acionamento dos motores usa a biblioteca SimpleFOClibrary implementando FOC (Field Oriented Control). O FOC garante controle suave de velocidade, alto torque e boa responsividade.

- Cada STM32 controla 2 motores via BLDCDriver3PWM.
- Pares de polos: 7 (motor 12N14P → 7 pares de polos).
- Resistência de fase configurada: 10 Ω | KV configurado: 60 rpm/V.
- Modo de controle atual: velocity_openloop (sem encoder ativo).
- Corrente máxima por motor: 300 mA.

## Encoder (Objetivo — Não Implementado)

### Status: Em Desenvolvimento

O encoder magnético ainda não está instalado no robô. A integração dos encoders é um dos próximos objetivos do time e permitirá fechar a malha de controle de velocidade (closed-loop), tornando o controle dos motores mais preciso e estável.

O componente previsto é o encoder magnético MT6701CT-STD-R, que lê a posição angular do motor por efeito Hall e comunica via protocolo I2C. Ele será fixado no suporte traseiro do motor com um imã acoplado ao eixo.

- Protocolo: I2C — simplifica a fiação, usando apenas 2 fios de dados.
- Biblioteca disponível para Arduino IDE (github.com/noranraskin/MT6701).
- Cada encoder fornece a posição angular absoluta do motor em tempo real.
- Com o encoder ativo, o modo de controle do SimpleFOC muda de velocity_openloop para velocity (closed-loop com PID).

## Kicker — Sistema de Chute

### Aviso — Substituição Prevista

O sistema de chute atual (descrito abaixo) será substituído por um sistema mais robusto baseado no CI LT3751 com IGBTs, capaz de carregar os capacitores a até 244 V e entregar mais energia à bobina. A documentação do sistema futuro está nos esquemáticos de referência do time.

### Sistema Atual — Visão Geral

O kicker atual é um circuito de média tensão com capacitores que, ao descarregar sobre uma bobina solenóide, empurra a bola. O limite regulamentar é 6,5 m/s.

### Estágio de Carregamento (Sistema Atual)

- Step-up: eleva 14 V da bateria para 50 V.
- Relé SRD-12VDC-SL-C: controla a passagem dos 50 V para o circuito de carga.
- Transistor 2N2222A (Q4): aciona o relé via sinal `charge` do MCU.
- Resistor de carga RZA01 (50 Ω): limita corrente de carregamento a ~1 A.
- Fusível F1 (2 A): proteção do circuito de carga.
- 3 capacitores 4700 µF / 63 V em paralelo: banco de energia para o chute.
- Tempo de carregamento: \(T = R \times 15 \times C \approx 3,5\ \text{s}\) (com 50 Ω e 3×4700 µF).

### Estágio de Descarga — Chute (Sistema Atual)

- MOSFET IRLZ44N (Q3): conecta o banco de capacitores à bobina solenóide.
- 2× transistores 2N2222A (Q1, Q2) em configuração Totem-Pole: lógica invertida para acionar o gate do MOSFET.
- Diodo 1N5408: proteção contra corrente reversa da bobina.
- Fusível F2 (3 A): proteção durante a descarga.
- Sinal de controle: pino `kicker` do MCU.

### Segurança com Capacitores

O banco de capacitores carregado a 50 V representa risco elétrico. A chave SwitchK1A permite descarregar manualmente os capacitores antes de manusear o robô. Esta chave deve ser aberta SEMPRE antes de qualquer manutenção.

## Dribler (Objetivo — Não Implementado)

### Status: Em Desenvolvimento

O dribler ainda não está implementado no robô atual. É um dos objetivos do time para próximas iterações. A descrição abaixo apresenta o conceito e o que deverá ser implementado.

O dribler é um motor que gira um rolo em contato com a bola, imprimindo efeito (backspin) para mantê-la próxima ao robô durante a movimentação. Ele é essencial para manobras de ataque e drible em alta velocidade.

### O que deverá ser implementado

- Motor BLDC dedicado ao dribler, controlado por um STM32 e driver próprio.
- Sistema de detecção de bola por IR (Ball Detection) para identificar quando a bola está em contato com o rolo.
- Sensor Ball Detection: LED IR (VSMB2000) + fotodiodo (TEMD1000) + AmpOp transimpedância (OPA320).
- Rolo mecânico com material e geometria adequados para maximizar o atrito com a bola sem danificá-la.
- Integração do sinal de `bola detectada` ao firmware para habilitar jogadas de passe e chute com controle.

## Eletrônica Geral — PCBs

O projeto é organizado em PCBs modulares. A placa principal atual (PCB B0) reúne os seguintes componentes implementados:

- ESP32: gerenciamento central, comunicação rádio.
- 2× STM32 (BluePill): controle FOC dos 4 motores de locomoção.
- Step-up de alimentação dos motores.

### Componentes e recursos não implementados na PCB atual

- Medidor de bateria — previsto, mas ainda não implementado.
- Encoder — ainda não instalado (ver seção 2.5).
- Dribler — sem placa própria ainda (ver seção 2.7).
- Kicker de alta tensão com LT3751 — sistema futuro.

Os esquemáticos do projeto estão disponíveis no repositório do time. Ver seção de Pendências para links.

# PROGRAMAÇÃO — VISÃO GERAL DO SOFTWARE

O software do robô é dividido em duas camadas principais: o Firmware (embarcado nos microcontroladores) e o Software de Estratégia (rodando em computador externo).

## Firmware (Embarcado)

| Módulo | Responsabilidade |
|---|---|
| ESP32 — Main Firmware | Recebe comandos via rádio (WiFi/RF), distribui para STM32s via UART |
| STM32 — Motor Control (×2) | Executa SimpleFOC para controle FOC de 2 motores cada, modo open-loop atual |
| STM32 — Kicker | Gerencia ciclos de carga/disparo do kicker via pinos `charge` e `kicker` |

### Itens em desenvolvimento (Firmware)

- Encoder MT6701CT: integrar leitura I2C para fechar malha de controle de velocidade.
- Dribler: adicionar controle de motor + leitura do sensor de bola (Ball Detection IR).
- Sensor de bola via IR: detectar presença da bola no dribler.
- LEDs indicativos: estado de bateria, recepção de dados, modo de operação.
- Comunicação STM32 → ESP32: envio de sensor de temperatura.
- Modo debugger: configurar e calibrar robô com ele ligado.
- Verificação de gargalos de comunicação.

## Software de Estratégia (NeonFC)

O software externo em Python recebe dados do SSL-Vision, processa a estratégia e envia comandos de velocidade aos robôs via rede.

### Controle

- Implementar e estudar controle FOC em malha fechada.
- Estudar tese RoboCIn para referências de controle.
- Implementar controladores de velocidade (PID).

### Estratégia

- Implementar algoritmo Húngaro para atribuição de funções a robôs.
- Adaptar sistema de chute para o robô físico.
- Melhorar pathfinding (DrunkWalk / planejamento de trajetória).
- Estudo de Machine Learning para probabilidade de gol (xG).

### Interface

- Visualização do campo corrigida.
- Recepção direta de pacotes da visão SSL-Vision.
- Comunicação NeonFC ↔ Interface com botão de start/stop.
- Controle do robô via interface gráfica.

### Filtros

- Implementar filtro de Kalman para suavização de posição.
- Implementar estimativa de aceleração da bola.