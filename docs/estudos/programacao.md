ROTEIRO DE ESTUDOS — PROGRAMAÇÃO

Roteiro estruturado para que o membro consiga contribuir tanto com o firmware dos microcontroladores quanto com o software de estratégia em Python.

## MÓDULO 1 — Fundamentos

*Objetivo: dominar as ferramentas e conceitos base usados no projeto.*

### Programação Orientada a Objetos (Python)

- Conceitos: Classes, Objetos, Atributos, Métodos.
- Encapsulamento, Herança e Polimorfismo — focar em conceitos antes de código.
- Exercício prático: criar uma classe Robot com posição, velocidade e métodos de movimentação.
- Aprofundamento (opcional): Dunder Methods, Classes abstratas (ABC), SOLID Principles, Dataclasses.

### Git e GitHub

- Diferença entre Git e GitHub.
- Comandos: git clone, add, commit, push, pull, branch, merge.
- Fluxo: branch para nova feature → commits atômicos → Pull Request.
- Resolução de conflitos de merge.
- Prática: fork do repositório NeonFC, clonar e fazer primeira contribuição.

### Ambiente de Desenvolvimento

- Python 3.10+ e ambientes virtuais (venv).
- IDE recomendada: VSCode com extensão Python e Pylance.
- Arduino IDE: configurar para STM32 via stm32duino.
- Leitura obrigatória: Getting Started do NeonFC (link no repositório do time).

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

## MÓDULO 3 — Estratégia, IA e Visão

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