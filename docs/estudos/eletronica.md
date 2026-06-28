ROTEIRO DE ESTUDOS — ELETRÔNICA

O roteiro parte dos fundamentos e progride até os tópicos específicos de toda a arquitetura eletrônica do robô SSL — desde drivers de motor e encoders até o kicker de alta tensão, sensores e projeto de PCB profissional no Altium Designer.

## MÓDULO 1 — Fundamentos de Eletrônica

*Objetivo: construir a base necessária para entender qualquer circuito do robô.*

### Conceitos Fundamentais de Eletricidade

- Lei de Ohm e Leis de Kirchhoff (KVL e KCL).
- Resistores: série, paralelo, divisor de tensão e de corrente.
- Capacitores: carga/descarga, constante de tempo \(\tau = RC\), armazenamento de energia.
- Indutores: campo magnético, relação tensão-corrente, impedância.
- Potência elétrica: \(P = V \cdot I = V^2/R = I^2 \cdot R\).
- Análise de circuitos AC: impedância, fasores, potência reativa.

*Ferramenta: simular circuitos no Falstad Circuit Simulator (gratuito, online) antes de montar na bancada.*

### Componentes Passivos — Filtros RC, RL e RLC

- Filtros passivos: passa-baixa, passa-alta, passa-banda com RC e LC.
- Constante de tempo RC: carregamento de capacitores no kicker \((\tau = R \cdot C)\).
- Circuitos RLC: resposta natural, frequência de ressonância, fator de qualidade Q.
- Indutores em conversores chaveados: armazenamento e liberação de energia.

### Semicondutores — Diodos

- Diodos retificadores: curva V-I, tensão de limiar, uso em proteção de polaridade.
- Diodo Schottky (ex: MBRB40250): baixa queda de tensão, alta velocidade — usado no kicker futuro.
- Diodo ultrarrápido (ex: ES3J): recovery time curto — essencial em conversores Boost.
- Diodo TVS (ex: SMBJ13CA): proteção contra surtos de tensão transientes.
- Diodo zener: regulação de tensão, uso como referência.
- Diodo de roda livre (flyback): proteção contra corrente reversa de bobinas — 1N4007 e 1N5408 no kicker atual.

### Transistores BJT — Uso como Chave

- Estrutura NPN e PNP: regiões de operação (corte, ativo, saturação).
- Transistor como chave digital: calcular resistor de base para saturação.
- 2N2222A: transistor NPN de uso geral — usado no kicker para acionar relé e MOSFET.
- Configuração Totem-Pole (Q1+Q2 no kicker): por que dois transistores em série invertem a lógica do gate.
- Pull-up e pull-down resistivos: R4 e R5 no circuito do kicker.
- Parâmetro hFE (ganho de corrente): dimensionamento da corrente de base.

### MOSFETs — Chaves de Potência

- MOSFET canal N e canal P: tensão gate-source (VGS), threshold voltage.
- Regiões de operação: corte, linear (triodo), saturação.
- IRLZ44N: MOSFET canal N de potência — aciona a bobina do kicker atual.
- RDS(ON): resistência em condução — impacta dissipação de calor.
- Capacitância de gate: por que é necessário um driver de gate para chaveamento rápido.
- Safe Operating Area (SOA): limites de tensão, corrente e temperatura.

### Protocolos de Comunicação Digital

- UART: bits start/stop, baud rate — comunicação ESP32 ↔ STM32.
- I2C: SDA/SCL, endereçamento 7-bit, resistores de pull-up — encoder MT6701CT e INA226.
- SPI: MOSI/MISO/SCK/CS, modo 0/1/2/3 — rádio SX1280 e kicker.
- PWM: duty cycle, frequência, resolução — controle de motores e LEDs.
- ADC: resolução, tensão de referência, oversampling — leitura de sensores e Ball Detection.
- GPIO, Timers, DMA: periféricos essenciais dos STM32.

## MÓDULO 2 — Amplificadores Operacionais, Fontes e Motores BLDC

*Objetivo: entender os blocos analógicos, conversores de energia e o controle FOC dos motores.*

### Amplificadores Operacionais (AmpOp)

- Conceito de AmpOp ideal: ganho infinito, impedância de entrada infinita, saída ideal.
- Realimentação negativa: como estabiliza o ganho e define o comportamento do circuito.
- Configurações fundamentais: inversor, não-inversor, seguidor de tensão (buffer), somador, subtrator.
- Amplificador de instrumentação: mede pequenas diferenças de tensão com alta CMRR.
- Amplificador transimpedância (TIA): converte corrente em tensão — usado no fotodiodo da Ball Detection (OPA320).
- OPA320: AmpOp rail-to-rail de precisão — analisar o datasheet e entender os parâmetros: GBW, slew rate, offset.
- MCP6002: AmpOp de uso geral — usado no circuito do kicker para monitoramento e comparação de tensão.
- Comparadores: diferença entre AmpOp em malha aberta e comparador dedicado.
- Filtros ativos de 1ª e 2ª ordem: passa-baixa Sallen-Key, uso na filtragem de sinais de sensores.

### Fontes Chaveadas — Buck e Boost

- Conversor Buck (Step-Down): indutor + MOSFET + diodo + capacitor — gera 12 V, 5 V a partir da bateria.
- LMR23630: CI Buck utilizado no Powerboard — analisar datasheet: frequência, indutor, resistores de feedback.
- Conversor Boost (Step-Up): como eleva a tensão — usado para alimentar os motores e o kicker.
- Duty cycle: relação entre tensão de entrada e saída em Buck \((V_{out} = D \cdot V_{in})\) e Boost \((V_{out} = V_{in}/(1-D))\).
- Ondulação de corrente (ripple): cálculo do indutor e capacitor de saída.
- Soft-start: como evitar pico de corrente na energização.
- Hot-Swap Controller LTC4231: proteção de inrush current e curto-circuito na entrada da Powerboard.
- Regulador Linear LDO (LT3080): gera 3,3 V estável para eletrônica digital — baixo ruído, sem ripple.

### Monitoramento de Tensão e Corrente

- Shunt resistor: medir corrente pela queda de tensão em resistor de baixo valor.
- INA226: CI monitor de tensão e corrente via I2C — lê corrente com shunt de 1 mΩ, envia dados à Mainboard.
- Registros do INA226: configuração de averaging, leitura de bus voltage, shunt voltage e power.
- Parâmetros: resolução de corrente, faixa de medição, endereço I2C.

### Motores Brushless DC (BLDC) e FOC

- Estrutura BLDC: estator com 3 fases, rotor com imãs permanentes, notação 12N14P.
- KV (rpm/V): relação velocidade × tensão; torque e corrente: \(T = K_t \times I\).
- Vantagens do Gimbal Motor: baixo KV, alta resistência de fase, controle suave em baixas velocidades.
- Driver DRV8313: 3 half-bridges integradas, interface 3×PWM — analisar tabela de verdade do datasheet.
- FOC: transformadas de Clarke e Park, vetores d-q, controle independente de torque e fluxo.
- SimpleFOClibrary: BLDCMotor, BLDCDriver3PWM, modos de controle (open-loop, velocity, angle).
- Tuning de PID: ajuste de P, I, D para controle de velocidade — usar monitor serial do SimpleFOC.

## MÓDULO 3 — Eletrônica de Potência, Sensores e Projeto de PCB

*Objetivo: entender os circuitos mais complexos do robô (kicker de alta tensão, sensores, comunicação RF) e aprender a projetar PCBs profissionais no Altium Designer.*

### Kicker de Alta Tensão — Sistema Futuro com LT3751

### Sistema em desenvolvimento

O kicker futuro opera com até 244 V e capacitores de alta energia (~107 J). Estudo obrigatório antes de qualquer trabalho neste circuito.

Bloco de carregamento — Conversor Boost de alta tensão:

- LT3751: CI controlador Boost dedicado a carregamento de capacitores — controla tensão final, detecta fim de carga (DONE), possui UVLO e OVLO.
- FDS2582: MOSFET de chaveamento do Boost — analisar tensão de drain, corrente de pico, RDS(ON).
- Indutor DA2034: armazenamento de energia durante o chaveamento do Boost.
- ES3J: diodo ultrarrápido de retificação do Boost.
- Capacitores 1800 µF / 250 V: banco de energia — energia \(E = \frac{1}{2}CV^2 \approx 107\ J\) a 244 V.
- SMBJ13CA: diodo TVS de proteção contra transientes.

Bloco de descarga — Chaveamento de alta corrente:

- IKB40N65ES5: IGBT de potência 40 A / 650 V — por que usar IGBT em vez de MOSFET para esta aplicação.
- FAN3229: driver de gate de alta corrente — aciona o gate do IGBT rapidamente para minimizar perdas de chaveamento.
- MBRB40250: diodo Schottky de potência — proteção de corrente reversa na bobina.
- MCP6002 no kicker: configurado como comparador para detectar tensão dos capacitores e sinalizar “carregado”.
- Cálculo da energia de chute: \(E = \frac{1}{2}CV^2\) e relação com velocidade da bola.

### Encoders e Sensores de Posição

- Encoder magnético vs. óptico: vantagens do magnético em ambiente com vibração e poeira.
- MT6701CT: encoder magnético absoluto de 14 bits — leitura de ângulo via I2C.
- iC-PX2604: encoder incremental com canais A/B — usado na arquitetura de referência, saídas para Timers do STM32.
- Canais A e B em quadratura: como determinar direção de rotação e contar pulsos.
- Multiplexador TCA9548A: por que usar com vários sensores I2C de mesmo endereço.

### Sensores Ópticos — Ball Detection e IR Controller

- Fotodiodo TEMD1000: converte luz incidente em corrente fotovoltaica proporcional à intensidade.
- LED IR VSMB2000: emite luz infravermelha modulada para detecção da bola.
- Amplificador transimpedância com OPA320: converte corrente do fotodiodo em tensão mensurável pelo ADC.
- Ganho do TIA: \(R_{feedback}\) determina a relação corrente→tensão \((V_{out} = I_{ph} \times R_f)\).
- IR Controller (STM32F031): microcontrolador dedicado que lê 5 sensores IR, filtra e envia dados via UART.
- Sensores RGB TCS34725 no Pattern Identification: como identificar o padrão de cores do robô para a câmera SSL-Vision.

### Sistema de LEDs e Drivers de Corrente

- BCR421: driver de corrente constante para LEDs — por que usar corrente constante e não tensão constante.
- Resistor REXT: define a corrente de saída do BCR421.
- LEDs RGBW de 1 W: potência, eficiência luminosa, dissipação térmica.
- Controle PWM de LEDs: dimmer de brilho via duty cycle dos Timers do STM32.

### Comunicação RF — SX1280 e SKY66112

- SX1280: transceptor RF 2,4 GHz — comunicação entre Base Station e robô.
- Modulações suportadas: GFSK, LORA, FLRC — por que usar RF dedicado e não WiFi.
- SKY66112: Front-End Module (FEM) RF — amplifica potência de transmissão e melhora sensibilidade na recepção.
- Filtro FL520WFMT1: filtro de antena para rejeitar interferências fora da banda 2,4 GHz.
- SPI: interface entre STM32 e SX1280 para envio de pacotes.
- Base Station: STM32 + SX1280 + DP83848 (PHY Ethernet) + FT2232 (USB) — ponte entre PC e robôs.

### Projeto de PCB Profissional no Altium Designer

- Interface do Altium: Schematic Editor, PCB Editor, ActiveBOM, Draftsman.
- Criação de esquemático: componentes, net labels, hierarquia de folhas, power ports.
- Library management: criar componentes (schematic symbol + PCB footprint + 3D model) no Altium.
- Design Rules Check (DRC): definir regras de clearance, largura de trilha mínima, via size.
- Layout de PCB: empilhamento de camadas, plano de GND, separação entre potência e lógica.
- Roteamento: trilhas de sinal fino (0,1–0,2 mm), trilhas de potência largas (calculadas pela corrente).
- Capacitores de desacoplamento: posicionar o mais próximo possível dos pinos VCC de cada CI.
- Dissipação térmica: thermal pads, vias térmicas, copper pours para ICs de potência.
- Integridade de sinal: retorno de corrente, minimizar loop de corrente, terminações.
- Compatibilidade eletromagnética (EMC/EMI): separação de circuitos analógicos/digitais/RF, plano de terra contínuo.
- Geração de arquivos de fabricação: Gerber, NC Drill, BOM, CPL — envio para JLCPCB ou PCBWay.
- Altium 365: colaboração em nuvem, revisões de projeto, integração com Git.