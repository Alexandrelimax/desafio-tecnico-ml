# 🎓 Student Performance — Desafio Técnico de Machine Learning

Este repositório contém a solução para um **desafio técnico de Machine Learning**, cujo objetivo é
**analisar fatores associados ao desempenho escolar** e construir um **modelo baseline de classificação**
para prever a **aprovação de estudantes**, utilizando dados públicos.

O projeto foi desenvolvido com foco em:
- Clareza analítica
- Storytelling orientado por dados
- Pipeline de ML reprodutível
- Simplicidade e boas práticas

---

## 📊 Dataset

- Fonte: **Kaggle**
- Dataset: *Student Performance Dataset*
- Link:  
  https://www.kaggle.com/datasets/dskagglemt/student-performance-data-set?select=student-mat.csv

O dataset original é proveniente do **UCI Machine Learning Repository** e contém informações
demográficas, socioeconômicas e educacionais de estudantes do ensino médio em Portugal.

### Principais grupos de variáveis
- Educação: tempo de estudo, reprovações, notas
- Família: escolaridade dos pais, suporte familiar
- Infraestrutura/contexto: acesso à internet, tempo de deslocamento
- Comportamento: faltas, atividades extracurriculares

---

## 🎯 Objetivo do projeto

1. **Explorar e entender os dados** por meio de análise exploratória e visualizações (storytelling).
2. **Definir um problema de Machine Learning claro e interpretável**.
3. **Construir um modelo baseline de classificação** para prever aprovação/reprovação.
4. **Salvar o modelo treinado** em formato reutilizável (`joblib`).

---

## 🧠 Definição do problema de Machine Learning

- Tipo: **Classificação binária**
- Target criado:
  - `target_pass = 1` → aluno aprovado (`G3 >= 10`)
  - `target_pass = 0` → aluno reprovado (`G3 < 10`)

As notas intermediárias (`G1`, `G2`) **não são utilizadas como features** no modelo baseline
para evitar **Data Leakage** (vazamento de informação).

---

## 📁 Estrutura do repositório

```text
DESAFIO_TECNICO_ML/
│
├── artifacts/
│   └── model_baseline_rf.joblib
├── data/
│   └── student-mat.csv 
├── notebooks/
│   ├── 01_storytelling_student_performance.ipynb
│   └── 02_model_baseline.ipynb
├── .gitignore
├── README.md
├── requirements.txt
└── .venv/ 
```
---

## 8. Próximos passos - Melhorias do modelo

Possíveis extensões naturais deste trabalho incluem:
- engenharia de features avançada
- validação cruzada
- tuning de hiperparâmetros
- modelos mais complexos
- deploy e monitoramento

---

## 9. Próximos passos — Ciclo de Vida do Modelo e MLOps (Visão GCP)

Além das evoluções analíticas e de modelagem, um próximo passo natural
para este projeto é a estruturação do **ciclo de vida completo do modelo**
sob uma perspectiva de **MLOps**, considerando escalabilidade, custo
e governança.

Abaixo descrevemos possíveis extensões arquiteturais no contexto do
Google Cloud Platform (GCP).

---

### 9.1 Orquestração e pipelines de treinamento

A etapa de treinamento e re-treinamento do modelo pode ser orquestrada
por pipelines automatizados, de acordo com a complexidade do cenário.

Possíveis abordagens:

- **Vertex AI Pipelines**  
  - Adequado para pipelines de ML gerenciados
  - Integração nativa com experiments, artefatos e model registry
  - Indicado para cenários com maior maturidade de ML

- **Apache Airflow**  
  - Para maior controle e flexibilidade da DAG
  - Pode ser executado via:
    - **Cloud Composer** (serviço gerenciado)
    - **Airflow em Compute Engine**, quando o custo for fator decisivo
  - Permite integrar facilmente etapas como:
    - ingestão de dados
    - feature engineering
    - treinamento
    - avaliação
    - persistência de artefatos

A escolha entre essas abordagens depende do equilíbrio entre
**custo operacional, complexidade e necessidade de governança**.

---

### 9.2 Armazenamento e processamento de dados

Considerando o volume e a natureza dos dados, o
**BigQuery** se apresenta como um candidato natural para:

- armazenamento analítico dos dados brutos e tratados
- execução de transformações em larga escala
- criação de camadas (raw, refined, curated)

Nesse contexto:

- Os dados poderiam ser ingeridos como tabelas externas ou nativas
- O dataset consolidado para modelagem poderia ser versionado no BigQuery
- O BigQuery serviria como fonte única de verdade para múltiplos modelos

---

### 9.3 Feature engineering e governança de transformações

Para cenários em que o **BigQuery** seja o principal motor analítico,
o **Dataform** surge como ferramenta adequada para:

- versionamento de transformações SQL
- definição de dependências entre tabelas
- execução incremental
- integração com CI/CD

Nesse modelo:
- o ciclo de vida dos dados é tratado no Dataform
- a governança das features ocorre antes da modelagem
- mudanças estruturais passam por versionamento e revisão

---

### 9.4 Estratégias de modelagem: BigQuery ML vs modelos customizados

Duas estratégias distintas podem ser consideradas:

#### Modelos com BigQuery ML
- Treinamento e inferência executados diretamente no BigQuery
- Ciclo de vida do modelo gerenciado pela própria plataforma
- Integração natural com Dataform e pipelines SQL
- Menor esforço operacional

#### Modelos customizados (scikit-learn, etc.)
- Maior flexibilidade algorítmica
- Treinamento orquestrado via Airflow ou Vertex Pipelines
- Persistência de artefatos (ex: `joblib`) em:
  - Artifact Registry
  - Cloud Storage
- Deploy via:
  - Vertex AI
  - Cloud Run
  - serviços customizados

A escolha depende do grau de customização e controle desejado.

---

### 9.5 CI/CD e versionamento de artefatos

Para modelos customizados, o ciclo de CI/CD pode ser estruturado com:

- **GitHub** como repositório de código
- **Cloud Build** para:
  - execução de testes
  - treinamento automatizado
  - validação de métricas
  - geração de artefatos
- **Artifact Registry** para versionamento de:
  - imagens Docker
  - modelos treinados
  - dependências

Essa abordagem permite:
- reprodutibilidade
- rastreabilidade
- rollback controlado de modelos

---

### 9.6 Monitoramento do modelo em produção

Após o deploy, o modelo deve ser monitorado continuamente para garantir
qualidade e confiabilidade.

Aspectos importantes incluem:

- **Model Monitoring**
  - detecção de data drift
  - detecção de prediction drift
  - acompanhamento de métricas ao longo do tempo
  - disparo de e-mails

- **Monitoramento operacional**
  - latência
  - taxa de erro
  - custo de inferência

No GCP, essas capacidades podem ser implementadas via:
- Vertex AI Model Monitoring
- Cloud Monitoring e Logging
- métricas customizadas

---

### 9.7 Considerações finais sobre MLOps

A incorporação de práticas de MLOps transforma este projeto de um
exercício analítico em um **sistema de ML sustentável**, capaz de:

- evoluir com novos dados
- manter governança e controle
- equilibrar custo e complexidade
- operar de forma confiável em produção

Essa etapa não faz parte do escopo do desafio técnico,
mas representa uma **evolução natural** do trabalho desenvolvido.


**Projeto desenvolvido por Alexandre de Almeida Lima para desafio técnico.**
