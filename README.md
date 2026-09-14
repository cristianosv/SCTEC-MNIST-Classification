# SCTEC-MNIST-Classification

Projeto de classificação de imagens de dígitos manuscritos utilizando a base **MNIST** e algoritmos de aprendizado de máquina.

O projeto foi desenvolvido como aplicação prática de técnicas de classificação multiclasse, envolvendo preparação dos dados, normalização, ajuste de hiperparâmetros, treinamento, validação, avaliação em conjunto de teste independente, análise de erros, experimento OOD (Out-of-Distribution) e classificação de imagens próprias.

O vídeo deste material pode ser acessado através do link https://drive.google.com/file/d/1Cchz-2Kv3wg6m796G7KCtvp9bKXNkuqM/view?usp=sharing

---

## 1. Objetivo

O objetivo do projeto é desenvolver e comparar modelos capazes de classificar imagens de dígitos manuscritos de **0 a 9**, utilizando a base MNIST.

Foram avaliados três algoritmos:

* K-Nearest Neighbors (KNN)
* Random Forest
* Multi-Layer Perceptron (MLP)

Além da comparação de desempenho entre os modelos, o projeto também investiga o comportamento dos classificadores diante de classes que não foram apresentadas durante o treinamento e diante de imagens próprias.

---

## 2. Base de dados

Foi utilizada a base **MNIST**, composta por imagens de dígitos manuscritos.

Cada imagem possui:

* Dimensão: **28 × 28 pixels**
* Tipo: escala de cinza
* Número de características após transformação: **784**
* Classes: **10**, correspondentes aos dígitos de 0 a 9
* Valores originais dos pixels: aproximadamente de 0 a 255

Para utilização nos modelos, cada imagem 28 × 28 foi transformada em um vetor de 784 características.

Os valores dos pixels foram normalizados para o intervalo entre 0 e 1.

---

## 3. Divisão dos dados

Os dados foram divididos de forma estratificada em:

* **70% — treinamento**
* **10% — validação**
* **20% — teste**

A estratificação foi utilizada para preservar a distribuição das classes nos diferentes conjuntos.

O conjunto de teste foi mantido separado durante o processo de ajuste dos hiperparâmetros e utilizado somente na avaliação final dos modelos.

---

## 4. Modelos utilizados

### KNN

O K-Nearest Neighbors classifica uma amostra considerando os exemplos mais próximos no espaço de características.

Configuração selecionada:

```text
n_neighbors = 3
weights = distance
```

### Random Forest

O Random Forest utiliza um conjunto de árvores de decisão para realizar a classificação.

Configuração selecionada:

```text
n_estimators = 150
max_depth = 20
```

### MLP

O Multi-Layer Perceptron é uma rede neural artificial utilizada para realizar a classificação das imagens.

Configuração selecionada:

```text
hidden_layer_sizes = (128, 64)
learning_rate_init = 0.001
```

---

## 5. Ajuste de hiperparâmetros

Cada modelo passou por uma etapa de ajuste utilizando o conjunto de validação.

O objetivo foi comparar diferentes configurações e selecionar aquela que apresentou o melhor desempenho de acordo com o **F1-Score ponderado (weighted)**.

O conjunto de teste não foi utilizado para escolher os hiperparâmetros.

### Configurações selecionadas

| Modelo        | Hiperparâmetros                                            |
| ------------- | ---------------------------------------------------------- |
| KNN           | `n_neighbors=3`, `weights=distance`                        |
| Random Forest | `n_estimators=150`, `max_depth=20`                         |
| MLP           | `hidden_layer_sizes=(128, 64)`, `learning_rate_init=0.001` |

Após a seleção das configurações, os modelos finais foram treinados utilizando os dados de treinamento e validação.

---

## 6. Avaliação dos modelos

A avaliação final foi realizada sobre o conjunto de teste independente utilizando:

* Accuracy
* Precision
* Recall
* F1-Score
* Matriz de confusão
* Análise dos principais erros
* Tempo de inferência

### Resultados

| Modelo        |   Accuracy |  Precision |     Recall |   F1-Score | Tempo de Inferência |
| ------------- | ---------: | ---------: | ---------: | ---------: | ------------------: |
| **MLP**       | **97,85%** | **97,85%** | **97,85%** | **97,85%** |         **0,109 s** |
| KNN           |     97,32% |     97,34% |     97,32% |     97,32% |            45,192 s |
| Random Forest |     96,80% |     96,80% |     96,80% |     96,80% |             0,724 s |

O **MLP apresentou o melhor desempenho geral**, alcançando Accuracy e F1-Score de aproximadamente **97,85%**.

Além do melhor desempenho preditivo, o MLP apresentou tempo de inferência muito inferior ao KNN no conjunto avaliado.

---

## 7. Análise dos erros

A análise das matrizes de confusão permitiu identificar as principais combinações entre classe real e classe prevista.

A maior confusão observada foi:

```text
Classe real: 4
Classe predita: 9
Quantidade: 24
```

Entre as demais confusões relevantes estavam:

* 1 → 5: 11 ocorrências
* 2 → 7: 11 ocorrências
* 3 → 7: 10 ocorrências
* 4 → 5: 10 ocorrências
* 5 → 3: 10 ocorrências
* 6 → 8: 10 ocorrências

Esses erros mostram que algumas formas manuscritas apresentam características visuais semelhantes, dificultando a separação entre determinadas classes.

---

## 8. Experimento OOD — Classes ocultadas

Foi realizado um experimento para avaliar o comportamento dos modelos diante de classes que não estavam presentes durante o treinamento.

As classes:

```text
4 e 7
```

foram completamente retiradas do treinamento.

Dessa forma, os modelos foram treinados utilizando somente:

```text
0, 1, 2, 3, 5, 6, 8 e 9
```

O conjunto de teste OOD foi composto exclusivamente por imagens das classes ocultadas.

Foram avaliadas **2.824 imagens OOD**.

### Confiança média

| Modelo        | Confiança média |
| ------------- | --------------: |
| **MLP**       |      **92,55%** |
| KNN           |      **91,45%** |
| Random Forest |      **56,85%** |

### Análise OOD

Como as classes 4 e 7 não estavam presentes durante o treinamento, os modelos não possuíam uma categoria específica para representar esses dígitos.

Consequentemente, as imagens OOD foram obrigatoriamente atribuídas a uma das classes conhecidas pelo modelo.

Esse comportamento demonstra uma limitação importante dos classificadores tradicionais: uma previsão com alta probabilidade não significa necessariamente que a amostra pertença ao domínio conhecido.

O MLP apresentou confiança média elevada nas imagens OOD, mesmo sem ter sido treinado com as classes 4 e 7. Esse resultado caracteriza um exemplo de **falsa certeza ou overconfidence**, pois o modelo pode apresentar alta confiança em uma classificação incorreta quando recebe uma classe que não conhece.

As matrizes de confusão OOD permitem visualizar para quais classes conhecidas os dígitos ocultados foram direcionados.

---

## 9. Classificação de imagens próprias — Challenge C

Além da avaliação com a base MNIST, foram utilizadas **10 imagens próprias de dígitos manuscritos**.

As imagens foram armazenadas no diretório:

```text
data/minhas_imagens/
```

O processamento aplicado incluiu:

* Conversão para escala de cinza
* Inversão de cores quando necessária
* Identificação da região do dígito
* Recorte da área relevante
* Redimensionamento para 28 × 28 pixels
* Centralização do dígito
* Normalização dos pixels para o intervalo [0,1]

As imagens processadas foram classificadas utilizando o **MLP**, selecionado como melhor modelo no conjunto de teste.

Para cada imagem foram apresentados:

* Imagem processada
* Classe prevista
* Confiança da previsão
* Distribuição das probabilidades entre as 10 classes

A avaliação das imagens próprias é utilizada como experimento complementar, pois trata-se de um conjunto pequeno e produzido fora das condições originais da base MNIST.

---

## 10. Tecnologias utilizadas

* **Python**
* **Jupyter Notebook / Google Colab**
* **NumPy**
* **Pandas**
* **Matplotlib**
* **Seaborn**
* **Scikit-learn**
* **Pillow**
* **Git**
* **GitHub**

---

## 11. Estrutura do projeto

```text
SCTEC-MNIST-Classification/
│
├── .gitignore
├── README.md
├── requirements.txt
│
├── data/
│   └── minhas_imagens/
│       ├── README.md
│       ├── digito_0 (4).png
│       ├── digito_1 (4).png
│       ├── ...
│       └── digito_9 (4).png
│
└── notebook/
    └── Mini_projeto_SCTEC2.ipynb
```

---

## 12. Como executar

### Pré-requisitos

É necessário possuir Python ou acesso ao **Google Colab**.

As dependências utilizadas no projeto estão especificadas no arquivo:

```text
requirements.txt
```

### Execução no Google Colab

1. Acesse o Google Colab.
2. Abra o arquivo:

```text
notebook/Mini_projeto_SCTEC2.ipynb
```

3. Execute as células na ordem apresentada.
4. O notebook realiza o carregamento da base MNIST.
5. Em seguida são executados o pré-processamento, treinamento, validação e avaliação dos modelos.
6. Ao final são realizados os experimentos OOD e a classificação das imagens próprias.

As imagens próprias utilizadas no Challenge C estão disponíveis em:

```text
data/minhas_imagens/
```

---

## 13. Possíveis melhorias

Como possibilidades de evolução do projeto, podem ser consideradas:

* Utilização de técnicas específicas para detecção de OOD.
* Avaliação de calibração das probabilidades dos modelos.
* Utilização de modelos de visão computacional mais avançados, como CNNs.
* Ampliação do conjunto de imagens próprias.
* Aplicação de data augmentation.
* Otimização do tempo de inferência do KNN.
* Avaliação com outras métricas e análises de erro.
* Criação de uma interface para classificação interativa de novos dígitos.

---

## 14. Conclusão

Os experimentos demonstraram que os três modelos são capazes de obter desempenho elevado na classificação do MNIST.

O **MLP apresentou o melhor resultado geral**, com aproximadamente **97,85% de Accuracy e F1-Score**, além de baixo tempo de inferência em comparação ao KNN.

O experimento OOD demonstrou uma limitação relevante: quando recebem classes não observadas durante o treinamento, os modelos continuam produzindo previsões, podendo apresentar alta confiança mesmo quando a classe verdadeira não pertence ao conjunto conhecido.

A classificação das imagens próprias complementou a avaliação, permitindo observar o comportamento do modelo em imagens produzidas fora da base MNIST.

O projeto, portanto, não se limita à comparação de desempenho dos classificadores, mas também aborda aspectos de **generalização, análise de erros, robustez e comportamento diante de dados fora da distribuição conhecida**.

9. Classificação de imagens próprias — Challenge C

Além da avaliação com a base MNIST, foram utilizadas 10 imagens próprias de dígitos manuscritos.

As imagens foram armazenadas no diretório:

data/minhas_imagens/

O processamento aplicado incluiu:

Conversão para escala de cinza
Inversão de cores quando necessária
Identificação da região do dígito
Recorte da área relevante
Redimensionamento para 28 × 28 pixels
Centralização do dígito
Normalização dos pixels para o intervalo [0,1]

As imagens processadas foram classificadas utilizando o MLP, selecionado como melhor modelo no conjunto de teste.

Para cada imagem foram apresentados:

Imagem processada
Classe prevista
Confiança da previsão
Distribuição das probabilidades entre as 10 classes

A avaliação das imagens próprias é utilizada como experimento complementar, pois trata-se de um conjunto pequeno e produzido fora das condições originais da base MNIST.

10. Tecnologias utilizadas
Python
Jupyter Notebook / Google Colab
NumPy
Pandas
Matplotlib
Seaborn
Scikit-learn
Pillow
Git
GitHub
11. Estrutura do projeto
SCTEC-MNIST-Classification/
│
├── .gitignore
├── README.md
├── requirements.txt
│
├── data/
│   └── minhas_imagens/
│       ├── README.md
│       ├── digito_0 (4).png
│       ├── digito_1 (4).png
│       ├── ...
│       └── digito_9 (4).png
│
└── notebook/
    └── Mini_projeto_SCTEC2.ipynb
12. Como executar
Pré-requisitos

É necessário possuir Python ou acesso ao Google Colab.

As dependências utilizadas no projeto estão especificadas no arquivo:

requirements.txt
Execução no Google Colab
Acesse o Google Colab.
Abra o arquivo:
notebook/Mini_projeto_SCTEC2.ipynb
Execute as células na ordem apresentada.
O notebook realiza o carregamento da base MNIST.
Em seguida são executados o pré-processamento, treinamento, validação e avaliação dos modelos.
Ao final são realizados os experimentos OOD e a classificação das imagens próprias.

As imagens próprias utilizadas no Challenge C estão disponíveis em:

data/minhas_imagens/
13. Possíveis melhorias

Como possibilidades de evolução do projeto, podem ser consideradas:

Utilização de técnicas específicas para detecção de OOD.
Avaliação de calibração das probabilidades dos modelos.
Utilização de modelos de visão computacional mais avançados, como CNNs.
Ampliação do conjunto de imagens próprias.
Aplicação de data augmentation.
Otimização do tempo de inferência do KNN.
Avaliação com outras métricas e análises de erro.
Criação de uma interface para classificação interativa de novos dígitos.
14. Conclusão

Os experimentos demonstraram que os três modelos são capazes de obter desempenho elevado na classificação do MNIST.

O MLP apresentou o melhor resultado geral, com aproximadamente 97,85% de Accuracy e F1-Score, além de baixo tempo de inferência em comparação ao KNN.

O experimento OOD demonstrou uma limitação relevante: quando recebem classes não observadas durante o treinamento, os modelos continuam produzindo previsões, podendo apresentar alta confiança mesmo quando a classe verdadeira não pertence ao conjunto conhecido.

A classificação das imagens próprias complementou a avaliação, permitindo observar o comportamento do modelo em imagens produzidas fora da base MNIST.

O projeto, portanto, não se limita à comparação de desempenho dos classificadores, mas também aborda aspectos de generalização, análise de erros, robustez e comportamento diante de dados fora da distribuição conhecida.
