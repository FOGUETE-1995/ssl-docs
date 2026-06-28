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