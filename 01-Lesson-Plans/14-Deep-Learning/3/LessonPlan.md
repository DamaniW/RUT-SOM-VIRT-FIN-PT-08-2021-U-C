## 14.3 Lesson Plan: Recurrent Neural Networks

---

### Overview

Today's class will introduce students to recurrent neural networks (RNN).

### Class Objectives

By the end of the class, students will be able to:

* Identify the differences between ANN (artificial neural network), RNN, and LSTM (long short-term memory) RNN models.

* Prepare data to fit LSTM RNN models.

* Define RNN models for sentiment analysis.

* Create LSTM RNN models for time series forecasting.

* Evaluate LSTM RNN models to discriminate against the best architecture for a given problem.

### Instructor Notes

* **Important:** Students with Apple Silicon M1 computers will need to use [Google Colab](https://colab.research.google.com/). An alternate "M1" file is provided in each activity folder.

* Slack out the [Algorithmic Trading Install Guide](../Supplemental/algorithmic-trading-install-guide.md) and tell students to complete the installation and verify it with a TA before the end of the next class. Students will need this installed before the next unit.

* Today's class is brief but may be challenging for students. Take extra time during the activities today to ensure that students can follow along.

* A few students may want to go even deeper into the theory and math behind RNNs, but it would be best to direct those in-depth discussions to office hours. Encourage students to review the reference links and to think about ways that they might be able to incorporate RNNs into their projects.

### Slideshow and Time Tracker

* The slides for this lesson can be viewed on Google Drive here: [Lesson Slides](https://docs.google.com/presentation/d/1-Oo9duYlIDTib327ZFFZoCj4Eh4BgBPhUdTUXBWsFck/edit?usp=sharing).

* To add the slides to the student-facing repository, download the slides as a PDF by navigating to File, selecting "Download as," and then choosing "PDF document." Then, add the PDF file to your class repository along with other necessary files. You can view instructions for this [here](https://docs.google.com/document/d/1XM90c4s9XjwZHjdUlwEMcv2iXcO_yRGx5p2iLZ3BGNI/edit?usp=sharing).

* **Note:** Editing access is not available for this document. If you wish to modify the slides, create a copy by navigating to File and selecting "Make a copy."

* The Time Tracker for this lesson can be found here: [Time Tracker](TimeTracker.xlsx).

---

### 1. Instructor Do: Welcome Class (5 min)

Welcome students to the third day of deep learning!

Open the lesson slides, and briefly cover today's agenda and class objectives. Explain to students that they will learn about recurrent neural networks (RNN), a type of neural network that can remember the past and combine that knowledge with new inputs to make decisions.

---

### 2. Instructor Do: Intro to the ROC Curve and AUC (10 min)

In this activity, students will learn how to assess the performance of a binary classification model by interpreting the ROC curve and AUC.

Explain to students that they will start Today's class by learning some advanced evaluation metrics, such as the ROC curve and AUC, that will be used to evaluate deep learning models.

Open the lesson slides and navigate to the "Introducing the ROC Curve and AUC" section and highlight the following:

* The confusion matrix is one method that can be used to assess the performance of a binary classification model.

  ![Confusion matrix components](Images/confusion-matrix.png)

* Let's recall the four components of this matrix:

  * TP (True Positives): Refers to the positive values that were correctly classified as positive by the model.

  * TN (True Negatives): Refers to the negative values that were correctly classified as negative by the model.

  * FP (False Positives): Refers to the negative values that were incorrectly classified as positive by the model.

  * FN (False Negatives): Refers to the positive values that were incorrectly classified as negative by the model.

* The ROC Curve and AUC are a couple of techniques that use the values from the confusion matrix to check and visualize the performance of a classification model

* ROC stands for "Receiver Operating Characteristic."

* The ROC curve shows the performance of a classification model as its discrimination threshold is varied.

* To plot a ROC curve, we use two parameters: true positive rate (`TPR` - also known as recall) and the false positive rate (`FPR`).

* The `TPR` is calculated as follows:

  ![rnn-sentiment-6](Images/rnn-sentiment-6.png)

* The `FPR` is calculated as follows:

  ![rnn-sentiment-7](Images/rnn-sentiment-7.png)

* Every point in the ROC curve represents the `TPR` Vs. `FPR` at different thresholds. The following image is a typical ROC Curve.

  ![ROC Curve](Images/roc-curve.png)

* Interpreting the ROC curve may be challenging; fortunately, we have the `AUC` that measures the area underneath the entire ROC curve (from `(0,0)` to `(1,1)`.

  ![AUC](Images/auc.png)

* The value of `AUC` ranges from `0` to `1`. A model whose predictions are `100%` wrong has an `AUC = 0.0`; in contrast, a model whose predictions are `100%` correct has an `AUC = 1.0`.

* An `AUC=1` is a paradox since it means that the model is perfect, but you may not trust in your model since it could be overfitted. A model with this `AUC` value correctly distinguishes between positive and negative classes.

* An `AUC=0.50` means that the model is unable to distinguish between positive and negative classes.

* Ideally, we may want to have `AUC` values ranging between `0` and `1`, where the higher the AUC, the better the model is at predicting `0s` as `0s` and `1s` as `1s`.

* So, a model with an `AUC=0.90` may be better than a model with an `AUC=0.65`.

  ![Different AUC values](Images/auc-for-roc-curves.png)

Explain to students that they will learn how to perform this analysis using python next. Answer any questions before moving on.

---

### 3. Everyone Do: Measuring Performance with the ROC Curve and AUC (15 min)

**Corresponding Activity:** [01-Evr_ROC_AUC](Activities/01-Evr_ROC_AUC)

This is an everyone do activity, where students will learn how to create the ROC Curve and compute the AUC using Python.

**Files:**

* [Solved version:roc_auc_fraud.ipynb](Activities/01-Evr_ROC_AUC/Solved/roc_auc_fraud.ipynb)

* [Unsolved version: roc_auc_fraud.ipynb](Activities/01-Evr_ROC_AUC/Unsolved/roc_auc_fraud.ipynb)

* [Dataset: transactions.csv](Activities/01-Evr_ROC_AUC/Resources/transactions.csv)

Explain to students this is a collaborative activity where everyone is going to follow you in the process of creating the ROC Curve and calculating the AUC of a deep learning model.

Slack out the unsolved version of the Jupyter notebook to students, and ask them to follow you as you live code the solution.

Open the unsolved version of the Jupyter notebook, and highlight the following.

* For this demo, we will create a fraud detection model using a deep neural network.

* We will use a dataset that contains anonymous information about `284807` credit card transactions made by European credit cardholders in September 2013.

* The dataset contains nine numerical variables, which are the result of PCA transformation to protect the confidentiality of credit cardholders. Only the transaction amount can be seen as is.

Import the dataset into a Pandas DataFrame called `transactions_df` and show the head.

![roc-auc-1](Images/roc-auc-1.png)

Continue the data preprocessing and highlight the following:

* The features set `X` will contain all the variables, from `V1` to `V9` and the `Amount`.

* The target vector `y` will contain the values of the `Class` column. It's set to `0` for non-fraudulent transactions, and to `1` for the fraudulent ones.

  ```python
  # Creating the X and y sets
  X = transactions_df.iloc[:, 0:10].values
  y = transactions_df["Class"].values
  ```

* Next we create the training, validation, and testing sets using the `train_test_split` method from sklearn.

  ```python
  # Creating training, validation, and testing sets
  from sklearn.model_selection import train_test_split

  X_train, X_test, y_train, y_test = train_test_split(X, y, random_state=78)

  X_train, X_val, y_train, y_val = train_test_split(X_train, y_train, random_state=78)
  ```

* We split the initial training set to create a new training set to fit the model, and a validation test for which data is going to be used during the training process to verify the model's metrics.

* Now that the training, validation, and testing sets are created, we can scale the data. Since the numerical features are on different scales, the `StandardScaler` from `sklearn` will be used to scale the data of `X_train`.

  ```python
  # Import the StandardScaler from sklearn
  from sklearn.preprocessing import StandardScaler

  # Scale the data
  scaler = StandardScaler().fit(X_train)
  X_train = scaler.transform(X_train)
  ```

* Now it's time to define our deep neural network, we will use a `Sequential` model and two `Dense` layers.

* First of all, we will define the number of inputs and the number of hidden nodes for each layer.

  ```python
  # Model set-up
  number_input_features = 10
  hidden_nodes_layer1 = 15
  hidden_nodes_layer2 = 5
  ```

* Next, we will define the model structure as follows.

  ```python
  # Define the LSTM RNN model
  model = Sequential()

  # Layer 1
  model.add(
    Dense(units=hidden_nodes_layer1, input_dim=number_input_features, activation="relu")
  )

  # Layer 2
  model.add(Dense(units=hidden_nodes_layer2, activation="relu"))

  # Output layer
  model.add(Dense(1, activation="sigmoid"))
  ```

Explain to students that we are using the `sigmoid` activation function since we have a binary output, `1` for fraud or `0` for no-fraud.

Next, the model is compiled. Explain to students that the `binary_crossentropy` loss function is used since we want to create a binary classification model.

Point out that we are defining some metrics to assess the model. These metrics are part of [the Keras metrics module](https://www.tensorflow.org/api_docs/python/tf/keras/metrics?version=stable) and are the same metrics students are already familiar with from previous units when binary classification was introduced. The only new metric is `AUC`, which will be explained next in the model's evaluation.

Explain to students that the `name` parameter is used to quickly identify each parameter during the training process and the model evaluation phase.

```python
# Compile the model
model.compile(
  loss="binary_crossentropy",
  optimizer="adam",
  metrics=[
    "accuracy",
    tf.keras.metrics.TruePositives(name="tp"),
    tf.keras.metrics.TrueNegatives(name="tn"),
    tf.keras.metrics.FalsePositives(name="fp"),
    tf.keras.metrics.FalseNegatives(name="fn"),
    tf.keras.metrics.Precision(name="precision"),
    tf.keras.metrics.Recall(name="recall"),
    tf.keras.metrics.AUC(name="auc"),
  ],
)
```

Time to fit the model! Explain to students that we will use a `batch_size = 1000` to speed-up the training process along `50` epochs. Point out that you are introducing the `validation_data` parameter to the `fit` method, explain to students that this parameter specifies a dataset that is used to validate the model's performance along the training process, excluding the validation data sample as training data.

```python
# Training the model
batch_size = 1000
epochs = 50
training_history = model.fit(
  X_train,
  y_train,
  validation_data=(X_val, y_val),
  epochs=epochs,
  batch_size=batch_size,
  verbose=1,
)
```

Execute the compilation code and highlight the following.

![roc-auc-2](Images/roc-auc-2.gif)

* Note that the training runs on `160203` samples and the validation on `53402` samples.

* As you can see, each epoch takes around `2` seconds, so running `50` epochs will take close to two minutes, so be patient.

* Also note that all the metrics are calculated on each epoch for the training and validation data. The validation metrics have the `val_` prefix.

* The model training results will be saved in the `training_history` variable for further analysis.

Continue the demo with the model performance assessment, explain to students that you will start by plotting two metrics that they are already familiar with: `loss` and `accuracy`. Highlight the following.

* The metrics results of the training process are stored in the `history` dictionary of the `training_history` object.

* You can access each metric using the names we define when compiling the model.

* To plot the metrics results, we are going to create a DataFrame using the `history` dictionary and plotting using the `plot()` method of the Pandas DataFrame.

  ![roc-auc-3](Images/roc-auc-3.png)

  ![roc-auc-4](Images/roc-auc-4.png)

Explain to students that the third metric to plot is `AUC` that stands for "Area Under the ROC Curve." Highlight the following.

![roc-auc-5](Images/roc-auc-5.png)

* The `AUC` value is better in the training data, however it improves for both training and validation data as the accuracy also increases.

* `AUC` may be desirable since it is scale-invariant. It measures how well predictions are ranked instead of their absolute values.

* `AUC` is classification-threshold-invariant. It measures the quality of the model's predictions regardless of the threshold.

Continue to the ROC curve plot. Explain to students that [`sklearn` has a method in the `metrics` module called `roc_curve`](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_curve.html) that calculates the values needed to plot the ROC curve for binary classification models. We will also use the `auc` method from `sklearn` in this part of the demo.

```python
# Import the roc_curve and auc metrics from sklearn
from sklearn.metrics import roc_curve, auc
```

Highlight the following as you continue live coding the demo.

* To create the ROC curve, it is crucial to get the predictions from the training and validation datasets.

  ```python
  # Making predictions to feed the roc_curve module
  train_predictions = model.predict(X_train, batch_size=1000)
  test_predictions = model.predict(X_test, batch_size=1000)
  ```

* The `roc_curve` method takes as parameters the actual labels and the predicted labels, to compute the false positive rate (`fpr`), true positive rate (`tpr`), and `thresholds`.

  ```python
  # Calculate the ROC curve and AUC for the training set
  fpr_train, tpr_train, thresholds_train = roc_curve(y_train, train_predictions)
  auc_train = auc(fpr_train, tpr_train)
  auc_train = round(auc_train, 4)

  # Calculate the ROC curve and AUC for the testing set
  fpr_test, tpr_test, thresholds_test = roc_curve(y_test, test_predictions)
  auc_test = auc(fpr_test, tpr_test)
  auc_test = round(auc_test, 4)
  ```

* Once we compute these values, we will use the `fpr` and `tpr` to create a pair of DataFrames to plot the ROC curve.

  ```python
  # Create a DataFrame with the fpr and tpr results
  roc_df_train = pd.DataFrame({"FPR Train": fpr_train, "TPR Train": tpr_train,})

  roc_df_test = pd.DataFrame({"FPR Test": fpr_test, "TPR Test": tpr_test,})
  ```

* We will plot the ROC curve using the `plot()` method from the Pandas DataFrame, we also include the `AUC` value in the title for further analysis.

  ![roc-auc-6](Images/roc-auc-6.png)

* Plotting the training and testing data ROC curves is a visual technique to validate how the model behaves with different data. It is also a way to see if the results with test data are relatively similar to train data or not.

Continue the demo by coding the model's evaluation using the `evaluate()` method. Explain to students that the evaluation results will be stored in the `scores` object that it's going to be used to create a `metrics` dictionary with the evaluation results.

![roc-auc-7](Images/roc-auc-7.png)

Point out to students that these are the results from the model's evaluation and that we will use these values for further analysis.

Continue the model's evaluation by creating a confusion matrix using the metrics obtained from the validation. A Pandas DataFrame is used to display the matrix.

![roc-auc-8](Images/roc-auc-8.png)

Next, the classification report is created using the `classification_report` method from `sklearn`.

![roc-auc-9](Images/roc-auc-9.png)

Finally, point out to students that these techniques can be used to compare the performance of a single model using different hyperparameters, as well as to compare different models for the same problem.

Answer any questions before moving on.

---

### 4. Instructor Do: Intro to Recurrent Neural Networks (15 min)

In this lecture, students will be introduced to the general concepts behind RNNs and LSTMs. The complex math behind these types of neural networks is beyond the scope of this lesson, but additional resources will be shared for those students interested in learning more about them.

Use the _Intro to Recurrent Neural Networks_ section of the lesson slides to highlight the following:

* RNNs are suitable for sequential pattern recognition, some examples include:

  * Natural Language Processing

  * DNA sequences

  * Time series data

  * Music composition

Before going deep into RNNs, it's essential to understand the general differences between artificial neural networks (ANNs) and RNNs. Pose the following question to students:

* ANNs can be used to identify the type of car from a still image. However, can we predict the direction of a car in movement?

  * **Answer:** The short answer for this question is _No_; if you try to predict the direction of a car only with the information of a still image, you don't have enough information to predict beyond of a _random guess_.

Explain to the class that without knowledge of where the car has been, you wouldn't have enough data to predict where the car is going.

Highlight the following reasons that ANNs are not suitable for detecting patterns over time:

* If we record in movement, we may have enough information to make a better prediction since we can have a sequence of images representing the car's movement.

* There is a challenge with ANNs since this type of neural network doesn't have a memory mechanism to store the different states of a sequence of images _per se_.

* This is where RNNs comes into action! RNNs are good at modeling sequence data thanks to their sequential memory.

* Following the example in the slides, using RNNs, we can predict that the car is moving to the right.

Explain to the class, that in contrast to an ANN, an RNN can remember previous data thanks to a feedback loop. This feedback loop allows information to flow from one step to the next along the sequence.

![ANNs Vs. RNNs](Images/ann_vs_rnn.png)

* For the car-in-motion example, the feedback loop of the RNN allows us to save the position of the car from one step to the next one as long as we have sequence data about the car's location.

Use an example from Natural Language Processing to explain how RNNs work:

* When a person reads a sentence such as `I want to invest for retirement`, the brain starts to make associations among the words as the person reads the sentence.

  ![RNN words](Images/rnn-words.png)

* The brain can decode the phrase and understand it because the architecture of our brain has memory to capture the sequence of the words in time. RNNs attempt to replicate this behavior.

* If we pass the sentence to an RNN, the sentence is split into individual words and the following steps are performed:

  * The first step is to feed `I` into the RNN. The RNN encodes `I` and produces an output (`01`).

  * For the next step, we feed the word `want` and the hidden state from the previous step. The RNN now has information on both the word `I` and `want`.

  * This process is repeated until the final step. By the last step, the RNN has encoded information from all the words in previous steps.

  * The final output (`06`) was created from the rest of the sequence. To predict what the phrase means, we take the final output and pass it to the feed-forward layer of the RNN to classify the intent.

  ![RNN prediction](Images/rnn_prediction.png)

Explain to students that the memory in an RNN has limitations; they only remember the most recent few steps of a sequence.

* The vanishing gradient in the hidden states illustrates this issue known as _short-term memory_.

  ![Vanishing gradient](Images/rnn_gradient.png)

* It can be seen that the RNN remembers a lot from the _brown state_, but just a little from the _red state_.

* This _short-term-memory_ issue is solved by using Long-Short-Term Memory Recurrent Neural Networks (aka LSTM or LSTM-RNN).

Explain to the class that we are not going over the math complexity of LSTMs. However, the key point is to know that an LSTM RNN works like an original RNN. The critical difference is that an LSTM can selectively decide which types of longer-term events are worth remembering and which are OK to forget.

* Thanks to LSTMs, we will be able to use the power of RNNs in longer time windows.

* LSTMs are capable of learning long-term dependencies using a mechanism called _gates_.

Open your web browser and navigate to the [Write With Transformer](https://transformer.huggingface.co/) website; slack out the URL to the students and explain that this website showcases different models that use neural networks for automatic text generation. This is part of the magic behind intelligent bots.

Click on the first model to demo how it works. Once the model's UI is opened, deleted the initial text.

![launching-write-with-transformer](Images/launching-write-with-transformer.gif)

Continue the demo by highlighting the following:

* Auto-text generators typically use neural networks to predict the next logical word or phrase to an initial text.

* Predictions are possible after training the models with thousands of corpus, which usually rely on different knowledge areas.

* Let's start typing `I want to` as the initial sentence. To auto-generate text using this model, you need to press the `TAB` key; next, the algorithm automatically creates some text suggestions based on the sequence of the previous words and the knowledge from its corpus

  ![text-gen-1](Images/text-gen-1.gif)

* When we type a longer sentence like `I want to invest for retirement`, we also include an intention in the phrase, so a different text with an apparently better semantical context is generated.

  ![text-gen-2](Images/text-gen-2.gif)

* This is just an example of how RNNs and LSTMs can be used for NLP.

For those students that may be interested in learning more about this matter, slack out the [Recurrent Neural Networks cheatsheet](https://stanford.edu/~shervine/teaching/cs-230/cheatsheet-recurrent-neural-networks) hosted by Stanford University.

Answer any questions before moving on.

---

### 5. Instructor Do: RNNs for NLP - Sentiment Analysis (15 min)

**Corresponding Activity:** [02-Ins_Sentiment_Analysis](Activities/02-Ins_Sentiment_Analysis)

In this activity, students will learn how to define an LSTM RNN model for sentiment analysis using Keras. Also, data preparation for using LSTM models for natural language processing is introduced.

**Files:**

* [austin_coffee_sentiment.ipynb](Activities/02-Ins_Sentiment_Analysis/Solved/austin_coffee_sentiment.ipynb)

* [austin_coffee_shops_reviews.csv](Activities/02-Ins_Sentiment_Analysis/Resources/austin_coffee_shops_reviews.csv)

Explain to students that you will start exploring the LSTM RNNs by creating a model for sentiment analysis using the Keras API of TensorFlow.

Open the unsolved Jupyter notebook, live code the solution, and highlight the following.

* For this demo, we are going to use a dataset that contains `6878` customer reviews of Coffee Shops in Austin, Texas. The reviews were taken from Yelp; however, the names of the Coffee Shops were anonymized for privacy reasons.

* The dataset has the following columns:

  * `coffee_shop_name`: The anonymized name of the coffee shop.

  * `full_review_text`: The customer reviews.

  * `sentiment`: The sentiment of each customer's review. `0` - Negative, `1` - Positive.

Create a new Pandas DataFrame named `reviews_df` and load the data from the `CSV` file provided.

![rnn-sentiment-1](Images/rnn-sentiment-1.png)

* As you can see, the names of the coffee shops are anonymized, the review comments are in plain English, and the sentiment of each customer review is already scored as positive (`1`) or negative (`0`).

Explain to students that RNNs, like any other neural network model, require an array data type, so the `full_review_text` column will be transformed into the `X` array and the "sentiment" column into the `y` array.

```python
# Creating the X and y vectors
X = reviews_df["full_review_text"].values
y = reviews_df["sentiment"].values
```

Explain to students that to train the RNN model, the text data should be encoded as an integer. This transformation can be done using [the Keras preprocessing modules](https://www.tensorflow.org/api_docs/python/tf/keras/preprocessing).

Import the `Tokenizer` and `pad_sequences` modules and highlight the following.

```python
# Import Keras modules for data encoding
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences
```

* The customers' review comments need to be tokenized by creating a `Tokenizer` object and fitting it with all the text comments in the `X` variable.

  ```python
  # Create an instance of the Tokenizer and fit it with the X text data
  tokenizer = Tokenizer(lower=True)
  tokenizer.fit_on_texts(X)
  ```

* We will use `lower=True` argument to convert all the text into lowercase to ensure data consistency.

* The `Tokenizer` object generates a dictionary that creates a list of words (tokens) that maps every unique word in the text with a unique integer. This list is going to be an encoded dictionary of the universe of particular words in the dataset; this dictionary is similar to the bag of words technique described in the NLP Unit.

  ```python
  # Print the first five elements of the encoded vocabulary
  for token in list(tokenizer.word_index)[:5]:
    print(f"word: '{token}', token: {tokenizer.word_index[token]}")
  ```

* Once the list of tokens is created, we need to transform every text review comment into a numerical sequence; this task is accomplished using the `texts_to_sequences()` method.

  ![rnn-sentiment-2](Images/rnn-sentiment-2.png)

* First, the text should be tokenized by fitting the Tokenizer class on the data set, using the "lower = True" argument to convert the text into lowercase to ensure consistency of the data. Afterward, map the list of words (tokens) to a list of unique integers for each unique word using the `texts_to_sequences` class.

* The RNN model requires that all the values of the `X` vector have the same length; the `pad_sequences` method will ensure that all integer encoded reviews have the same size. Each entry in `X` will be shortened to `140` integers, or pad with `0's` in case it's shorter.

  ```python
  # Padding sequences
  X_pad = pad_sequences(X_seq, maxlen=140, padding="post")
  ```

Explain to students that once the data is encoded, we will create training and testing sets.

```python
# Creating training, validation, and testing sets
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X_pad, y, random_state=78)
```

Highlight the following about these sets:

* First of all, we create the training and testing sets as we usually do.

* Now it's time to start building the model using Keras. We will use the `Sequential` model as has been done before; however, there are two new types of layers that are needed: `Embedding` and `LSTM`.

```python
# Import Keras modules for model creation
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Embedding, LSTM, Dense
```

Highlight the following about these new types of layers.

* [`Embedding` layer](https://www.tensorflow.org/api_docs/python/tf/keras/layers/Embedding?version=stable): It's a type of layer that is used in neural networks to process encoded text data.

* [`LSTM` layer](https://www.tensorflow.org/api_docs/python/tf/keras/layers/LSTM?version=stable): It's used to add an LSTM layer to the model.

Explain to students that the `Embedding` layer requires at least three parameters as follows. The `vocabulary_size` refers to the size of the vocabulary in the text that is going to be processed; this variable is set at the total number of words in the `tokenizer` dictionary plus `1`. The second parameter needed by this layer is the `input_length`; this parameter is set at `140` (`max_words` variable) that is the value defined for padding the reviews. Finally, the third parameter is the `embedding_size`; this parameter specifies how many dimensions will be used to represent each word. As a rule-of-thumb, a multiple of eight could be used; for this demo, tuning the model value to `64` delivered the best result.

```python
# Model set-up
vocabulary_size = len(tokenizer.word_counts.keys()) + 1
max_words = 140
embedding_size = 64
```

After setting these initial variables, the model's structure is defined. Explain to students that the LSTM RNN model will have two layers, start coding the model structure and comment on the following.

```python
# Define the LSTM RNN model
model = Sequential()

# Layer 1
model.add(Embedding(vocabulary_size, embedding_size, input_length=max_words))

# Layer 2
model.add(LSTM(units=280))

# Output layer
model.add(Dense(1, activation="sigmoid"))
```

* The model is defined as a `Sequential` instance.

* The first layer is an `Embedding` type, this layer processes the integer-encoded sequence of each review comment to create a dense vector representation that will be used by the `LSTM` layer.

* Next, we add an `LSTM` layer with `280` units (the double of the `max_words` variable as initial test value). This layer transforms the dense vector into a single vector that contains information about the entire sequence that will be used by the activation function in the `Dense` layer to score the sentiment.

* Potentially, adding more `LSTM` layers and input units can lead to better results.

* Finally, we add a `Dense` output layer with a sigmoid activation function to predict the probability of a review being positive.

* After defining our LSTM RNN, it's time to compile the model.

* Since this is a binary classification model, we'll use the `binary_crossentropy` loss function with the `adam` optimizer.

```python
# Compile the model
model.compile(
  loss="binary_crossentropy",
  optimizer="adam"
)
```

Time to fit the model! Explain to students that we will use a `batch_size = 1000` to speed-up the training process along `10` epochs.

```python
# Training the model
batch_size = 1000
model.fit(
    X_train,
    y_train,
    epochs=10,
    batch_size=batch_size,
    verbose=1,
)
```

Execute the compilation code and highlight that each epoch takes around `20` seconds. Running `10` epochs will take close to five minutes, so be patient.

Once the training process ends, it's time to test our model by making some predictions. To do that, explain to students that we are going to use a sample of ten integer-encoded review comments from the testing set.

To predict the sentiment of each review comment, we use the `predict()` method in conjunction with a threshold of `0.5`.  Anything under `0.5` is classified as `0` and anything over `0.5` is classified as `1`.  To do this we use the following code: 

```python
# Make sentiment predictions
predicted = (model.predict(X_test[:10]) > 0.5).astype("int32")
```

Finally, a DataFrame is created with the actual and predicted sentiments to contrast the prediction results versus the actual values.

![rnn-sentiment-11](Images/rnn-sentiment-11.png)

Answer any questions before moving on.

---

### 6. Students Do: Sentiment Analysis - RNNs Vs. Vader (40 min)

**Corresponding Activity:** [03-Stu_RNN_Vader](Activities/03-Stu_RNN_Vader)

In this activity, students will use two different models to score sentiment. The goal is to put the performance metrics and techniques students have learned into action to decide which model performs better between VADER and RNN LSTM.

**Instructions:**

* [README.md](Activities/03-Stu_RNN_Vader/README.md)

**Files:**

* [rnn_vs_vader.ipynb](Activities/03-Stu_RNN_Vader/Unsolved/rnn_vs_vader.ipynb)

* [movie_comments.csv](Activities/03-Stu_RNN_Vader/Resources/movie_comments.csv)

---

### 7. Instructor Do: Review Sentiment Analysis - RNNs Vs. Vader (10 min)

**Files:**

* Unsolved version [rnn_vs_vader.ipynb](Activities/03-Stu_RNN_Vader/Unsolved/rnn_vs_vader.ipynb)

* Solved version: [rnn_vs_vader.ipynb](Activities/03-Stu_RNN_Vader/Solved/rnn_vs_vader.ipynb)

* Dataset: [movie_comments.csv](Activities/03-Stu_RNN_Vader/Resources/movie_comments.csv)

Open the unsolved version of the Jupyter notebook, live code the solution, and highlight the following:

* Scoring sentiment is adding value to financial decisions nowadays, that's why it's essential to know how to score sentiment using different models.

* We used a dataset about movie reviews in this activity, but we can also use news feeds, blogs, or social media to score sentiment.

* The goal of this activity is to compare the performance of VADER sentiment Vs. an RNN LSTM model to decide which one could be better to score sentiment.

* As you already know, VADER is a natural language processing technique, whereas RNN LSTM models are a kind of deep neural networks. To compare these two models, we will use some metrics that could help us to assess which model performs better.

* First at all, to compare models, it's essential to set the random seed of `numpy` and `tensorflow` to ensure reproducibility.

  ```python
  # Set the random seed for reproducibility
  from numpy.random import seed
  seed(1)

  from tensorflow import random
  random.set_seed(2)
  ```

* After loading the dataset into the `df_reviews` DataFrame, we define the features set `X` and the target vector `y` by assigning the `comment` column to `X` and the `sentiment` column to `y`.

  ```python
  # Create the features set (X) and the target vector (y)
  X = df_reviews["comment"].values
  y = df_reviews["sentiment"].values
  ```

* To make the model comparison fair, we will use the same training and testing data. We will use the `train_test_split` method from `sklearn` to create the training, testing, and validation sets.

  ```python
  # Create the train, test, and validation sets
  from sklearn.model_selection import train_test_split

  X_train, X_test, y_train, y_test = train_test_split(X, y)

  X_train, X_val, y_train, y_val = train_test_split(X_train, y_train)
  ```

* First, we will score the sentiment using VADER, the same technique we learn in the NLP unit.

* As a good practice, we will download or update the VADER lexicon.

  ```python
  # Download/Update the VADER Lexicon
  nltk.download('vader_lexicon')
  ```

* Next, an instance of the `SentimentIntensityAnalyzer` from `nltk` is created to score the sentiment of every movie review using VADER.

  ```python
  # Initialize the VADER sentiment analyzer
  analyzer = SentimentIntensityAnalyzer()
  ```

* We create two lists to store the sentiment scoring results.

  ```python
  # Define two lists to store vader sentiment scoring
  y_vader_pred = []
  y_vader_prob = []
  ```

* We will use the `y_vader_pred` list to save the sentiment scored by VADER as `1` for positive, and `0` for negative.

* To plot the ROC Curve, we need the predicted probability of a review to be positive, so we will use the `y_vader_prob` list to store the value of the `pos` polarity score computed by VADER for each review comment.

* Using a `for` loop, we iterate across all the review comments in the `X` set to score the sentiment using VADER.

  ```python
  # Score sentiment of test set using Vader
  for comment in X_test:
    y_vader_prob.append(analyzer.polarity_scores(comment)["pos"])
    sentiment_score = analyzer.polarity_scores(comment)["compound"]
    if sentiment_score >= 0.1:
        y_vader_pred.append(1)
    else:
        y_vader_pred.append(0)
  ```

* Since we want to compare these two models on scoring sentiment as positive or negative, we need a way to interpret the polarity scores given by VADER.

* Following some recommendations of NLP researchers, we define a threshold of `0.1` to label a review as positive, if the `compound` score is greater than or equal to `0.1`, the review comment will be positive (append `1` to `y_vader_pred`); if the `compound` score is below `0.1`, the review comment will be negative (append `0` to `y_vader_pred`).

* Once we score the sentiment using VADER, we need to normalize the values of the `y_vader_prob` list. We will use the min-max normalization algorithm.

* We can apply the min-max normalization algorithm by using the `MinMaxScaler` method from `sklearn` as follows:

  ```python
  # Option 1: Normalizing data using MinMaxScaler from sklearn
  from sklearn.preprocessing import MinMaxScaler

  scaler = MinMaxScaler()
  scaler.fit(np.array(y_vader_prob).reshape(-1,1))
  y_vader_prob_norm = scaler.transform(np.array(y_vader_prob).reshape(-1,1))
  ```

* Also, we may want to manually code the min-max algorithm using a comprehension list as follows:

  ```python
  # Option 2: Using a comprehension list
  normalized = [(x - min(y_vader_prob)) / (max(y_vader_prob) - min(y_vader_prob))
    for x in y_vader_prob]
  ```

* Either method you use, will lead to the same results of normalizing the values between `0` and `1`. We will consider the first approach for this demo.

* At this time, we have the original sentiments in `y_test`, the predicted sentiment classes in `y_vader_pred`, and the sentiment predictions in `y_vader_prob`. Now we will continue to score sentiment using an RNN LSTM model.

* As you know, RNN LSTM models work with numerical data, so we need to encode the text data on the review comments to a numerical representation.

* We start by encoding the vocabulary of the dataset using the `Tokenizer` method from Keras.

  ```python
  # Import the Tokenizer method from Keras
  from tensorflow.keras.preprocessing.text import Tokenizer

  # Create an instance of the Tokenizer and fit it with the X text data
  tokenizer = Tokenizer(lower=True)
  tokenizer.fit_on_texts(X)
  ```

* For testing proposes, we print the first five elements of the encoded vocabulary.

  ![Sample encoded-words using the Tokenizer method from Keras](Images/movie-reviews-encoded-vocabulary.png)

* Next, we transform all the review comments to numerical sequences using the `text_to_sequences()` method of the `Tokenizer`.

  ```python
  # Transform the text data to numerical sequences
  X_seq = tokenizer.texts_to_sequences(X)
  ```

* For testing proposes, we compare the text representation of a movie review with its numerical representation, by printing the first text review in `X` and the first encoded element in `X_seq`.

  ![Contrast of a move review comment as text and as numerical sequence](Images/text-vs-sequence.png)

* RNN LSTM models need equal size inputs, so we will pad the sequences stored in `X_pad` up to `140` integers using the `pad_sequences` method from Keras.

  ```python
  # Import the pad_sequences method from Keras
  from tensorflow.keras.preprocessing.sequence import pad_sequences

  # Set the pad size
  max_words = 140

  # Pad the sequences using the pad_sequences() method
  X_pad = pad_sequences(X_seq, maxlen=max_words, padding="post")
  ```

* We may use a bigger padding size; however, using a bigger value will increase the time that takes fitting the RNN LSTM model.

* After preparing the data, we create training, validation, and testing sets using the `train_test_split` method from `sklearn`.

  ```python
  # Creating training, validation, and testing sets using the encoded data
  X_train_rnn, X_test_rnn, y_train_rnn, y_test_rnn = train_test_split(X_pad, y)

  X_train_rnn, X_val_rnn, y_train_rnn, y_val_rnn = train_test_split(X_train_rnn, y_train_rnn)
  ```

* Now we can define the structure of the RNN LSTM model since we will use an `Embedding` layer, we need to set the `input_dim` parameter to the size of the vocabulary, and define an embedding size, so we set the following initial configuration variables.

  ```python
  # Model set-up
  vocabulary_size = len(tokenizer.word_counts.keys()) + 1
  embedding_size = 64
  ```

* We will define an RNN LSTM model with three layers as follows:

  * _Layer 1:_ Add an `Embedding` layer using the `vocabulary_size` and `embedding_size` variables as the first two parameters, and setting `input_length=max_words` (the same size as the padding).

  * _Layer 2:_ Add an LSTM layer with `280` units.

  * _Output Layer:_ Add a `Dense` layer with `1` unit and `sigmoid` as activation function.

    ```python
    # Define the LSTM RNN model
    model = Sequential()

    # Layer 1
    model.add(Embedding(vocabulary_size, embedding_size, input_length=max_words))

    # Layer 2
    model.add(LSTM(units=280))

    # Output layer
    model.add(Dense(units=1, activation="sigmoid"))
    ```

* We compile the model using the `binary_crossentropy` loss function, the `adam` optimizer, and fetch the following metrics: Accuracy, True positives, True negatives, False positives, False negatives, Precision, Recall, and AUC.

  ```python
  # Compile the model
  model.compile(
      loss="binary_crossentropy",
      optimizer="adam",
      metrics=[
          "accuracy",
          tf.keras.metrics.TruePositives(name="tp"),
          tf.keras.metrics.TrueNegatives(name="tn"),
          tf.keras.metrics.FalsePositives(name="fp"),
          tf.keras.metrics.FalseNegatives(name="fn"),
          tf.keras.metrics.Precision(name="precision"),
          tf.keras.metrics.Recall(name="recall"),
          tf.keras.metrics.AUC(name="auc"),
      ],
  )
  ```

* Once the model is compiled, it's training time. We will train this model along `10` epochs with a batch size of `1000`.

  ```python
  # Training the model
  batch_size = 1000
  epochs = 10
  model.fit(
      X_train_rnn,
      y_train_rnn,
      validation_data=(X_val_rnn, y_val_rnn),
      epochs=epochs,
      batch_size=batch_size,
      verbose=1,
  )
  ```

At this point in the solution's review, switch to the solved version to continue by doing a dry walkthrough of the code since fitting the model takes up to 10 minutes. Highlight the following.

* Once the model training finishes, we predict the classes using the testing data to start the model comparison.

  ```python
  # Predict classes using the testing data
  y_rnn_pred = model.predict(X_test_rnn) > 0.5.astype("int32")
  ```

* Now, that we have sentiment scoring with both models, it's time to compare their performance.

* We start by comparing the models' accuracy using the `accuracy_score` method from `sklearn`.

  ![Comparing models accuracy](Images/vader-rnn-accuracy.png)

* At this point, the RNN LSTM model looks better since it has higher accuracy, let's take a look at the confusion matrices of these two models; we use the `confusion_matrix` method from `sklearn` for this task.

* Using the `confusion_matrix` method, and passing as parameters the sentiments from the testing data and the sentiments scored by both models, we gather the data needed to create and display a confusion matrix using a Pandas DataFrame.

  ![Confusion matrix for VADER](Images/confusion-matrix-vader.png)

  ![Confusion matrix for VADER](Images/confusion-matrix-rnn.png)

* After reviewing both confusion matrices, we observe that once again, the RNN LSTM model performs better since it has less false positives and false negatives.

* Another exciting tool for comparing binary classifications models is the `classification_report` from `sklearn`, since we can analyze metrics such as precision, recall, and the F1-score.

  ![Comparison of the classification report of both models](Images/rnn-vader-classification-reports.png)

* Again, the RNN LSTM model performs better, especially by observing the F1-score.

* Finally, we make a visual analysis of model performance by plotting the ROC curve and computing the AUC. We use the `roc_curve` and `auc` methods from `sklearn`

  ```python
  # Import the roc_curve and auc metrics from sklearn
  from sklearn.metrics import roc_curve, auc
  ```

* We start by plotting the ROC curve for VADER; we use the `roc_curve` method to compute the false positive rate (fpr) and true positive rate (tpr).

  ```python
  # Data for ROC Curve - VADER
  fpr_test_vader, tpr_test_vader, thresholds_test_vader = roc_curve(y_test, y_vader_prob_norm)
  ```

* After calculating the `fpr` and `tpr` for VADER, we use the `auc` method of `sklearn` to calculate the AUC for VADER. Next, we round the final result up to `4` decimals.

  ```python
  # AUC for VADER
  auc_test_vader = auc(fpr_test_vader, tpr_test_vader)
  auc_test_vader = round(auc_test_vader, 4)
  ```

* Once we fetch all the data needed to plot the ROC curve, we create a DataFrame with the `fpr` and `tpr` data from VADER and we plot the curve using the `plot()` method of the DataFrame.

  ```python
  # Dataframe to plot ROC Curve for VADER
  roc_df_test_vader = pd.DataFrame({"FPR Test": fpr_test_vader, "TPR Test": tpr_test_vader,})

  roc_df_test_vader.plot(
    x="FPR Test",
    y="TPR Test",
    color="red",
    style="--",
    xlim=([-0.05, 1.05]),
    title=f"Test ROC Curve - Vader (AUC={auc_test_vader})",
  )
  ```

  ![ROC Curve for VADER](Images/roc-curve-vader.png)

* After creating the ROC curve for the RNN LSTM mode, we need to make predictions using the `predict()` method of the model and the testing data.

  ```python
  # Making predictions to feed the roc_curve module
  test_predictions_rnn = model.predict(X_test_rnn, batch_size=1000)
  ```

* Next, we use the `roc_curve` method to compute the false positive rate (fpr) and true positive rate (tpr) for the RNN LSTM model.

  ```python
  # Data for ROC Curve - RNN LSTM Model
  fpr_test_rnn, tpr_test_rnn, thresholds_test_rnn = roc_curve(y_test_rnn, test_predictions_rnn)
  ```

* Now, we calculate the AUC for the RNN LSTM model as we did for VADER.

  ```python
  # AUC for the RNN LSTM Model
  auc_test_rnn = auc(fpr_test_rnn, tpr_test_rnn)
  auc_test_rnn = round(auc_test_rnn, 4)
  ```

* Finally, we create a DataFrame with the `fpr` and `tpr` data and plot the curve using the same process as with VADER.

  ```python
  # Dataframe to plot ROC Curve for the RNN LSTM model
  roc_df_test_rnn = pd.DataFrame({"FPR Test": fpr_test_rnn, "TPR Test": tpr_test_rnn,})

  roc_df_test_rnn.plot(
    x="FPR Test",
    y="TPR Test",
    color="blue",
    style="--",
    xlim=([-0.05, 1.05]),
    title=f"Test ROC Curve (AUC={auc_test_rnn})",
  )
  ```

  ![ROC Curve for the RNN LSTM model](Images/roc-curve-rnn-lstm.png)

* Now we have enough metrics to have a conclusion about these two models' performance.

* After reviewing the results, we can conclude that the RNN LSTM model has a better performance to score sentiment. The RNN model has a higher accuracy and `F1` score values. Also, its ROC Curve plot has a better shape whose area under the curve (AUC) is very close to `1`.

* Comparing models is a crucial part of making a decision using machine learning algorithms, since you may want to be sure to deploy the best model for your data and the context of your business scenario.

* You can use the techniques you employ in this activity to compare any binary classification algorithms, such as models to score the decision of buy or sell in a trading algorithm, or the decision to increase or decrease the selling price of a house in real estate.

Answer any questions before moving on.

---

### 8. BREAK (40 min)

---

### 9. Instructor Do: RNN LSTM and Time Series (15 min)

**Corresponding Activity:** [04-Ins_LSTM_Time_Series](Activities/04-Ins_LSTM_Time_Series)

In this activity, students will learn how to build RNN LSTM models for time series forecasting using Keras.

**Files:**

* [lstm_predictor.ipynb](Activities/04-Ins_LSTM_Time_Series/Solved/lstm_predictor.ipynb)

* [stock_data.csv](Activities/04-Ins_LSTM_Time_Series/Resources/stock_data.csv)

Explain to students that RNN LSTM models can also be used to forecast time-series data, such as stock prices, sales, temperature, or even the heights of ocean tides or sound waves!

Open the unsolved version of the Jupyter notebook; live code the solution and highlight the following:

* At a glance, forecasting time series data using RNN LSTM models could be seen as a three step process:

  * **Step 1:** Data preparation.

  * **Step 2:** Model building and training.

  * **Step 3:** Model evaluation and predicting new values.

* This activity will use a history of closing prices to predict the next closing price.

* The first step to solving this problem is to prepare the data for the model. This process typically requires the following steps:

  1. Load and clean the data (clean if needed).
  2. Window the data.
  3. Split the data into training and testing datasets.
  4. Scale and Normalize the data with `StandardScaler` or `MinMaxScaler`.
  5. Reshape the inputs for the model.

 When prototyping models, it's a common practice to set the random seed to ensure reproducibility; the random seed is set for `numpy` and `tensorflow` as follows.

  ```python
  from numpy.random import seed
  seed(1)

  from tensorflow import random
  random.set_seed(2)
  ```

* The data is loaded into a Pandas DataFrame. Note that the index is set to the column containing the date of the time series, and the `infer_datetime_format` and `parse_dates` parameters are set to `True`.

  ![Data Prep 1](Images/data-prep-1.png)

Explain to students that the first step towards preparing the data is to create the input features vectors `X` and the target vector `y`.

Explain to students that we will use the `window_data()` function to create these vectors and highlight the following.

```python
def window_data(df, window, feature_col_number, target_col_number):
    """
    This function accepts the column number for the features (X) and the target (y).
    It chunks the data up with a rolling window of Xt - window to predict Xt.
    It returns two numpy arrays of X and y.
    """
    X = []
    y = []
    for i in range(len(df) - window):
        features = df.iloc[i : (i + window), feature_col_number]
        target = df.iloc[(i + window), target_col_number]
        X.append(features)
        y.append(target)
    return np.array(X), np.array(y).reshape(-1, 1)
```

* This function chunks the data up with a rolling window of _X<sub>t</sub> - window_ to predict _X<sub>t</sub>_.

* The function returns two `numpy` arrays:

  * `X`: The input features vectors.

  * `y`: The target vector.

* The function has the following parameters:

  * `df`: The original DataFrame with the time series data.

  * `window`: The window size in days of previous closing prices that will be used for the prediction.

  * `feature_col_number`: The column number from the original DataFrame where the features are located.

  * `target_col_number`: The column number from the original DataFrame where the target is located.

Explain to students, that we will predict closing prices using a `5` days windows of previous _T-Bonds_ closing prices, so that, we will create the `X` and `y` vectors by calling the `window_data` function and defining a window size of `5` and setting the features and target column numbers to `2` (this is the column with the _T-Bonds_ closing prices).

![Data Prep 2](Images/data-prep-2.png)

* To create the training and testing dataset, the data is manually split using array slicing to avoid randomizing data when creating the samples. This is because the sequencing of data in time is essential when training the model, so random sampling and shuffling should be avoided.

```python
# Use 70% of the data for training and the remainder for testing
split = int(0.7 * len(X))
X_train = X[: split]
X_test = X[split:]
y_train = y[: split]
y_test = y[split:]
```

Once the training and test datasets are created, explain to students that we need to scale the data before training the LSTM model. We will use the `MinMaxScaler` from `sklearn` to scale all values between `0` and `1`. Note that because this is a regression problem (we are predicting a closing price), we scale both the features **AND** the target data. If the output target were categorical, the `y` data would not need to be scaled.

```python
# Use the MinMaxScaler to scale data between 0 and 1.
from sklearn.preprocessing import MinMaxScaler

# Create a MinMaxScaler object
scaler = MinMaxScaler()

# Fit the MinMaxScaler object with the training feature data X_train
scaler.fit(X_train)

# Scale the features training and testing sets
X_train = scaler.transform(X_train)
X_test = scaler.transform(X_test)

# Fit the MinMaxScaler object with the training target data y_train
scaler.fit(y_train)

# Scale the target training and testing sets
y_train = scaler.transform(y_train)
y_test = scaler.transform(y_test)
```

Explain to students that the LSTM API from Keras needs to receive the features data as a _vertical vector_  so we need to reshape the `X` data in the form `reshape((X_train.shape[0], X_train.shape[1], 1))`. Both sets, training, and testing are reshaped.

```python
# Reshape the features for the model
X_train = X_train.reshape((X_train.shape[0], X_train.shape[1], 1))
X_test = X_test.reshape((X_test.shape[0], X_test.shape[1], 1))
```

Once we prepare the data, explain to students that we are now going to build and train an LSTM RNN model using TensorFlow and Keras.

* The LSTM RNN model in Keras uses the `Sequential` model and the `LSTM` layer as we did before. However, there is a new type of layer called `Dropout`.

* The `Dropout` layer refers to a regularization technique for reducing overfitting in neural networks. It only drops a certain percentage of the connections to avoid overfitting.

Open the lesson slides, and move to the _Introduction to Dropout_ section to give a brief explanation about this concept to the class.

* For simplicity, the concept of dropout will be explained using a simple neural network design with just one layer.

* Dropout consists of removing units from the hidden layers. It achieves this by randomly selecting a fraction of the hidden nodes and sets their output to zero, regardless of the input. Dropout effectively removes those connections from contributing to the decision logic.

* The dropout layer will randomly select a different subset of units for each training input.

* In Keras, a dropout is implemented by adding a layer after each `LSTM` layer and defining the fraction of nodes to drop as the layer parameter.

Switch back to the Jupyter notebook and continue the solution:

```python
# Define the LSTM RNN model.
model = Sequential()

number_units = 5
dropout_fraction = 0.2

# Layer 1
model.add(LSTM(
    units=number_units,
    return_sequences=True,
    input_shape=(X_train.shape[1], 1))
    )
model.add(Dropout(dropout_fraction))
# Layer 2
model.add(LSTM(units=number_units, return_sequences=True))
model.add(Dropout(dropout_fraction))
# Layer 3
model.add(LSTM(units=number_units))
model.add(Dropout(dropout_fraction))
# Output layer
model.add(Dense(1))
```

* To create an LSTM RNN model, we will add `LSTM` layers.

* The `return_sequences` parameter needs to set to `True` every time we add a new `LSTM` layer, excluding the final layer. This is just how Keras knows to connect each layer.

* The `input_shape` is the number of time steps and the number of indicators. In this example, the number of time steps is equal to the window specified earlier (5), while the number of indicators is related to how many input features are used to predict the final price. In this case, only the past five closing prices are used to predict the next closing price, so the input shape is `(5, 1)`. Another way to remember this is `input_shape=(window_size, number_of_features)`.

* After each `LSTM` layer, we add a `Dropout` layer to prevent overfitting.

* The parameter passed to the `Dropout` layer is the fraction of nodes that will be dropped on each epoch.

* For this demo, we will use a dropout value of `0.2`, which means that on each epoch, we will randomly drop `20%` of the units.

* The number of units in each `LSTM` layer, is equal to the size of the time window, in this demo, we are taking five previous `T-Bonds` closing prices to predict the next closing price.

Explain to students that the optimizer and loss parameters need to be specified to compile the model. For a regression problem, a good starting choice for these parameters is to use `adam` for the optimizer and `mean_squre_error` for the loss function. Further information about these can be found on [the Keras API for TensorFlow website](https://www.tensorflow.org/api_docs/python/tf/keras/layers/LSTM).

```python
# Compile the model
model.compile(optimizer="adam", loss="mean_squared_error")
```

* Once the model is defined, we train (fit) the model using 10 epochs.

  ```python
  # Train the model
  model.fit(X_train, y_train, epochs=10, shuffle=False, batch_size=1, verbose=1)
  ```

* Since we are working with time-series data, it's important to set `shuffle=False` since it's necessary to keep the sequential order of the data.

* We can experiment with the `batch_size` parameter; however, the smaller batch size is recommended; in this demo, we will use a `batch_size=1`.

Explain to students that after training the model, it's time to evaluate our model to assess its performance.

* We will use the `evaluate()` method using the testing data.

  ```python
  # Evaluate the model
  model.evaluate(X_test, y_test)
  ```

* To better understand the model evaluation results, we will make some predictions and plot the predicted vs. the real prices.

  ```python
  # Make some predictions
  predicted = model.predict(X_test)
  ```

* Since we scaled the original values using the `MinMaxScaler`, we need to recover the original prices to better understand the predictions.

* We will use the `inverse_transform()` method of the scaler to decode the scaled values to their original scale.

  ```python
  # Recover the original prices instead of the scaled version
  predicted_prices = scaler.inverse_transform(predicted)
  real_prices = scaler.inverse_transform(y_test.reshape(-1, 1))
  ```

* To plot the predicted vs. the real values, we will create a DataFrame.

  ![model-eval-1](Images/model-eval-1.png)

* Finally, we plot the predicted vs. real prices using the `plot()` method of the DataFrame.

  ![model-eval-2](Images/model-eval-2.png)

After creating the plot, ask students the following question and conduct a brief discussion about the results:

* Will you trust in this model to predict prices?

  * **Sample Answer:** It's difficult to trust in this model as is because the error between the real and predicted values looks big.

Explain that if you wanted to use this model despite the initial error size, it might be useful to re-run the notebook using different iterations of training and testing data. Be sure to re-run the experiment without the random seed to gain additional randomness in the experiment.

Finally, explain to students that this model could be enhanced as follows:

* We can test different window sizes, for example, from `5` to `10`.

* We can test different dropout parameters, for example, between `0.2` and `0.5`.

* We may want to add or remove additional LSTM layers.

* When training the model, we can test different batch sizes and adjust the training epochs.

Answer any questions before moving on.

---

### 10. Students Do: Predicting Gold Closing Prices (30 min)

**Corresponding Activity:** [05-Stu_Predict_Gold_Prices](Activities/05-Stu_Predict_Gold_Prices)

This activity is a mini-project where students will gain hands-on experience building an RNN LSTM model for time-series data predicting gold closing prices.

**Instructions:**

* [README.md](Activities/05-Stu_Predict_Gold_Prices/README.md)

**Files:**

* [gold_price_predict.ipynb](Activities/05-Stu_Predict_Gold_Prices/Unsolved/gold_price_predict.ipynb)

---

### 11. Instructor Do: Review Predicting Gold Closing Prices (10 min)

**Files:**

* [gold_price_predict.ipynb](Activities/05-Stu_Predict_Gold_Prices/Solved/gold_price_predict.ipynb)

Open the unsolved version of the Jupyter notebook, live code the solution, and highlight the following:

* In real-world applications, you may need to interact with live data that you should retrieve from an API, that's why we used in this activity the Quandl API.

* As you already know, the safest way to store API keys is using environment variables, so we start by importing our Quandl API key.

  ```python
  # Import the API key for QUANDL
  import os

  quandl_key = os.getenv("QUANDL_API_KEY")
  ```

* In this activity, you were asked to create a model to predict gold prices, so using our API key, we fetch the historical prices of gold up to yesterday using the Quandl end-point for this price and the `requests` library.

  ```python
  # Set Gold price URL
  request_url = "https://www.quandl.com/api/v3/datasets/LBMA/GOLD.json?api_key="

  gold_prices_url = request_url + quandl_key

  # Fetch Gold prices from QUANDL
  import requests

  response_data = requests.get(gold_prices_url).json()
  ```

* To retrieve the prices of gold, first, we get the keys of the `response_data` dictionary, where we realized that there is only one key called `dataset` that encloses all the data from the API response.

  ![Exploring the gold prices response](Images/explore-gold-prices-response.png)

* After printing the content of the `dataset` key, we can identify that there are two keys with the data we need, these keys are `column_names` and `data`.

  ![Content of the dataset key](Images/dataset-key-content.png)

* As you can imagine, we can also go to the API documentation to understand the structure of the response and identify where the data is that we need. However, when you review the [Quandl API documentation for time-series data](https://docs.quandl.com/docs/in-depth-usage), you can see that the name of the main key of the `json` output is `dataset_data`.

  ![Quandl API docs](Images/quandl-api-docs.png)

* This errata in the documentation could be due to a change in the API that is not updated in the documentation jet, that's why sometimes you will need to manually explore the response of an API to identify where the data you need is located.

* Once we identify where the data we need is, we create a DataFrame. It's essential to explicitly cast the `Date` column to `datetime` data type and set this column as the DataFrame index.

  ```python
  # Create a DataFrame with Gold prices
  gold_df = pd.DataFrame(
      response_data["dataset"]["data"],
      columns=response_data["dataset"]["column_names"],
  )

  #Transform the "Date" column to datetime
  gold_df['Date']= pd.to_datetime(gold_df['Date'])

  # Set the "Date" column as the DataFrame Index
  gold_df.set_index("Date", inplace=True)

  # Show the DataFrame head
  gold_df.head()
  ```

  ![Sample gold prices DataFrame](Images/sample-gold-prices-df.png)

* It's important to clean the data before we start using it for any machine learning model. In this case, we look for missing data by counting all the null values in the DataFrame.

  ![Missing gold prices in the DataFrame](Images/missing-gold-prices.png)

* As you can see, there are `16020` missing prices in the DataFrame, so we will fill these missing values with the gold price of the preceding day using the `fillna()` method of the DataFrame and setting `method=pad`.

  ```python
  # Filling missing values with the previous ones
  gold_df = gold_df.fillna(method ='pad')
  ```

* Once we have cleaned up our data, we will create the features and target set using the custom `window_data()` function we define.

* For this demo, we will use a window size of `30` days, and since we want to predict the gold prices in US Dollars, we will pass `1` as the feature and target columns index.

  ```python
  # Define the window size
  window_size = 30

  # Set the index of the feature and target columns
  feature_column = 1
  target_column = 1

  # Create the features (X) and target (y) data using the window_data() function.
  X, y = window_data(gold_df, window_size, feature_column, target_column)
  ```

* Next, to avoid the dataset being randomized, we manually create the training and testing sets using array slicing and defining the 70% of the data for training and the remainder for testing.

  ```python
  # Manually splitting the data
  split = int(0.7 * len(X))

  X_train = X[: split]
  X_test = X[split:]

  y_train = y[: split]
  y_test = y[split:]
  ```

* Before training the RNN LSTM model, we use the `MinMaxScaler` from `sklearn` to scale the training and testing data between `0` and `1`. Note that we scaled the features and the target sets.

  ```python
  # Importing the MinMaxScaler from sklearn
  from sklearn.preprocessing import MinMaxScaler

  # Create a MinMaxScaler object
  scaler = MinMaxScaler()

  # Fit the MinMaxScaler object with the training feature data X_train
  scaler.fit(X_train)

  # Scale the features training and testing sets
  X_train = scaler.transform(X_train)
  X_test = scaler.transform(X_test)

  # Fit the MinMaxScaler object with the training target data y_train
  scaler.fit(y_train)

  # Scale the target training and testing sets
  y_train = scaler.transform(y_train)
  y_test = scaler.transform(y_test)
  ```

* As you already know, the LSTM API from Keras needs to receive the features data as a _vertical vector_, so we reshape the `X` data with `reshape((X_train.shape[0], X_train.shape[1], 1))`. Both sets, training, and testing should be reshaped.

  ```python
  # Reshape the features data
  X_train = X_train.reshape((X_train.shape[0], X_train.shape[1], 1))
  X_test = X_test.reshape((X_test.shape[0], X_test.shape[1], 1))
  ```

* We finally get the data ready to be used by our model, so we will define a three layers RNN LSTM model using `30` units in each layer since this is the size of our time windows, and a dropout fraction of `20%`.

  ```python
  # Define the LSTM RNN model.
  model = Sequential()

  # Initial model setup
  number_units = 30
  dropout_fraction = 0.2

  # Layer 1
  model.add(LSTM(
      units=number_units,
      return_sequences=True,
      input_shape=(X_train.shape[1], 1))
      )
  model.add(Dropout(dropout_fraction))

  # Layer 2
  model.add(LSTM(units=number_units, return_sequences=True))
  model.add(Dropout(dropout_fraction))

  # Layer 3
  model.add(LSTM(units=number_units))
  model.add(Dropout(dropout_fraction))

  # Output layer
  model.add(Dense(1))
  ```

* We compile the model using the `adam` optimizer, and `mean_square_error` as loss function since the value we want to predict is continuous.

  ```python
  # Compile the model
  model.compile(optimizer="adam", loss="mean_squared_error")
  ```

* Now, we will train the model using the training data, `10` epochs, and a batch size of `90`. Remember, it's important to set `shuffle=False` to keep the sequential order of the time-series data.

  ```python
  # Train the model
  model.fit(X_train, y_train, epochs=10, shuffle=False, batch_size=90, verbose=1)
  ```

* Time to assess our model's performance! We will use the `evaluate()` method of the model and the testing data to evaluate the model's performance using the mean square error (MSE) metric we defined.

  ![Mean square error of the model](Images/rnn-lstm-gold-mse.png)

* It seems that the model is not too bad since the MSE is close to zero. Now, we will make predictions using the testing data for further analysis.

```python
# Make predictions using the testing data X_test
predicted = model.predict(X_test)
```

* Since we scaled the original values using the `MinMaxScaler`, we need to recover the original gold prices to better understand the predictions.

* We will use the `inverse_transform()` method of the scaler to decode the scaled testing and predicted values to their original scale.

  ```python
  # Recover the original prices instead of the scaled version
  predicted_prices = scaler.inverse_transform(predicted)
  real_prices = scaler.inverse_transform(y_test.reshape(-1, 1))
  ```

* Finally, we will create a DataFrame with the decoded values to create a line chart to contrast the predicted vs. the actual values.

  ```python
  # Create a DataFrame of Real and Predicted values
  stocks = pd.DataFrame({
    "Actual": real_prices.ravel(),
    "Predicted": predicted_prices.ravel()
  }, index = gold_df.index[-len(real_prices): ]) 

  ```

* Once we create the DataFrame, we can use the `plot()` method of the DataFrame to create the line chart.

  ![Line chart with Actual Vs. Predicted gold prices](Images/actual-vs-predicted-gold-prices.png)

* In this activity, you gained hands-on experience in creating RNN LSTM models to make predictions using time-series data.

* Some other tests can be done, such as varying the time window size, the number of layers in the model, or using different batch sizes for training.

* This fine-tuning of a model's parameters is part of the job that should be done to find the model that fits best to a particular business problem.

Answer any questions before moving on.

---

### 12. Instructor Do: Structured Review (35 mins)

**Note:** If you are teaching this Lesson on a weeknight, please save this 35 minute review for the next Saturday class.

Please use the entire time to review questions with the students before officially ending class.

Suggested Format:

* Ask students for specific activities to revisit.

* Revisit key activities for the homework.

* Allow students to start the homework with extra TA support.

Take your time on these questions! This is a great time to reinforce concepts and address misunderstandings.

### End Class

---

Trilogy Education Services © 2019. All Rights Reserved.
