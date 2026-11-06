# 🛡️ Dynamic BioSecurity — Controle de Acesso e Detecção de Anomalias com Redes Neurais & Biometria Comportamental

Sistema avançado de **Autenticação Contínua e Detecção de Anomalias em Tempo Real** utilizando arquitetura híbrida de Deep Learning (Redes Neurais Densas & LSTM) aplicada à **Biometria Comportamental (Keystroke Dynamics)**.

Desenvolvido como projeto de pesquisa e portfólio prático por Alice Miranda (Ciência da Computação - PUC Minas), o sistema substitui barreiras de login estáticas por um monitoramento comportamental contínuo e transparente.

#### 🚧 Status do Projeto: Em desenvolvimento ativo / Pesquisa aplicada iterativa.
**Nota:** Os parâmetros dos modelos e o pipeline de redes neurais estão em constante evolução à medida que os testes e a validação experimental avançam.

## 🎯 Problema de Negócio & Abordagem Científica

Sistemas tradicionais de segurança baseados unicamente em credenciais estáticas (senhas, PINs e MFA via SMS) falham ao combater sequestro de sessão (*session hijacking*), roubo de credenciais por engenharia social ou bots automatizados.

### A Solução

O **Dynamic BioSecurity** transforma a interação física/digital do usuário em um vetor de dados biométricos únicos (a "assinatura invisível" de digitação). A solução avalia continuamente o comportamento do usuário e ajusta a permissão de acesso através de um **Score de Risco Dinâmico**.

```
[ Captura do Teclado ] ──► [ Pré-processamento & Z-Score ]
                                    │
                                    ▼
                 ┌──────────────────────────────────────┐
                 │ REDE 1: Perfilamento & Anomalias     │
                 │ (Deep Neural Network / LSTM)         │
                 └──────────────────┬───────────────────┘
                                    │ Generates Anomaly Score
                                    ▼
                 ┌──────────────────────────────────────┐
                 │ REDE 2: Avaliação de Risco & Decisão │
                 │ (Classificador Adam + BCE Loss)      │
                 └──────────────────┬───────────────────┘
                                    │
       ┌────────────────────────────┼────────────────────────────┐
       ▼                            ▼                            ▼
[ Acesso Aprovado ]        [ Desafio MFA Dinâmico ]    [ Bloqueio Preventivo ]
  (Baixo Risco)                 (Risco Médio)                (Alto Risco)

```

### 📊 Pipeline de Ciência de Dados & Pré-processamento

**1. Extração de Métricas Biométricas (Keystroke Dynamics)**
* **Dwell Time ($T_d$):** Tempo em milissegundos (ms) em que uma tecla permanece pressionada.
* **Flight Time ($T_f$):** Intervalo de tempo (ms) entre a libertação de uma tecla e o acionamento da seguinte.
* **Métricas Contextuais:** Cadência de digitação, ritmo de preenchimento de formulários e telemetria básica da sessão.

**2. Limpeza e Escalonamento**

* **Remoção de Outliers via Z-Score:** Filtro para remover variações externas e pausas físicas anómalas:
  > `|Z| = | (x - μ) / σ | > 3`

* **Normalização Min-Max:** Padronização das variáveis no intervalo $[0, 1]$ para otimização da convergência dos gradientes:
  > `x' = (x - x_min) / (x_max - x_min)`

## 🧠 Arquitetura de Redes Neurais (Abordagem Híbrida)

O pipeline de inferência é composto por duas redes neurais complementares operando em cascata:

### 🔹 Rede 1: Modelo de Perfilamento de Anomalias (Deep Neural Network)

* **Arquitetura:** Camadas Densas (Feedforward) totalmente conectadas com camadas de dropout para prevenir *overfitting*.

* **Função de Ativação:** `ReLU` nas camadas ocultas (captura de não-linearidades do ritmo motor) e `Sigmoid` na camada de saída.

* **Objetivo:** Comparar o vetor de digitação atual com o *baseline* histórico do usuário cadastrado, gerando uma métrica de desvio comportamental.

### 🔹 Rede 2: Engine de Decisão & Classificação de Risco

* **Arquitetura:** Classificador multicamadas conectado a um motor de regras de segurança.

* **Otimizador & Perda:** Otimizador **Adam** (taxa de aprendizado adaptativa) e função de perda **Binary Cross-Entropy**.

* **Objetivo:** Consolidar a taxa de anomalia da Rede 1 com metadados de acesso (horário, sistema e IP) para tomar a decisão de segurança:

  1. **Aprovado:** Acesso transparente sem interrupções.

  2. **MFA Dinâmico:** Solicitação de autenticação adicional.

  3. **Bloqueio:** Interrupção imediata da sessão por suspeita de fraude.

## 📊 Métricas de Desempenho & Validação

O modelo é avaliado com foco em métricas críticas para ambientes bancários e corporativos:

* **Precision & Recall:** Maximização do *Recall* para mitigar Falsos Negativos (impedir invasões) mantendo alta *Precision* para minimizar Falsos Positivos (evitar bloqueios indevidos de usuários legítimos cansados).

* **Curva ROC-AUC & Matriz de Confusão:** Utilizadas no ajuste fino do limiar (*threshold*) de decisão.

## 🛠️ Stack Tecnológica

* **Linguagem Principal:** Python 3.10+

* **Deep Learning & ML:** TensorFlow, Keras, Scikit-Learn

* **Processamento & Análise de Dados:** Pandas, NumPy

* **Visualização:** Matplotlib, Seaborn

* **Arquitetura de Microsserviços:** FastAPI (Endpoints de validação biométrica)

## 📂 Estrutura do Repositório

```
├── notebooks/
│   └── treinamento_redes_neurais.ipynb   # Treinamento e avaliação do modelo
├── src/
│   ├── data_preprocessing.py            # Tratamento de outliers e MinMaxScaler
│   ├── neural_networks.py               # Definição das arquiteturas Keras
│   └── inference_api.py                 # Endpoint FastAPI para validação
├── models/                              # Pesos dos modelos treinados (.h5 / .keras)
├── requirements.txt                     # Dependências do projeto
└── README.md                            # Documentação técnica

```

## 👩‍💻 Autora

**Alice Miranda** — *Estudante de Ciência da Computação na PUC Minas*

* Pesquisadora em IA/ML, Engenheira de Software AI-Native

* 🌐 [LinkedIn](https://linkedin.com/in/alicemirandar) | 🐙 [GitHub](https://github.com/AliceMiranda1)
