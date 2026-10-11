# Uncertainty-Aware Multimodal Anomaly Detection for Microservice Systems with Active Learning

- O artigo começa expondo o estouro na popularidade de arquiteturas de microsserviços e a complexidade crescente na detecção de anomalias nesses sistemas.

- Deixa claro que a dificuldade da depuração de problemas leva a perdas economicas para empresas e usuários.

- Mostra que estudos recentes utilizam abordagens unimodais (isto é, só consideram logs OU métricas OU traces) para a tomada de decisão de uma anomalia, o que possui certa eficiência, porém extremamente limitada para capturar a complexidade das interações em sistemas de microsserviços.

- Comenta sobre estudo de abordagens multimodais nos anos recentes.
  - Levanta que, apesar das abordagens considerarem as múltiplas modalidades de dados, ainda enfrentam desafios, caracterizados por:
    - **Incerteza Intra-Modal**: Como dados multimodais se originam de fontes de dados heterogêneas e é tende a ser incerta/imprecisa devido à ruído, desaparecimento de datapoints e ambiguidade.
      
      - Estas incertezas comprometem a qualidade dos dados e reduzem significamente a capacidade do modelo de compreender o conteúdo multimodal de maneira efetiva.
      
      - Abordagens existentes empregram a vetorização de pontos determinísticos para que o modelo possa aprender representações específicas de cada modal.

      - Esses métodos porém dependem fortemente de dados de treinamento de alta qualidade. E, quando processando dados de baixa qualidade, a vetorização baseada em dados de alta qualidade pode fazer com que o modelo capture de maneira imprecisa informações críticas. O que gera um gap de performance e robustez.

    - **Incerteza Inter-Modal**: Ainda que todos os modais precisam ser levados em conta na hora de tomar uma decisão. O peso de cada modalidade não é estática a cada amostra, isto é, a cada amostra, um modal diferente pode refletir de maneira mais precisa o estado real do sistema.

      - Abordagens existentes consideram que, em todos os casos, todas os modais são importantes para um processo de detecção.
      - Mas isso leva a imprecisão, especialmente considerando o conflito inter-modal (um modal apresenta normalidade, outro não).
      - Os autores indicam que é necessária uma avaliação dinâmica de qual modalidade reflete com mais precisão o estado real do sistema, o que permitiria que o modelo pudesse ajustar os níveis de confiança em cada modalidade.
  
    - **Escassez de dados de treinamento**: Amostras de dados de alta qualidade e rotuladas de maneira precisa melhoram significativamente a performance de detecção dos modelos, mas o precsso é caro e demorado, pois involve a análise manual de dados complexos e a tomada de uma decisão sobre cada amostra.

      - Consequentemente, um desafio chave na detecção de anomalias multimodal é fazer o balanceamento entre o custo da anotação e a performance do modelo, o que permitirá mantera a efetividade em cenários com escassez de dados rotulados.

- A partir destes desafios o trabalho propõe o MUAD, um framework de detecção de anomalias multimodal alerta para a incerteza, com aprendizado ativo. Consiste de 4 estágios:
  1. **Encoding de features unimodal**: Onde cada modalidade é codificada em uma representação determínistica.
  
  2. **Modelagem de incerteza**: Que extrai robustas representações especificas de cada modalidade ao projetar vetores estocásticos latentes.

  3. **Fusão dinâmica de modalidades**: Onde um mecanismo de fusão baseado em confiança designa pesos diferentes para cada modalidade baseado em uma estimativa de confiança, e então combina as modalidades em uma representação global.

  4. **Treinamento ativo**: Que atualiza o modelo iterativamente utilizando dados pseudo-rotulados e amostras precisamente rotuladas.