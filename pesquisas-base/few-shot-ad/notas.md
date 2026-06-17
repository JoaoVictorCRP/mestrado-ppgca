# O artigo

- O artigo *"Few-shot Multimodal Anomaly Detection via DynamicIntra-modal Sparsity Attention and Quality-awareCross-modal Fusion in Microservice System"* apresenta o FuseGuard, um framework aprimorado por LLM que pioneira a fusão multimodal consciente da qualidade para detecção de anomalias (AD) em sistemas de microsserviços. O FuseGuard aborda os desafios críticos do cenário de poucos disparos, como a extração eficaz de recursos intra-modais e o alinhamento de representações multimodais heterogêneas. Ele utiliza mecanismos avançados, como atenção de correlação esparsa dinâmica para métricas, codificação espaço-temporal para rastreamentos e codificação semântica-frequência aprimorada por LLM para logs. Além disso, o FuseGuard introduz um mecanismo de fusão consciente da qualidade que pondera dinamicamente as modalidades com base na estimativa de incerteza. Avaliado em várias plataformas e um sistema de produção.

## O problema

- O artigo destava os desafios no consumo de dados multimodais e o estabalecimento da correlação entre eles.

- Em especial, dois desafios são citados:
  1. Exploração suficiente de dados multimodais: Métodos existentes falham em modelar dependências multimodais complexas em métricas (gerando falsas correlações ou ignorando o domínio da frequência) e tratam logs apenas em nível estatístico superficial, ignorando pistas contextuais

  2. Alinhamento e fusão multimodal robustos: Existe uma disparidade inerente entre as modalidades (métricas e traces têm estrutura temporal precisa, enquanto logs possuem riqueza semância mas organização temporal imprecisa). Além disso, os dados reais sofrem com discrepâncias de qualidade devido a erros de transmisssão e ruídos, gerando corrupção nas representações quando funidas de maneira indiscriminada.

