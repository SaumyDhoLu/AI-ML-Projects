# Stock Price Prediction Using Recurrent Neural Networks

## Project Overview

This project explores the use of Recurrent Neural Networks (RNNs) and related sequence models for predicting the closing prices of four technology companies:

- Amazon (AMZN)
- Alphabet / Google (GOOGL)
- IBM (IBM)
- Microsoft (MSFT)

The project investigates how different sequential modeling approaches and time-window configurations affect stock-price prediction performance.

Multiple neural network architectures are explored, including:

- Simple RNN
- LSTM-based RNN
- Multi-output Simple RNN
- GRU

The project also experiments with different sequence-window sizes, strides, model hyperparameters, dropout rates, and batch sizes.

---

## Objective

The primary objective is to investigate whether recurrent neural networks can learn temporal patterns from historical stock-price data and use those patterns to estimate subsequent closing prices.

The project focuses on:

- Preparing historical stock data for sequence modeling.
- Combining multiple stock datasets.
- Scaling numerical features.
- Creating sliding-window sequences.
- Training recurrent neural networks.
- Performing hyperparameter experiments.
- Comparing different recurrent architectures.
- Evaluating predictions using MAE, MSE, RMSE, and R².
- Visualizing actual versus predicted stock prices.

---

## Stocks Used

The analysis includes four stocks:

| Stock | Symbol |
|---|---|
| Amazon | AMZN |
| Alphabet / Google | GOOGL |
| IBM | IBM |
| Microsoft | MSFT |

The individual datasets are merged using the trading date as the common key.

---

## Dataset Preparation

The notebook loads individual CSV files and combines them into a single time-series dataset.

The data-loading workflow:

```
Individual Stock CSV Files
          |
          v
Convert Date to Datetime
          |
          v
Remove Name Column
          |
          v
Prefix Stock-Specific Columns
          |
          v
Merge on Date
          |
          v
Combined Multi-Stock Dataset
```

Each stock's features are renamed using the stock ticker as a prefix.

For example:

```
AMZN_Open
AMZN_High
AMZN_Low
AMZN_Close
AMZN_Volume

GOOGL_Open
GOOGL_High
GOOGL_Low
GOOGL_Close
GOOGL_Volume
```

and similarly for IBM and MSFT.

---

## Exploratory Data Analysis

Before model development, the notebook explores the historical stock data.

The analysis includes:

- Price distributions
- Trading volume
- Time-series behavior
- Correlation between stocks
- Moving averages
- Historical trends

Correlation analysis is used to investigate relationships between the price movements of the four companies.

The notebook observes stronger synchronization between some of the technology stocks, while IBM shows somewhat different behavior.

---

## Sequence Preparation

Because stock prices are time-dependent, the data is transformed into sequential windows before being passed to recurrent neural networks.

The general structure is:

```
Historical Data
      |
      v
Scaling
      |
      v
Sliding Window
      |
      v
X sequences + y targets
      |
      v
RNN / LSTM / GRU
```

The notebook experiments with different:

- Window sizes
- Window strides
- Train/test ratios

A major experiment compares:

```
65-day window
5-day stride
```

with:

```
5-day window
1-day stride
```

The 5-day window and 1-day stride configuration produced substantially stronger reported performance for the multi-stock Simple RNN.

---

## Feature Scaling

The notebook applies scaling before creating the sequential inputs.

The resulting scaled arrays are used to train the recurrent models.

The workflow produces:

```
X_train_scaled
y_train_scaled
X_test_scaled
y_test_scaled
```

along with the corresponding feature and target scalers.

Because the reported MAE, MSE, and RMSE values are calculated on scaled targets in several evaluation sections, those values should not be interpreted directly as dollar-price errors.

---

## Model 1: Simple RNN

The first major modeling approach uses a standard Simple RNN architecture.

The basic structure is:

```
Input Sequence
      |
      v
SimpleRNN
      |
      v
Dropout
      |
      v
Dense Output
```

The model is experimented with using different:

- RNN units
- Dropout rates
- Batch sizes
- Learning rates

### Simple RNN: Single-Target Experiment

The notebook first explores prediction for individual stock targets.

Hyperparameter experiments are performed to identify promising configurations based on R².

The notebook subsequently builds a final advanced recurrent model using a configuration including:

```
Units: 100
Dropout: 0.0
Batch Size: 32
Learning Rate: 0.0001
Epochs: 40
```

Early stopping is used to restore the best validation weights.

---

## Model 2: LSTM-Based Advanced RNN

The project also explores an advanced recurrent architecture using LSTM.

LSTM networks are designed to model sequential dependencies while addressing some of the limitations of basic recurrent networks.

The notebook reports the following performance for the selected advanced LSTM experiment:

| Metric | Reported Value |
|---|---|
| MSE | 0.0015 |
| RMSE | 0.0393 |
| MAE | 0.0288 |
| R² | 0.9875 |

These metrics are calculated on the scaled target values.

The notebook also visualizes the predicted and actual values to assess how closely the model follows the observed test sequence.

---

## Multi-Stock Prediction

The project is extended to predict the closing prices of all four stocks simultaneously.

The target variables are:

- AMZN_Close
- GOOGL_Close
- IBM_Close
- MSFT_Close

The multi-output model produces four predictions for each sequence:

```
                  +--> AMZN_Close
                  |
Input Sequence -->+--> GOOGL_Close
                  |
                  +--> IBM_Close
                  |
                  +--> MSFT_Close
```

### Multi-Output Simple RNN

The multi-output Simple RNN uses:

```
SimpleRNN
    |
Dropout
    |
Dense(4)
```

where the four output neurons correspond to the four target stocks.

### Simple RNN: 65-Day Window

The first multi-stock experiment uses:

```
Window Size: 65 days
Window Stride: 5 days
Train/Test Split: 80/20
```

The notebook reports the following R² values:

| Stock | R² |
|---|---|
| AMZN | 0.7185 |
| GOOGL | 0.9795 |
| IBM | 0.7462 |
| MSFT | 0.8551 |

The combined reported R² is:

```
0.9358
```

The results show substantial variation between stocks.

GOOGL achieves the strongest individual R² in this experiment, while AMZN has the lowest.

### Simple RNN: 5-Day Window

A second sequence configuration uses:

```
Window Size: 5 days
Window Stride: 1 day
Train/Test Split: 80/20
```

The reported results improve considerably:

| Stock | MAE | R² |
|---|---|---|
| AMZN | 0.0746 | 0.9280 |
| GOOGL | 0.0178 | 0.9862 |
| IBM | 0.0324 | 0.8258 |
| MSFT | 0.0246 | 0.9881 |

The notebook reports an overall R² of:

```
0.9848
```

This experiment demonstrates the importance of sequence construction when working with time-series neural networks.

---

## Model 3: GRU

The project also explores a Gated Recurrent Unit (GRU) architecture.

The GRU architecture is:

```
Input Sequence
      |
      v
GRU
      |
      v
Dropout
      |
      v
Dense(4)
      |
      +--> AMZN
      +--> GOOGL
      +--> IBM
      +--> MSFT
```

The GRU configuration is explored using different:

- Number of units
- Dropout rates
- Batch sizes

The notebook evaluates candidate models using the average R² across all four stocks.

### GRU Hyperparameter Search

The notebook searches over:

```
Units:
64
128

Dropout:
0.2
0.3

Batch Size:
8
16
```

Each configuration is trained and evaluated against the four target stocks.

The average R² across the four targets is used as the comparison metric.

### GRU Results

The final reported GRU experiment produces the following results:

| Stock | MAE | R² |
|---|---|---|
| AMZN | 0.0257 | 0.9907 |
| GOOGL | 0.0167 | 0.9880 |
| IBM | 0.0121 | 0.9610 |
| MSFT | 0.0258 | 0.9879 |

The notebook reports an overall combined R² of:

```
0.9961
```

The reported test loss is approximately:

```
0.00023
```

Again, these values are calculated using scaled target data and should be interpreted within the notebook's experimental setup rather than as direct financial-price prediction accuracy.

---

## Model Comparison

The main experiments can be summarized as:

| Model / Configuration | Overall R² |
|---|---|
| Simple RNN — 65-day window, 5-day stride | 0.9358 |
| Simple RNN — 5-day window, 1-day stride | 0.9848 |
| LSTM-based model | 0.9875 |
| GRU — multi-stock | 0.9961 |

These figures are the values reported by the notebook.

The experiments demonstrate how model architecture and sequence-window design can substantially affect the measured performance of recurrent time-series models.

---

## Training Techniques

The notebook uses several techniques to improve training.

### Early Stopping

Early stopping monitors validation loss and restores the best-performing model weights.

```
EarlyStopping
patience = 10
restore_best_weights = True
```

### Learning-Rate Scheduling

Some experiments use:

```
ReduceLROnPlateau
```

to reduce the learning rate when validation loss stops improving.

### Dropout

Dropout is introduced between recurrent and dense layers to experiment with regularization.

---

## Evaluation Metrics

The project evaluates models using:

**Mean Squared Error**
Measures the average squared difference between predicted and actual scaled target values.

**Root Mean Squared Error**
Provides the square root of MSE.

**Mean Absolute Error**
Measures the average absolute prediction error.

**R² Score**
Measures the proportion of variance explained by the predictions.

For multi-stock models, R² is calculated separately for:

- AMZN
- GOOGL
- IBM
- MSFT

and an overall combined R² is also calculated by flattening the predictions and targets.

---

## Visualization

The notebook generates actual-versus-predicted plots for the stock targets.

These visualizations help inspect whether the recurrent models are following:

- Overall trends
- Short-term movements
- Peaks
- Drops
- Relative price patterns

Visual comparison is used alongside numerical evaluation rather than relying exclusively on a single metric.

---

## Saved Models

The repository includes a dedicated folder:

```
saved_models/
```

The saved model artifacts were generated from the model-training code in the notebook and committed to the repository.

This allows the trained neural-network artifacts to be retained alongside the training notebook rather than requiring the models to be recreated from scratch simply to inspect the project structure.

The saved-model directory is therefore part of the reproducibility and portfolio artifact structure.

---

## Project Structure

```
RNN Stock Price Prediction/
│
├── RNN_Assg_Stock_Price_Prediction_Starter_final.ipynb
│
├── saved_models/
│    ├──  AMZN_bilstm1_model.h5/
│    ├── AMZN_bilstm1_model.keras/
│    ├── GOOGL_bilstm1_model.h5/
│    ├── GOOGL_bilstm1_model.keras/             
│    ├── IBM_bilstm1_model.h5/
│    ├── IBM_bilstm1_model.keras/
│    ├── MSFT_bilstm1_model.h5/
│    ├── MSFT_bilstm1_model.keras/   
│    ├── multi_output_5d_bilstm_model.keras/
│    └── multioutput_bilstm.keras/
│
└── README.md
```

The exact filenames inside `saved_models/` should be kept synchronized with the files committed in the GitHub repository.

---

## How to Run

### 1. Install Dependencies

```bash
pip install numpy pandas matplotlib seaborn scikit-learn tensorflow
```

### 2. Add the Stock Datasets

Place the four stock CSV files in the project directory:

```
AMZN.csv
GOOGL.csv
IBM.csv
MSFT.csv
```

### 3. Open the Notebook

```
RNN_Assg_Stock_Price_Prediction_Starter_final.ipynb
```

### 4. Run the Notebook

Execute the notebook sequentially.

The notebook performs:

- Data loading
- Data merging
- Exploratory analysis
- Correlation analysis
- Moving-average analysis
- Scaling
- Window generation
- RNN experiments
- LSTM experiments
- Multi-stock prediction
- GRU experiments
- Evaluation
- Visualization

---

## End-to-End Workflow

```
Historical Stock CSV Files
          |
          v
Data Loading
          |
          v
Date Alignment + Merging
          |
          v
Exploratory Data Analysis
          |
          v
Correlation / Trend Analysis
          |
          v
Feature Scaling
          |
          v
Sliding-Window Sequences
          |
          v
+-----------------------------+
| Recurrent Model Experiments |
+-----------------------------+
      |       |        |
      v       v        v
    RNN     LSTM      GRU
      |       |        |
      +-------+--------+
              |
              v
       Model Evaluation
              |
              v
 Actual vs Predicted Plots
              |
              v
       Saved Model Artifacts
```

---

## Key Findings

### 1. Sequence Window Matters

Changing the sequence construction from:

```
65-day window + 5-day stride
```

to:

```
5-day window + 1-day stride
```

resulted in a substantial increase in the reported overall R² for the Simple RNN experiment.

The reported R² increased from:

```
0.9358
```

to:

```
0.9848
```

### 2. Model Architecture Matters

The notebook shows progressively different results across Simple RNN, LSTM, and GRU architectures.

The GRU experiment produced the highest reported combined R² among the notebook's major multi-stock experiments.

### 3. Different Stocks Behave Differently

The models do not perform equally across all stocks.

For example, in the 65-day Simple RNN experiment:

```
GOOGL: 0.9795
MSFT:  0.8551
IBM:   0.7462
AMZN:  0.7185
```

This highlights that a single recurrent architecture can behave differently across individual time series.

---

## Important Modeling Considerations

### Scaled Metrics

Many evaluation metrics in the notebook are calculated against scaled targets.

Therefore:

- MAE
- MSE
- RMSE

should not be interpreted as dollar-denominated stock-price errors.

A future version could inverse-transform the predictions and calculate metrics directly in the original price scale.

### Test-Set Usage During Experimentation

The notebook repeatedly evaluates candidate configurations against the test set while experimenting with architectures and hyperparameters.

For a stricter machine-learning evaluation pipeline, a future implementation should separate:

- Training Set
- Validation Set
- Test Set

and reserve the final test set for one-time evaluation after model selection.

### Stock Prediction Limitations

High R² on historical test data does not imply that the model can reliably predict future market movements or generate profitable trading strategies.

Stock prices are affected by many factors that are not represented in the historical price sequences used here, including:

- Market-wide events
- Company announcements
- Macroeconomic conditions
- News
- Investor sentiment
- Unexpected events

This project should therefore be viewed as a time-series deep-learning experiment, not as a trading system.

---

## Future Improvements

Potential extensions include:

- Use chronological train/validation/test splits.
- Avoid using the final test set during hyperparameter selection.
- Inverse-transform predictions before reporting price-scale MAE/RMSE.
- Add walk-forward validation.
- Compare against naive forecasting baselines.
- Add GRU/LSTM architecture comparisons using the same evaluation protocol.
- Experiment with bidirectional versus causal architectures where appropriate.
- Add attention mechanisms.
- Include additional technical indicators.
- Incorporate market indices.
- Add sentiment or news features.
- Perform rolling-window evaluation.
- Evaluate directional accuracy in addition to regression metrics.
- Investigate prediction intervals and uncertainty.
- Build an interactive visualization dashboard.

---

## Technologies Used

**Programming**
- Python
- Jupyter Notebook

**Deep Learning**
- TensorFlow
- Keras
- Simple RNN
- LSTM
- GRU
- Dropout
- Early Stopping
- Learning-rate scheduling

**Data Processing**
- Pandas
- NumPy
- Scikit-learn

**Visualization**
- Matplotlib
- Seaborn

**Evaluation**
- Mean Absolute Error
- Mean Squared Error
- Root Mean Squared Error
- R² Score

---

## Skills Demonstrated

### Time-Series Machine Learning
- Sequential data preparation
- Sliding-window generation
- Temporal feature engineering
- Time-series scaling

### Deep Learning
- Recurrent neural networks
- LSTM
- GRU
- Multi-output neural networks
- Dropout regularization
- Early stopping
- Hyperparameter experimentation

### Model Evaluation
- Regression metrics
- Per-stock evaluation
- Combined multi-target evaluation
- Actual-versus-predicted visualization

### Data Analysis
- Correlation analysis
- Moving averages
- Historical trend analysis
- Multi-stock dataset integration

---

## Project Highlights

- Built recurrent neural-network models for four technology stocks.
- Compared Simple RNN, LSTM, and GRU architectures.
- Developed both single-target and multi-target prediction workflows.
- Tested multiple sequence-window configurations.
- Implemented sliding-window time-series preparation.
- Performed hyperparameter experimentation.
- Used early stopping and learning-rate scheduling.
- Evaluated individual stock predictions separately.
- Compared combined multi-stock performance.
- Saved trained model artifacts in a dedicated saved_models directory.

---

## Project Takeaways

This project demonstrates the complete workflow of applying recurrent deep-learning architectures to financial time-series data:

```
Raw Historical Data
        ↓
Data Integration
        ↓
Exploratory Analysis
        ↓
Scaling
        ↓
Sequence Generation
        ↓
RNN Architecture Experiments
        ↓
Hyperparameter Experiments
        ↓
Model Evaluation
        ↓
Prediction Visualization
        ↓
Saved Model Artifacts
```

The experiments demonstrate that both model architecture and sequence-window design can significantly influence measured performance in recurrent time-series models.

---

## Academic Context

This project was developed as part of postgraduate Machine Learning and AI coursework and focuses on applying recurrent neural networks to financial time-series prediction.

---

## Disclaimer

This project is an educational machine-learning experiment.

The reported model performance should not be interpreted as a guarantee of future stock-price movements or investment returns.

The project does not constitute financial advice or a trading recommendation.

---

## Author

**Saumy DhoLu**

Machine Learning & AI

GitHub:(https://github.com/SaumyDhoLu)
