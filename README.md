# SCTEC-MNIST-Classification

Projeto de classificação de imagens de dígitos manuscritos utilizando a base **MNIST** e algoritmos de aprendizado de máquina.

O projeto foi desenvolvido como aplicação prática de técnicas de classificação multiclasse, envolvendo preparação dos dados, normalização, ajuste de hiperparâmetros, treinamento, validação, avaliação em conjunto de teste independente, análise de erros, experimento OOD (Out-of-Distribution) e classificação de imagens próprias.

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

A configuração selecionada durante o processo de validação foi:

```text
n_neighbors = 3
weights = distance
```

### Random Forest

O Random Forest utiliza um conjunto de árvores de decisão para realizar a classificação.

A configuração selecionada foi:

```text
n_estimators = 150
max_depth = 20
```

### MLP

O Multi-Layer Perceptron é uma rede neural artificial utilizada para realizar a classificação das imagens.

A configuração selecionada foi:

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

Além do melhor desempenho preditivo, o MLP apresentou tempo de inferência inferior ao KNN no conjunto avaliado.

---

## 7. Análise dos erros

A análise da matriz de confusão permitiu identificar as principais combinações entre classe real e classe prevista.

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

Foram avaliadas:

**2.824 imagens OOD.**

### Confiança média

| Modelo | Confiança média |
| ------ | --------------: |
| MLP    |      **92,55%** |
| KNN    |           91,45 |
