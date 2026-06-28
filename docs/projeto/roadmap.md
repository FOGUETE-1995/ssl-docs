# PENDÊNCIA E PRÓXIMOS PASSOS

## MECÂNICA
- Finalizar seções de Mecânica do Kicker e do Dribler no documento do projeto
- Documentar carcaça e encaixes internos com imagens e dimensões
- Validar tolerâncias de impressão 3D nas peças de encaixe
- Revisar chassi para acomodar PCBs futuras, kicker e dribler definitivos

## ELETRÔNICA
- Integrar encoders MT6701CT ao hardware e validar leitura I2C no STM32
- Projetar e testar PCB do dribler (motor + sensor Ball Detection IR)
- Evoluir kicker para sistema com LT3751 e IGBTs (substituição do sistema atual)
- Implementar medidor de bateria (INA226 ou similar) na PCB principal
- Documentar Step-up de alimentação dos motores (seção ainda incompleta)
- Finalizar seção de Eletrônica do Dribler e do Kicker no documento
- Desenvolver próxima revisão de PCB no Altium Designer integrando todos os módulos

### Pendência de Documentação — Esquemáticos
- Adicionar link para os esquemáticos ATUAIS do robô (pasta/repositório do time)
- Adicionar link para os esquemáticos de REFERÊNCIA (arquitetura futura com Powerboard, Mainboard STM32H7, Kicker LT3751, Ball Detection, LEDs, encoders iC-PX)

    *Ambos os links são importantes: os atuais documentam o que existe, e os de referência guiam o desenvolvimento futuro.*

## PROGRAMÇÃO
- Firmware: integrar encoders MT6701CT e fechar malha de controle de velocidade
- Firmware: implementar controle do dribler + sensor de bola IR
- Firmware: adicionar LEDs indicativos de estado
- Estratégia: adaptar sistema de chute para o robô físico
- Estratégia: implementar algoritmo Húngaro para alocação de robôs
- Estratégia: melhorar pathfinding (evoluir ou substituir DrunkWalk)
- Interface: corrigir visualização do campo e receber pacotes da visão diretamente
- Filtros: implementar Kalman para posição e aceleração da bola
- Simulador: arrumar simulador de jogo para testes sem hardware