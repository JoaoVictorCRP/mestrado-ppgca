# UAC-AD: Unsupervised Adversarial Constrative AD

## Resumo
- "É muito importante manter a estabilidade e confiabilidade de sistemas baseados em microserviços, bem como identificar falhas e problemas através e uma detecção de anomalias."

- Considerando a falta de rotulamento de dados de observabilidade, foram propostas diversas metodologias baseadas em aprendizado não-supervisionado para detecção e análise multi-modal.
	- No entanto, métodos atuais possuem desafios em distinguir amostras difíceis (hard to classify) de anomalias reais.
	- Os autores indicam dois motivos:
    - O 1º é que amostras difíceis são complexas, isto é, os dados possuem multiplas dimensões, e a combinação entre diferentes modais é complexa, um padrão de log pode ser combinado com múltiplos padões de métrica para representar o estado normal de um sistema, o contrário também é possível (N métricas x 1 Log).

    - O 2º é que a velocidade de convergência é inconsistente entre amostras simples e difíceis (Ver Fig. 1), o que causa um overfitting do modelo em amostras simples e um underfitting em amostras difíceis, limitando a efetividade da AD.

- Para adereçar estes problemas do aprendizado não-supervisionado, é proposto o método de aprendizado não-supervisionado via análise Adversarial Contrastiva para detecção de anomalias (UAC-AD).
  - Aqui é utilizado aprendizado contrastivo para ajudar o modelo a aprender padrões complexos de amostras dificeis e alargar a distancia entre amostras dificeis e anomalias reais (possibilitando a distinção precisa entre estes dois).

- Para fins de avaliação, O UAC-AD foi aplicado em dois datasets open-sources simulados e um dataset industrial de uma grande companhia de comunicações.

- O código foi disponibilizado para testes e replicação em pesquisas futuras.

## Introdução 
- Aprendizado contrastivo é usado para possibilitar que o modelo aprenda combinações complexas de amostras normais ao alargar a distância entre amostras normais e anômalas. Isso permite que se possa distinguir melhor as amostras dificeis das anormais e reduzir falsos positivos.

- "Dados multimodais temporalmente alinhados refletem o estado operacional de um sistema, enquanto dados não alinhados refletem estado inconsistentes".
  - Por isso, os autores consideram a combinação temporalmente alinhada de logs e métricas como positivas (normais), enquanto as combinações não alinhadas como NEGATIVAS (anormais). Com isso, faz se então um aumento da distância entre amostras positivas e negativas, aliviando o primeiro desafio das amostras dificeis.

## Contribuições
- Clarificação do problema de incosistência de velocidade de convergência de amostras difíceis na detecção de anomalia multimodal e desenho de framework de aprendizado para resolver este problema.

- Estudos extensivos de ablação para verificar a efetividade da estratégia adversarial proposta.

- Aprendizado contrastivo dentro do aprendizado advesarial, melhorando a compreensão do modelo sobre combinação complexas de amostras multi-modal dificeis. Essa metodologia foca em aumentar o espaço entre amostras dificeis e as que são realmente anômalas, aumentando assim a performance da AD.

## Trabalhos Relacionados
- Diversos trabalhos anteriores abordaram a detecção de anomalias em sistemas baseados em microserviços utilizando aprendizado não-supervisionado.

### Baseados em métricas
- Variando de acordo com o critério para deteminação de anomalia, os paradigmas baseados em métricas podem ser categorizados em 3 tipos de métodos:
  - **Baseados em estimativa de densidade**: Considera que dados normais possuem uma distribuição probabilistica específica, é possível identificar anomalias de acordo com a densidade e a localização dos data points.
    - Zong et al. e Yairi et al. utilizaram Mistura Gaussiana, ou GMM, para estimativa de densidade.

  - **Baseados em clusterização**: Considera que dados outliers são anomalias, é possível detectar uma anomalia com base na distância de um data point até o centro do cluster.
    - Tax et al. e Ruff et al. seguiram essa abordagem em seus trabalhos.

  - **Baseados em reconstrução**: Considera que dados anormais são dificeis de reconstruir, portanto anomalias podem ser detectadas com base nos erros de reconstrução de encoders.
    - Park et al. e Ya et al. usaram LSTM ou GRU para capturar dependências temporais e Variational AutoEnconder (VAE) para reconstrução.

    - Hang et al. e Jung et al. incomporaram Graph Attention Networks (GAT) para capturar correlações espaciais em dimensões multivariadas de séries temporais.

### Baseados em logs
- A metodologia tradicional designa uma anomalia com base no aparecimento de palavras-chave como "erro" ou "falha" nos logs, ou então contam sua ocorrência em um período específico de tempo. No entanto, palavras negativas nos logs pode não indicar um problema real em um sistema.

- Por isso, aborgagens avançadas são propostas, todas seguem um fluxo similar: parsear logs, extrair atributos (semanticos ou sequenciais) e, a partir destes atributos, detectar a anomalia.
  - A maioria dos trabalhos detecta a anomalia por meio da análise de uma construção espacial dos logs.
    - Xu et al. usaram PCA para redução de dimensionalidade.
    - Lin et al. e He et al. utilizaram métodos de clusterização para a identificação de anomalias nos logs.

### Baseados em análise multi-modal
- Apesar da efetividade da análise uni-modal ser comprovadamente efetiva, elas podem ignorar anomalias que surgem em apenas um dos dois tipos de dados. Então, para obter melhor performance, pesquisasdores tentam nalizar mais de uma fonte de dados.

- Algumas pesquisas propõem a initegração com dados de tracing junto aos logs e métricas ou agrupar logs e métricas de um mesmo contexto de execução (via correlação temporal e/ou espacial).
  - Lee et al. propuseram a detecção de anomalias em logs e métricas através de aprendizado não-supervisionado.

## Metodologia

### Aprendizado Adversarial
- O aprendizado adversarial é uma técnica fundamental de machiine learning que envolve a competição entre dois modelos: um gerador e um discriminador.
  - O gerador tenta reconstruir (ou gerar) dados que se assemelhem aos dados reais.
  - O discriminador tenta distinguir entre os dados reais e os dados gerados pelo gerador.

- Um exemplo popular de aprendizado adversarial é a Generative Adversarial Network (GAN), onde o gerador e o discriminador são treinados de forma competitiva até que o gerador produza dados indistinguíveis dos reais.

- A vantagem desta abordagem é que o gerador aprende a capturar a distribuição dos dados reais, permitindo a detecção de anomalias com base em discrepâncias entre os dados gerados e os dados observados.

### Aprendizado Contrastivo
- O aprendizado constrastivo é um método de aprendizado não-supervisionado que busca aprender representações discriminativas ao maximizar a similaridade entre amostras positivas (semelhantes) e minimizar a similaridade entre amostras negativas (diferentes).

- Este método é particularmente útil para detecção de anomalias, pois permite que o modelo aprenda representações robustas dos dados normais, facilitando a identificação de padrões que se desviam do comportamento esperado.

- No caso deste trabalho, os autores fazem a fusão das características multi-modais com base no alinhamento temporal. Eles então tratam blocos temporais com métriicas e logs correspondentes como amostras positivas e aqueles que não correspondem como amostras negativas.
  - Este alinhamento permite a reconstrução dos dados dentro de janelas temporais consistentes, fazendo com que o modelo possa capturar melhor as dependências temporais e identificar anomalias de forma mais precisa.

### Pré-processamento de Dados
- Métricas são séries temporais típicas, geradas em um intervalo de tempo regular, possuindo um processamento facilitado para análise.

- Já os logs, por sua natureza semi-estruturada e não uniformemente distribuída, exigem um pré-processamento mais complexo para extração de atributos relevantes antes da análise.
  - No caso do artigo, eles usam o Drain para extrair templates de logs a partir dos registros brutos, na sequência esses templates são colocados em ordem cronológica.

- Feito isso, os dados de logs e métricas são alinhados temporalmente.

- E então, a estrutura destes dados é uniformizada através da transformação dos dados de logs em um série temporal.
  - Para cada bloco temporal, é verificada a ocorrência dos templates, marcando como "1" se o template estiver presente e "0" caso contrário. Desta forma, se obtém uma representação binária dos logs ao longo do tempo, compatível com a análise temporal das métricas.

### Módulo Gerador
- O módulo inclui os seguintes subcomponentes:
  - **Log Modeling**: Encoder de logs que enrigesse a relações entre blocos temporais. 
    - Isso é feito por meio de uma janela deslizante que utiliza os blocos temporais vizinhos para mapear atributos, para isso é adotada uma CNN (Convolutional Neural Network) 1D que captura as dependências temporais locais entre os blocos.

  - **Metric Modeling**: Similarmente ao Log Modeling, é utilizado CNN 1D e Transformer Encoder para capturar as correlações entre os blocos de métricas.
    - Isso permite que o modelo capture tanto as dependências temporais locais quanto as relações de longo alcance entre os blocos de métricas, melhorando a capacidade de detecção de anomalias.

## Validação

- O UAC-AD foi avaliado através da resposta das seguintes Research Questions (RQs):
  - RQ1: O quão efetivo é o UAC-AD na deteção de anomalias?

  - RQ2: Qual é a contribuição de cada componente do UAC-AD?

  - RQ3: Como o UAC-AD performa em termos de eficiência de tempo e armazenamento?

  - RQ4: O quão é afetado o método por parâmetros de sensibilidade?

  - RQ5: É possível explicar visualmente o impacto de cada componente do método?

### Os datasets
- Foram usados 3 datasets distintos para a avaliação do UAC-AD:
  - Dataset A: dataset disponibilizado por pesquisa anterior (HADES).
  - Dataset B: dataset multi-modal complexo simulado por uma empresa de tecnologia.
  - Dataset C: dataset real coletado por uma grande empresa de comunicações.