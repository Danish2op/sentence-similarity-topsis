
# Sentence Similarity Model Evaluation and TOPSIS Ranking

This project evaluates and ranks various sentence similarity models based on their performance using the STS Benchmark dataset. It leverages a custom-built Python package `topsis102203633Danishsharma` (available on PyPI: [topsis102203633Danishsharma](https://pypi.org/project/topsis102203633Danishsharma/)) to rank the models using the TOPSIS (Technique for Order of Preference by Similarity to Ideal Solution) method. 

The evaluation involves computing cosine similarity between sentence embeddings generated by multiple pre-trained models and comparing these with human-annotated similarity scores. The models are then ranked based on their performance using multiple metrics like Pearson correlation, Spearman correlation, Mean Absolute Error (MAE), and Mean Squared Error (MSE).

Finally, the models are ranked based on their performance using the **TOPSIS** method, which considers the relative importance of different evaluation metrics.

## Table of Contents
- [Introduction](#introduction)
- [Dataset](#dataset)
- [Models Evaluated](#models-evaluated)
- [Evaluation Metrics](#evaluation-metrics)
- [TOPSIS Method](#topsis-method)
- [How It Works](#how-it-works)
- [Results](#results)
- [Explanation of Weights and Biases](#explanation-of-weights-and-biases)
- [Graph of Results](#graph-of-results)
- [Usage](#usage)
- [License](#license)

## Introduction

This project evaluates multiple sentence similarity models and ranks them using the TOPSIS method. The models are evaluated based on their performance on the **STS Benchmark** dataset. The similarity score for each model is computed by calculating cosine similarity between sentence embeddings, and the performance is compared using multiple evaluation metrics.

### Models Evaluated

The following pre-trained models from the `sentence-transformers` library were evaluated in this project:

1. **all-MiniLM-L6-v2**
2. **paraphrase-MiniLM-L12-v2**
3. **sentence-t5-base**
4. **distilbert-base-nli-stsb-mean-tokens**
5. **bert-base-nli-mean-tokens**

These models were selected for their ability to generate sentence embeddings, which are crucial for measuring sentence similarity.

## Evaluation Metrics

The models were evaluated using the following metrics:

- **Pearson Correlation**: Measures the linear correlation between the predicted similarity scores and the actual gold scores.
- **Spearman Correlation**: Measures the monotonic relationship between the predicted similarity scores and the actual gold scores.
- **Mean Absolute Error (MAE)**: Measures the average of the absolute differences between predicted and actual similarity scores.
- **Mean Squared Error (MSE)**: Measures the average of the squared differences between predicted and actual similarity scores.

```python
pearson = pearsonr(gold_scores[:1000], cos_sims)[0]
spearman = spearmanr(gold_scores[:1000], cos_sims)[0]
mae = mean_absolute_error(gold_scores[:1000], cos_sims)
mse = mean_squared_error(gold_scores[:1000], cos_sims)
```

Each model’s performance is compared based on these metrics.

## TOPSIS Method

The **TOPSIS** method is used to rank the models based on their evaluation metrics. TOPSIS helps in determining which models perform better in comparison to others by calculating a "Topsis Score" based on a set of criteria (here, the evaluation metrics) and determining the rank of each model.

### Weights and Impacts

Weights were assigned to each evaluation metric to reflect their relative importance in the ranking process. The impacts indicate whether a higher or lower value of each metric is desirable.

- **Weights**: These reflect the importance of each metric in the ranking process. For instance, **Pearson** and **Spearman** correlations have a higher weight because the relationship between predicted and actual similarity is more important than the error metrics (MAE, MSE).
  
    - **Pearson** and **Spearman** have a weight of 0.3 each.
    - **MAE** has a weight of 0.1.
    - **MSE** has a weight of 0.2.
    - **Inference Time** has a weight of 0.1.

```python
weights = [0.3, 0.3, 0.1, 0.2, 0.1]
```

- **Impacts**: The direction of optimization for each metric.
    - A **higher value** is better for **Pearson** and **Spearman** because we want the predicted similarity scores to correlate strongly with the actual scores.
    - A **lower value** is better for **MAE** and **MSE** because we want the errors to be minimized.
  
```python
impacts = ['+', '+', '-', '-', '-']
```

The custom `Topsis` class from the `topsis102203633Danishsharma` package (available on [PyPI](https://pypi.org/project/topsis102203633Danishsharma/)) is used to calculate the final TOPSIS scores.

```python
topsis = Topsis(results_df, weights, impacts, distance_metric='euclidean')
topsis_result = topsis.calculate()
```

## How It Works

### 1. **Sentence Embedding Generation**: 
Each model generates embeddings for sentence pairs from the dataset using the `sentence-transformers` library.

### 2. **Cosine Similarity Calculation**:
The cosine similarity between the embeddings for each sentence pair is calculated using the formula:
```python
cos_sims = np.sum(embeddings1 * embeddings2, axis=1) / (
    np.linalg.norm(embeddings1, axis=1) * np.linalg.norm(embeddings2, axis=1)
)
```

### 3. **Evaluation Metrics Calculation**:
For each model, we compute the Pearson and Spearman correlations, MAE, and MSE with respect to the gold similarity scores.

### 4. **TOPSIS Ranking**:
The performance metrics (Pearson, Spearman, MAE, MSE) for each model are passed into the TOPSIS method to compute the "Topsis Score" and rank the models based on their performance.

## Results

The results for each model are stored in the file `model_comparison_results.csv`. The columns in this CSV file are:

- **Model**: The name of the model.
- **Pearson**: Pearson correlation coefficient.
- **Spearman**: Spearman rank correlation coefficient.
- **MAE**: Mean Absolute Error.
- **MSE**: Mean Squared Error.
- **InferenceTime**: Time taken by the model for inference.
- **TOPSIS Score**: Final score based on the TOPSIS method.
- **Rank**: Final rank of the model based on the TOPSIS score.

The models are ranked from best to worst based on the TOPSIS scores.

## Graph of Results

The following graph displays the TOPSIS ranking of the sentence similarity models. The **x-axis** represents the model names, and the **y-axis** represents the TOPSIS score. Higher scores indicate better performance.

![TOPSIS Ranking of Sentence Similarity Models](topsis_ranking_plot (1).png)

## Usage

### Prerequisites

Before running the code, make sure to install the required dependencies:

```bash
pip install -r requirements.txt
```

Where `requirements.txt` should contain:

```
numpy
pandas
matplotlib
scipy
sklearn
sentence-transformers
datasets
topsis102203633Danishsharma
```

### Running the Evaluation

To run the evaluation script, follow these steps:

1. Clone the repository:
    ```bash
    git clone https://github.com/Danish2op/sentence-similarity-topsis.git
    cd sentence-similarity-topsis
    ```

2. Run the notebook:
    ```bash
    python evaluate_models.ipynb
    ```

3. The results will be saved in `model_comparison_results.csv`, and the plot will be saved as `topsis_ranking_plot.png`.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
