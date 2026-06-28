# LOCOMOÇÃO

## MOTOR
Modelo: GBM2804H-100T Gimbal Motor

<p align="center">
    <img src="../../assets/images/eletronica/motor_1.png"
    width="500">
</p>

Especificações:<br>
Modelo NO.: GBM2804H-100T<br>
Peso: 41,5g<br>
Dimensão do motor: 35 mm * 15 mm<br>
Diâmetro do estator: 28mm * 4mm<br>
Fio de cobre (OD): 0,19 mm<br>
Configuração: 12N14P<br>
Resistência: 11,2 ohms<br>
Passo central da base: 16mm 19mm<br>
Pré-enrolado: com eixo oco de 100 voltas<br>
Tensão de teste: 11,1V<br>
Tensão máxima: 14,8 V<br>
Corrente de teste: 0,0099 A<br>
Corrente máxima: 5A<br>
Velocidade de rotação do teste: 1637 rpm<br>
Velocidade máxima de rotação: 2180 rpm<br>

O motor escolhido foi pensado em ter um motor que ocupe menos espaço e proporcione um torque adequado para a locomoção do robô. O fato de possuir cerca de 11 Ohms de resistência interna, faz com que haja uma corrente menor sendo consumida pelo motor, isso ajuda na escolha do driver de acionamento, pois possibilita escolher um driver com uma capacidade de corrente menor.

## DRIVER
Modelo: DRV8313

<p align="center">
    <img src="../../assets/images/eletronica/driver_1.png"
    width="500">
</p>

Especificações:<br>
Arquitetura: Integrated FET<br>
Interface de controle: 3xPWM<br>
RDS(ON) (HS + LS) (mΩ): 480<br>
Corrente de pico de saída máx. (A): 3<br>
Vs (min) (V): 8<br>
Vs ABS (max) (V): 65<br>
Faixa de temperatura de operação (°C): -40 até 125<br>

Esta é uma figura que ilustra um modelo de placa desenvolvida baseada no CI DRV8313 para o controle de um motor brushless e é um modelo de construção bastante utilizado em projetos com FOC. Mais especificações podem ser encontradas no datasheet do componente: [DRV8313 datasheet (Rev. D)](https://www.ti.com/lit/ds/symlink/drv8313.pdf?ts=1744957647878&ref_url=https%253A%252F%252Fwww.ti.com%252Fproduct%252FDRV8313)


## ENCODER
Modelo: MT6701CT-STD-R

<p align="center">
    <img src="../../assets/images/eletronica/encoder_1.png"
    width="500">
</p>

O encoder é um componente importante no controle de velocidade do robô, visto que é necessário ter o valor da velocidade mais precisa durante a tomada de decisão ajuda a criar estratégias mais efetivas durante os jogos. Este tipo de encoder pode se comunicar utilizando o protocolo I2C, que facilita as conexões. Mais especificações podem ser encontradas em: [MT6701CT-STD-R | Datasheet LCSC Electronics](https://www.lcsc.com/datasheet/C3003196.pdf). Já existe uma biblioteca para o uso deste sensor com o Arduino IDE, que facilita a programação.
	
## STEP-UP DE ALIMENTAÇÃO DOS MOTORES
