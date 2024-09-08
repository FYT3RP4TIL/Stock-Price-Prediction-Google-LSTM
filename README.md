# README: RNN, Vanishing Gradient Problem, LSTM, and Stock Price Prediction

## 1. What is RNN (Recurrent Neural Network)?

Recurrent Neural Networks (RNNs) are a class of artificial neural networks designed to work with sequential data. Unlike traditional feedforward neural networks, RNNs have loops that allow information to persist, making them ideal for tasks involving time series, natural language processing, and other sequential data.

Key features of RNNs:
- They can process input sequences of any length.
- They maintain an internal state (memory) that gets updated as they process new inputs.
- They share parameters across different time steps, allowing them to learn patterns across time.

## 2. What is the Vanishing Gradient Problem?

The Vanishing Gradient Problem is a significant issue that affects the training of deep neural networks, particularly RNNs. It occurs when the gradients of the loss function approach zero as they are backpropagated through many layers or time steps.

Key points:
- As gradients are multiplied during backpropagation, they can become extremely small.
- This results in earlier layers or time steps learning very slowly or not at all.
- It's particularly problematic for RNNs when dealing with long-term dependencies in sequences.
- The problem makes it difficult for the network to learn and retain information over many time steps.

## 3. What is LSTM (Long Short-Term Memory)?

Long Short-Term Memory (LSTM) networks are a special kind of RNN designed to address the vanishing gradient problem and better capture long-term dependencies in sequential data.

Key features of LSTMs:
- They introduce a memory cell that can maintain information over long periods.
- They use gating mechanisms (input gate, forget gate, output gate) to control the flow of information.
- LSTMs can learn to store relevant information for long periods and forget irrelevant details.
- They are particularly effective for tasks requiring the understanding of long-term context.

## Notebook Explanation

### Importing the Training Set

```python
dataset_train = pd.read_csv('Google_Stock_Price_Train.csv')
training_set = dataset_train.iloc[:, 1:2].values
```

This code imports the training data from a CSV file containing Google stock prices from 2012 to 2016. It then extracts the 'Open' price values into a numpy array.

### Feature Scaling

```python
from sklearn.preprocessing import MinMaxScaler
sc = MinMaxScaler(feature_range = (0, 1))
training_set_scaled = sc.fit_transform(training_set)
```

Feature scaling is applied using Min-Max normalization to scale the values between 0 and 1. This is crucial for neural networks to work effectively.

Normalization Formula:
```
X_normalized = (X - X_min) / (X_max - X_min)
```

Where:
- X is the original value
- X_min is the minimum value in the feature
- X_max is the maximum value in the feature

### Creating 60 Timestamps

```python
X_train = []
y_train = []
for i in range(60, 1258):
    X_train.append(training_set_scaled[i-60:i, 0])
    y_train.append(training_set_scaled[i, 0])
X_train, y_train = np.array(X_train), np.array(y_train)
```

This code creates sequences of 60 timestamps for the LSTM model. Each input sequence (X_train) consists of 60 previous stock prices, and the corresponding output (y_train) is the next day's price. This structure allows the model to learn from the past 60 days to predict the next day's price.

### LSTM Model Architecture

The LSTM model is created using Keras with the following architecture:

1. First LSTM layer:
   ```python
   regressor.add(LSTM(units = 50, return_sequences = True, input_shape = (X_train.shape[1], 1)))
   regressor.add(Dropout(0.2))
   ```
   - 50 neurons
   - Returns sequences for stacking LSTM layers
   - Input shape based on the training data
   - Dropout of 20% to prevent overfitting

2. Second LSTM layer:
   ```python
   regressor.add(LSTM(units = 50, return_sequences = True))
   regressor.add(Dropout(0.2))
   ```
   - 50 neurons
   - Returns sequences
   - 20% dropout

3. Third LSTM layer:
   ```python
   regressor.add(LSTM(units = 50, return_sequences = True))
   regressor.add(Dropout(0.2))
   ```
   - Similar to the second layer

4. Fourth LSTM layer:
   ```python
   regressor.add(LSTM(units = 50))
   regressor.add(Dropout(0.2))
   ```
   - 50 neurons
   - Does not return sequences (last LSTM layer)
   - 20% dropout

5. Output layer:
   ```python
   regressor.add(Dense(units = 1))
   ```
   - Dense layer with 1 unit for predicting the stock price

The model is compiled using the Adam optimizer and Mean Squared Error as the loss function, which is appropriate for regression problems.

### Stock Price Prediction

The model predicts stock prices for January 2017 using the following steps:

1. Prepare the test data by concatenating training and test datasets.
2. Create sequences of 60 timestamps for the test data.
3. Use the trained model to make predictions.
4. Inverse transform the predictions to get actual stock price values.
5. Plot the real and predicted stock prices for visual comparison.

The resulting plot shows:
- Red line: Real Google Stock Price
- Blue line: Predicted Google Stock Price

This allows for a visual assessment of the model's performance in predicting stock prices.
