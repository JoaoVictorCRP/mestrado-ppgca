# O artigo

- O artigo *"Few-shot Multimodal Anomaly Detection via DynamicIntra-modal Sparsity Attention and Quality-awareCross-modal Fusion in Microservice System"* apresenta o FuseGuard, um framework aprimorado por LLM que pioneira a fusão multimodal consciente da qualidade para detecção de anomalias (AD) em sistemas de microsserviços. O FuseGuard aborda os desafios críticos do cenário de poucos disparos, como a extração eficaz de recursos intra-modais e o alinhamento de representações multimodais heterogêneas. Ele utiliza mecanismos avançados, como atenção de correlação esparsa dinâmica para métricas, codificação espaço-temporal para rastreamentos e codificação semântica-frequência aprimorada por LLM para logs. Além disso, o FuseGuard introduz um mecanismo de fusão consciente da qualidade que pondera dinamicamente as modalidades com base na estimativa de incerteza. Avaliado em várias plataformas e um sistema de produção.

## O problema

- O artigo destava os desafios no consumo de dados multimodais e o estabalecimento da correlação entre eles.

- Em especial, dois desafios são citados:
  1. Exploração suficiente de dados multimodais: Métodos existentes falham em modelar dependências multimodais complexas em métricas (gerando falsas correlações ou ignorando o domínio da frequência) e tratam logs apenas em nível estatístico superficial, ignorando pistas contextuais

  2. Alinhamento e fusão multimodal robustos: Existe uma disparidade inerente entre as modalidades (métricas e traces têm estrutura temporal precisa, enquanto logs possuem riqueza semância mas organização temporal imprecisa). Além disso, os dados reais sofrem com discrepâncias de qualidade devido a erros de transmisssão e ruídos, gerando corrupção nas representações quando funidas de maneira indiscriminada.

- Destaque para a abordagem Few-shot, que é a capacidade de um modelo aprender a partir de um número muito limitado de exemplos. Isso é especialmente relevante em cenários de detecção de anomalias, pois dados de anomalias são escassos (dado o fato de que esse não é um dado disponível publicamente em grande quantidade, e também porque anomalias são eventos raros por definição). O FuseGuard é projetado para ser eficaz mesmo com poucos exemplos de anomalias, o que o torna uma solução prática para ambientes de produção onde a coleta de dados de anomalias pode ser difícil.

## Os métodos utilizados

- **Para métricas**: Mecanismos de atenção de correlação esparsa dinâmica no domínio da frequência (via rFFT) para capturar dependências multimodais complexas, e amostragem reparametrizada (Gumbel-Softmax) para enfatizar métricas relevantes, evitando falsas correlações.
  
  - A **rFFT (Real Fast Fourier Transform)** é uma técnica de transformação que pode converter uma sequência de tempo em uma representação no domínio da frequência. Isso é útil para capturar padrões e dependências que podem não ser evidentes no domínio do tempo, especialmente em dados de séries temporais como métricas, exemplo: CPU bate 100% a cada 5 minutos, o que pode indicar um padrão de uso.

  - A **Atenção de Correlação Esparsa Dinâmica** faz o computador descobrir quais métricas têm a ver uma com a outra em determinado momento, e ignora as que não tem relação. Isto é feito por meio da **Distância de Mahalanobis**, que é uma medida de distância entre um ponto e uma distribuição. No contexto do artigo, ela é usada para determinar a relevância das métricas em relação umas às outras, permitindo que o modelo se concentre nas métricas mais importantes para a detecção de anomalias.

  - Amostragem reparametrizada (**Gumbel-Softmax**) é um truque matemático que permite a "suavização" de uma escolha rígida, isto é, ao invés de o computador ter que escolher entre 0 e 1 para a determinação de uma afirmação, ele pode escolher um valor ENTRE 0 e 1, ex: 0,99; 0,80; 0,50, etc. Isso é útil para estabelecer um parâmetro preciso de relevância.

- **Para Traces**: Convoluções temporais 1D para a captura de tendências recentes e Convoluções em Grafos para analise da estrutura de comunicações entre os nós (serviços) do grafo.

  - Como dito acima, para traces são usados dois conceitos distintos, um para a visualização de espaço e outra para a visualização de tempo.

  - As **Convoluções temporais 1D** conseguem passar "deslizando" por todo uma janela (intervalo) de uma série temporal, permitindo a percepção de padrões anômalos.

  - Já as **Convoluções em grafos** são usadas para que o algoritmo aprenda padrões por meio da agregação de informações entre os nós vizinhos (no nosso contexto, isso é útil para analisar a dependência entre dois serviços diferentes).


- **Para Logs**: Uso da ferramenta de parsing open-source **Drain3** para template de logs, permitindo o agrupamento das mensagens que estão em um mesmo modelo.

  - **Exemplo** 
    - Imagine que a aplicação começa a soltar logs de falha de login sem parar: `User 123 failed to login at 10:01`, `User 999 failed to login at 10:01`. 
    
    - Por meio de uma ferramenta de parsing como o Drain3, é possível definirmos um template para os logs, que nesse caso seria: `User <*> failed to login at xx:xx`
    
    - A partir disso, o parser começa a contar a ocorrência desse padrão na saída de logs, gerando uma nova métrica útil na análise da IA.

  - É descrito no texto que o Drain3 faz o processamento apenas do padrão do log, e entrega para a IA apenas este padrão limpo e quantas vezes ele ocorreu (ao invés de mandar um log gigantesco cheio de informações que podem causar dispersão), isso permite que o modelo de linguagem entenda o real significado do evento.

- O artigo também destaca a necessidade do **processamento intra-modal** (ou seja, o processamento de cada modalidade de forma independente) para que o modelo de linguagem consiga extrair informações da maneira mais precisa possível, e então as correlacione.