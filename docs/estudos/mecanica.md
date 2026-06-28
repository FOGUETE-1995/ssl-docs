ROTEIRO DE ESTUDOS — MECÂNICA

Caminho progressivo de estudos para os membros da área de Mecânica, do zero até os tópicos necessários para contribuir com o projeto SSL.

## MÓDULO 1 — Fundamentos de Modelagem 3D

*Objetivo: dominar o ambiente de CAD e criar modelos simples com boas práticas desde o início.*


### Introdução ao CAD — Fusion 360

- Criar conta Autodesk com e-mail institucional e ativar licença educacional.
- Conhecer a interface: barra de ferramentas, Browser (árvore de componentes), Timeline.
- Conceitos: Sketch (esboço 2D), Features (operações 3D) e Bodies.
- Primeiros comandos: Line, Circle, Rectangle, Extrude, Revolve, Fillet, Chamfer.
- Restrições geométricas: Coincident, Parallel, Perpendicular, Equal.
- Restrições dimensionais: cotas automáticas com Sketch Dimension.

*Exercício sugerido: modelar um cubo com furo central passante e uma esfera tangente à face superior.*

### Boas Práticas em Modelagem

- Sempre modelar a partir de esboços totalmente constrainados (sem linhas azuis livres).
- Nomear sketches, bodies e components descritivamente (ex: `Corpo_RodaA`, não `Body1`).
- Usar parâmetros (Parameters) para cotas que podem mudar.
- Organizar componentes em sub-assemblies lógicos (ex: `Conjunto_Motor_Roda`).
- Manter a Timeline limpa: excluir esboços auxiliares ao terminar.
- Salvar versões nomeadas (Milestone) ao concluir etapas importantes.

### Impressão 3D — Conceitos Essenciais

- Como funciona FDM: extrusão de filamento camada por camada.
- Materiais: PLA (fácil, frágil), PETG (resistente), ABS (alta temperatura, difícil).
- Parâmetros de fatiamento: layer height, infill, perímetros, suporte, velocidade.
- Anisotropia: peças são mais fracas perpendicular às camadas — orientar considerando a carga.
- Tolerâncias: furos saem menores que o projetado — usar folgas de 0,2 a 0,5 mm.
- Quando usar suportes: overhangs acima de 45° precisam de suporte.
- Softwares de fatiamento: Cura, PrusaSlicer, BambuStudio.

*Dica: imprima sempre uma peça de teste para validar tolerâncias antes de imprimir a peça completa.*

## MÓDULO 2 — Modelagem Avançada, Cinemática e Mecanismos

*Objetivo: projetar peças complexas e entender os mecanismos presentes no robô.*

### Modelagem de Peças Complexas

- Operações avançadas: Loft, Sweep, Shell, Mirror, Pattern (circular e linear).
- Multi-body modeling: criar múltiplos bodies em um componente e combiná-los.
- Importação de peças externas: formatos STEP, STL, OBJ.
- Projeto da roda sueca: analisar a modelagem existente e entender o espaçamento das rodinhas.

*Exercício sugerido: recriar a rodinha do robô a partir do zero baseando-se nas vistas do documento.*

### Assemblies e Joints

- Tipos de joints: Rigid, Revolute, Slider, Cylindrical, Ball, Planar.
- Criar um assembly do conjunto Motor+Roda+Suporte e verificar interferências.
- Motion Study: simular o movimento de rotação da roda.
- Contact Sets: detectar colisão entre corpos no assembly.

### Fundamentos de Álgebra Linear para Cinemática

Pré-requisitos matemáticos para entender a cinemática do robô (seção 2.3):

- Vetores: representação, soma, produto por escalar, produto interno.
- Matrizes: operações básicas (soma, produto, transposta), matriz identidade.
- Produto matricial: regra de compatibilidade de dimensões \((m \times n) \cdot (n \times p) = (m \times p)\).
- Sistemas lineares: \(Ax = b\), solução por eliminação gaussiana.
- Pseudo-inversa de Moore-Penrose: \(A^+ = (A^TA)^{-1}A^T\) — usada na cinemática inversa.
- Trigonometria: seno, cosseno, tangente; projeções de vetores nos eixos.
- Matrizes de rotação: \(R(\theta)\) e sua inversa \(R(-\theta) = R(\theta)^T\).

### Mecanismos Mecânicos do Robô

- Rodas omnidirecionais: como as rodinhas passivas perpendiculares ao eixo permitem deslizamento lateral.
- Transmissão direta (direct drive): sem redução, implicações em torque e velocidade.
- Suporte e fixação: análise de esforços — tração, compressão, torção.
- Acoplamentos: rígido vs. flexível, como o imã do encoder é acoplado ao eixo.

## MÓDULO 3 — Projeto com Requisitos de Competição

*Objetivo: projetar subsistemas completos respeitando as restrições da categoria SSL.*

### Análise de Esforços e FEA Básico

- Conceitos básicos de FEA: malha, condições de contorno, cargas.
- Simular esforços no suporte do motor usando Fusion Simulation.
- Interpretar resultados: Von Mises stress, deformação, fator de segurança.
- Otimizar geometria: nervuras, aliviamentos, redução de peso.

### Projeto do Kicker e Dribler — Mecânica

- Kicker: dimensionamento do solenóide, curso de atuação, fixação da chapa de chute.
- Dribler: geometria do rolo, ângulo de contato com a bola, tipo de rolamento e material.
- Restrições de espaço: verificar que tudo cabe dentro dos 180 mm de diâmetro.

### Gestão de Revisões de CAD

- Usar o sistema de versões do Fusion 360 (Timeline + Versions panel).
- Documentar cada revisão: o que mudou, por que mudou, resultado.
- Criar desenhos técnicos 2D com vistas, cotas e tolerâncias.
- Elaborar BOM (Bill of Materials) — peças compradas vs. impressas.