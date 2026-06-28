# SOBRE A CATEGORIA SSL

A categoria Small Size Soccer League (SSL), também conhecida como F180, é uma das ligas mais antigas do RoboCup — competição internacional de robótica criada em 1997. O foco da SSL está na coordenação inteligente de múltiplos agentes autônomos em um ambiente altamente dinâmico.

## Regras e Dimensões

| Parâmetro | Valor |
|---|---|
| Tamanho máximo do robô | 180 mm de diâmetro \| 150 mm de altura |
| Partidas (Divisão A) | 11 vs 11 robôs |
| Partidas (Divisão B) | 6 vs 6 robôs |
| Campo (Divisão A) | 12 m × 9 m |
| Campo (Divisão B) | 9 m × 6 m |
| Bola | Golf ball laranja |
| Velocidade máx. da bola | 6,5 m/s |
| Sistema de visão | SSL-Vision (câmeras suspensas a 4 m) |
| Comunicação | Rádio sem fio (Bluetooth não permitido) |
| Cor do casco | Deve suportar casco claro e escuro intercambiáveis |

## Como Funciona um Jogo SSL

O ecossistema de um jogo SSL envolve três camadas principais que devem trabalhar juntas de forma integrada:

- **SSL-Vision**: sistema de visão compartilhado com câmeras sobre o campo que rastreia posição e orientação de todos os robôs e da bola, enviando os dados por Ethernet.
- **SSL-Game-Controller**: software árbitro que traduz decisões em sinais de rede para as equipes.
- **Computador externo (off-field)**: cada equipe roda seu software de estratégia fora do campo, recebendo dados da visão e enviando comandos via rádio para os robôs.
- **Robôs autônomos**: executam localmente os comandos de baixo nível (controle de motores, chute, dribler) sem intervenção humana durante a partida.

> O nosso robô deve caber em um círculo de 180 mm de diâmetro e ter no máximo 150 mm de altura. A bola não pode ser chutada acima de 6,5 m/s. O padrão de visão no topo do robô deve seguir as especificações do SSL-Vision.