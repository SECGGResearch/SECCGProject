# CTI Article Classification

## Introduction

The SECCG project aims to automatically extract and categorize cyber threat intelligence (CTI) from unstructured text sources. This involves two main steps:

1. **CTI Sentence Identification**: Classify sentences from CTI articles to identify and label different types of CTI sentences, specifically tagging them with cyber attack tactics and behaviors using NLP models.

2. **CTI Article Classification**: Aggregate the sentence-level classifications to classify entire articles as CTI or non-CTI articles using deep learning models.

We utilize the Python library **simpletransformers** to perform model training and inference in both steps.

## Workflow

### 1. CTI Sentence Identification

- **Manual Labeling and Initial Model Training**:
  - Manually label cyber threat sentences from selected articles to create an initial dataset (`CTI_sentences.xlsx`).
  - Use the `CTI Sentence Identification Model Training.ipynb` notebook to train an initial sentence classification model.

- **Active Learning with Uncertainty Sampling**:
  - For specific cyber tactics where the model shows poor performance, use `Active learning uncertainty sampling.ipynb` to selectively extract sentences, label them, and update `CTI_sentences.xlsx`.
  - Repeat the process to improve model performance through active learning.

- **Final Model Training**:
  - After iterative active learning, retrain the high-performance sentence classification models using `CTI Sentence Identification Model Training.ipynb`.

### 2. CTI Article Classification

- **Sentence Classification and Feature Extraction**:
  - Use the trained CTI Sentence Identification models to label each sentence in all articles with tactics and behavior labels.
  - Calculate statistics for sentences, Indicator of Compromise (IOC) words, and tactics in each article.
  - Embed the CTI sentences in each article into vectors using the `RepresentationModel` from `simpletransformers`.
  - Apply PCA to reduce the dimensionality of the sentence embeddings.
  - Save the aggregated article data into `article.csv`.

- **Model Training and Evaluation**:
  - Use the `Article Embedding and Classification.ipynb` notebook to load the article data and related functions.
  - Train a Deep Neural Network (DNN) model using the aggregated article vectors.
  - Evaluate model performance and adjust the classification threshold to achieve the best balance between precision and recall.

## CTI Sentence Identification

### Introduction

The goal is to train classification models to automatically identify and label CTI sentences within articles. Each sentence is labeled with cyber attack tactics and behaviors, enabling fine-grained analysis of CTI content.

### Dataset and Labeling

- **Data Collection**:
  - Labeled 8,408 sentences from 672 articles in the ATT&CK knowledge base.
  - Used active learning to label an additional 9,012 sentences from 5,110 CTI articles.
  - Total of 17,420 sentences labeled with cyber attack tactics.
  - Further labeled 1,023 sentences describing attack behaviors and 940 negative samples.

- **Data Structure**:
  - `CTI_sentences.xlsx` contains three columns:
    - **Sentence**: The text of the sentence.
    - **Tactics**: The cyber attack tactic labels.
    - **Behavior**: The cyber attack behavior labels.

- **Label Distribution**:
  - The dataset includes sentences with varying numbers of attack tactic labels.
  - **Attack Tactics Distribution**:

    | Number of Attack Tactics | Sentence Count |
    |--------------------------|---------------:|
    | 0                        |           4,022|
    | 1                        |           8,437|
    | 2                        |           4,220|
    | 3                        |             673|
    | 4                        |              37|
    | 5                        |              10|
    | 6                        |               1|

### Active Learning and Uncertainty Sampling

- Employed active learning to improve model performance, particularly for tactics with fewer labeled samples.
- **Uncertainty Measures**:
  - **Classification Uncertainty**: \( 1 - p_{\text{max}} \)
  - **Classification Margin**: \( p_{\text{max}} - p_{\text{next max}} \)
  - **Classification Entropy**: \( -\sum_{i=1}^{n} p_i \log_2 p_i \)
- **Process**:
  - Select sentences with high uncertainty scores.
  - Manually label these sentences and update the dataset.
  - Retrain the model with the expanded dataset.

### Model Performance

- **Improvement Through Active Learning**:
  - The tactic model achieved an average **88% precision** and **90% recall** after active learning.
  - The behavior model achieved an average **83% precision** and **82% recall**.
- **Performance Metrics Before and After Active Learning**:

  | Attack Tactic       | Precision Before | Recall Before | Precision After | Recall After |
  |---------------------|------------------|---------------|-----------------|--------------|
  | Initial Access      | 0.67             | 0.75          | 0.88            | 0.89         |
  | Execution           | 0.17             | 0.11          | 0.90            | 0.92         |
  | Defense Evasion     | 0.50             | 0.14          | 0.85            | 0.86         |
  | Command and Control | 0.05             | 0.06          | 0.84            | 0.87         |
  | Privilege Escalation| 0.35             | 0.39          | 0.89            | 0.90         |
  | Persistence         | 0.00             | 0.00          | 0.91            | 0.93         |
  | Lateral Movement    | 0.00             | 0.00          | 0.90            | 0.91         |
  | Data Leak           | 0.18             | 0.12          | 0.88            | 0.89         |
  | Exfiltration        | 0.00             | 0.00          | 0.84            | 0.89         |
  | Impact              | 0.00             | 0.00          | 0.94            | 0.92         |
  | **Macro Average**   | **0.21**         | **0.17**      | **0.88**        | **0.90**     |

### Usage

- **Pre-trained Models**:
  - Download `tactic model.zip` and `behavior model.zip` from our [Hugging Face repository](https://huggingface.co/CTIKR/CTIKR/tree/main).
  - Unzip the models in the same folder as the notebooks.

- **Running the Model**:
  - Open `CTI Sentence Identification Model Training.ipynb`.
  - Set `my_best_model_dir` to `'./model'`.
  - Run the **Setup** and **Evaluation** cells.
  - The variable `y_pred` will contain the list of inference results.

- **Training a New Model**:
  - Input the training data as `sentence.pkl` in the **Setup** cell.
  - Run the **Training** cell.
  - The model uses k-fold cross-validation.
  - Trained models are saved in the `clfmodel/k/best_model` folders.

### Requirements

- `simpletransformers >= 0.63.9`

## CTI Article Classification

### Introduction

Building upon the sentence-level classifications, we transform each CTI article into a feature vector that represents its content. This vector includes sentence statistics, IOC word counts, attack tactic distributions, and embeddings of CTI sentences. A Deep Neural Network (DNN) model then classifies the article as CTI or non-CTI.

### Feature Vector Composition

- **Overall Sentence Statistics**
- **IOC Words Statistics**
- **Attack Tactics Statistics**
- **Sentence Classification Results Embedding**
  - Generated using the `encode_sentences()` function from `simpletransformers.language_representation.RepresentationModel`.

### Dataset and Evaluation

- **Data Collection**:
  - Labeled 3,588 articles, with 1,799 CTI articles and 1,789 non-CTI articles.

- **Model Structure**:

  <p align="center">
    <img src="https://i.imgur.com/A5d184e.png" alt="DNN Structure">
  </p>
  <p align="center">
    <b>Figure: DNN Structure</b>
  </p>

- **Performance**:
  - Adjusted the threshold to optimize recall and precision.
  - Achieved **90.5% recall** and **75.7% precision** at a threshold of 0.2.

  <p align="center">
    <img src="https://i.ibb.co/d40Mwh8/articlecruve.png" alt="Performance Curve">
  </p>
  <p align="center">
    <b>Figure: Precision, Recall, and F1-score at Different Thresholds</b>
  </p>

### Workflow

1. **Sentence Classification and Embedding**:
   - Use the **CTI Sentence Identification** models to label sentences with tactics and behavior labels.
   - Calculate statistics and embed CTI sentences into vectors.
   - Apply PCA for dimensionality reduction.
   - Save features as `article.csv`.

2. **Model Training and Evaluation**:
   - Run the **Functions** cell in `Article Embedding and Classification.ipynb` to load related functions.
   - Load data using the **Load the Data** cell.
   - Train the DNN model using the **Model Training and Evaluation** cell.
   - Plot performance curves and select the optimal threshold.

## Tool Implementation

### Step-by-Step Instructions

1. **Environment Setup**:
   - Install required packages:
     ```bash
     pip install simpletransformers==0.63.9
     ```

2. **Download Pre-trained Models**:
   - Obtain `tactic model.zip` and `behavior model.zip` from our [Hugging Face repository](https://huggingface.co/CTIKR/CTIKR/tree/main).
   - Unzip them in your working directory.

3. **CTI Sentence Identification**:
   - Open `CTI Sentence Identification Model Training.ipynb`.
   - Modify `my_best_model_dir` to `'./model'`.
   - Run the **Setup** and **Evaluation** cells.
   - Predictions are stored in `y_pred`.

4. **Compute Article Features**:
   - Use the predictions to calculate article-level statistics and embeddings.
   - Save these features to `article.csv`.

5. **CTI Article Classification**:
   - Open `Article Embedding and Classification.ipynb`.
   - Run the **Functions** and **Load the Data** cells.
   - Execute the **Model Training and Evaluation** cell to train the DNN model.
   - Adjust the threshold based on performance metrics.

### Requirements

- `simpletransformers >= 0.63.9`
- Additional dependencies as specified in your project environment.

## Conclusion

By following this workflow, you can classify CTI articles effectively using sentence-level classifications and deep learning models. The active learning approach significantly enhances model performance, particularly for tactics with initially limited data. The provided tools and models facilitate replication and adaptation for different datasets.

---

**Note**: Ensure you have all the necessary data files and follow licensing agreements when using the models and datasets provided.
