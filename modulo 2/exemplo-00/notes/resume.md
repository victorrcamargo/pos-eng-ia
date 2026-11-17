# O que concluímos do módulo?

---

## Machine Learning e IA

* **IA:** A forma que se usa para ensinar uma máquina a executar uma tarefa.
* IAs são algoritmos que aprendem com dados. Estes algoritmos que aprendem com dados são chamados de **Machine Learning**.
* **ML** é uma subárea de IA que usa algoritmos para entender padrões em dados e prever resultados.
* **Deep Learning** trabalha com camadas de processamento para analisar dados e tomar decisões mais inteligentes.

> [!NOTE]
> **Em resumo:** IA refere-se a sistemas que podem se comportar de forma inteligente e aprender como humanos.

* A interface de um modelo de inteligência como ChatGPT / Claude / Gemini funciona como um agregador de modelos de Machine Learning: ele entende sua intenção e processa usando o melhor modelo para responder uma determinada pergunta.
* Usar Deep Learning para vencer em jogos e reconhecer faces direto no navegador é algo que atualmente o ecossistema JS se destaca e viabiliza diretamente na aplicação cliente.
* O foco do curso foi dominar **tensores**, **redes neurais**, **neurônios**, além de **criar e treinar uma rede neural do zero** utilizando **TensorFlow.js**.

---

## Conceitos de Redes Neurais

* O **TensorFlow** trabalha com **tensores**, que são vetores (listas em JS) cujo conteúdo é representado por números para serem usados por algoritmos que aprendem com padrões de dados.
* **Exemplo de aplicação:** Em um array de pessoas, cada pessoa está em uma categoria. Dadas as propriedades de uma nova pessoa, você quer determinar em qual categoria ela mais se encaixa baseado no histórico do banco de dados.
* O TensorFlow **não entende** objetos literais ou instâncias primitivas (`string`, `boolean`, etc.); precisamos converter tudo para uma estrutura matemática composta apenas por números (tensor).
* **Exemplo prático de conversão:** Para transformar um array de pessoas em um tensor, criamos uma representação tabular e atribuímos pontos/pesos para as propriedades que estão ativas:
  * Se o Erick gosta de azul $\rightarrow$ `azul = 1`, `vermelho = 0`, `verde = 0`.
  * Se a Ana gosta de vermelho $\rightarrow$ `azul = 0`, `vermelho = 1`, `verde = 0`.
  * Se o Carlos gosta de verde $\rightarrow$ `azul = 0`, `vermelho = 0`, `verde = 1`.

### Tabela de Categorização:

| Pessoa | Azul | Vermelho | Verde |
| :--- | :---: | :---: | :---: |
| **Erick** | `1` | `0` | `0` |
| **Ana** | `0` | `1` | `0` |
| **Carlos** | `0` | `0` | `1` |

* Em termos práticos isso se chama **categorização (*One-Hot Encoding*)**, convertendo o array de pessoas em um array de arrays (tensor bidimensional composto estritamente por números).
* Sempre que for trabalhar com aprendizado de máquina (ML), deve acontecer a etapa de **normalização** e **conversão de dados para tensores**, viabilizando à máquina aprender padrões, prever situações e recomendar produtos.

---

## Criando e Treinando Redes Neurais

> **Código de exemplo prático:** [index.js](../index.js)

### Fundamentos Principais do Código

#### 1. Pré-processamento de Dados (Redes neurais só entendem números)
Como a rede não processa textos diretamente (`"verde"`, `"Curitiba"`), os dados passaram por duas transformações fundamentais:

* **Normalização (Min-Max):** A idade foi convertida para uma escala entre `0` e `1` (ex: 28 anos virou `0.2`). Isso evita que números com grandezas maiores tenham peso desproporcional no aprendizado.
* **One-Hot Encoding:** Variáveis categóricas (texto) foram transformadas em posições binárias (`0` ou `1`):
  * Cores (`azul`, `vermelho`, `verde`) $\rightarrow$ 3 posições.
  * Cidades (`SP`, `RJ`, `Curitiba`) $\rightarrow$ 3 posições.
  * **Entrada final ($X$):** 1 vetor com 7 posições: `[idade, azul, vermelho, verde, SP, RJ, Curitiba]`.
  * **Saída esperada ($Y$):** Vetor com 3 posições representando as classes (ex: `premium = [1, 0, 0]`).

#### 2. Tensores (`tf.tensor2d`)
* Os arrays padrão do JavaScript foram convertidos em Tensores (`inputXs` e `outputYs`).
* Tensores são matrizes otimizadas para operações matemáticas vetorizadas e cálculo acelerado de redes neurais.

#### 3. Arquitetura da Rede (`tf.sequential`)
A rede foi construída em camadas sequenciais (*feedforward*):

* **Camada Oculta (`Dense` com 80 neurônios):** Recebe as 7 entradas (`inputShape: [7]`).
  * **Ativação `relu`:** Adiciona não-linearidade ao modelo. Se o cálculo do neurônio for nulo ou negativo, ela zera; se for positivo, propaga para frente. Permite à rede aprender padrões e correlações mais complexas.
* **Camada de Saída (`Dense` com 3 neurônios):** Um neurônio para cada classe (`premium`, `medium`, `basic`).
  * **Ativação `softmax`:** Converte os valores brutos calculados em uma distribuição de probabilidades contínuas (a soma das 3 saídas sempre totaliza $1.0$ ou $100\%$).

#### 4. Treinamento (`compile` e `fit`)
* **Otimizador (`adam`):** Algoritmo que ajusta os pesos e conexões da rede a cada iteração para minimizar os erros cometidos.
* **Função de Perda (`loss: categoricalCrossentropy`):** Métrica padrão para problemas de classificação exclusiva. Ela mede a distância matemática entre a previsão da rede e o resultado real esperado.
* **Épocas (`epochs: 100`):** Quantidade de vezes em que a rede analisa o conjunto completo de dados para calibrar seus pesos internos.
* **`shuffle: true`:** Embaralha os dados a cada época para evitar que o modelo aprenda vícios baseados na ordem das linhas.

#### 5. Inferência / Predição (`predict`)
* Após o treinamento, o modelo recebe os dados normalizados de um novo indivíduo (*José*).
* A função `model.predict()` gera as probabilidades de cada classe.
* O código classifica e exibe ordenadamente o grau de certeza percentual para cada categoria (do mais provável para o menos provável).