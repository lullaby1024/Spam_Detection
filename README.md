# Spam Detection

## Introduction
The goal of this project is to identify spam messages using ML models. Besides accuracy, **precision** is a priority for our task, as users don’t want to miss important messages.  Therefore, decreasing FP is priority. 

A walk-through of the project is given below.

## Data Import
- **Data Source**: [SMS Spam Collection Dataset](https://www.kaggle.com/uciml/sms-spam-collection-dataset), which is a set of SMS tagged messages that have been collected for SMS Spam research. 
- 5,574 English messages, tagged as ham (legitimate) or spam.

*Side note: Given the size of the dataset, it is not necessary to apply Spark. The use of Spark is for demonstration purpose.*

## Preprocessing & EDA
### EDA
- Target: imbalanced. More ham messages.
- Feature: text
  - Wordcloud
  - Special characters
    - Spam messages are more likely to use exclamation marks.
    - There's not much difference in use of question marks and punctuations in general.
    - Spam messages are more likely to use numbers.
  - URLs
    - Spam messages are more likely to use URLs.
  - Message length
    - Spam messages seem to be longer.
#### Visualization
- A [Tableau dashboard](https://public.tableau.com/profile/qi.feng1229#!/vizhome/SpamAnalysis/SpamAnalysisfromTrainingData?publish=yes) was established to summarize exploratory results.

<img src="https://github.com/lullaby1024/Spam_Detection/blob/master/img/tableau_demo.gif" width="80%">

### Preprocessing
- Missing values: dropped
- Text preprocessing (integrated in model pipeline)
  - Text
    - Removal of special characters (punctuations and digits): `TextFilter`
    - Tokenization: `Tokenizer`
    - Removal of stopwords: `StopWordsRemover`
    - Stemming (`Stemmer`)/Lemmatization (`Lemmatizer`)
      - Both increase recall, with lemmatization giving up some of that recall to increase precision
      - Stemmer is faster
      - Lemmatizer returns actual words
      - For our task, since **precision** is a priority, we will use lemmatizer to get the word roots.
    - Vectorization
      - CountVectorizer (`CountVectorizer`)/TF-IDF(`HashingTF`, `IDF`)
    - *Remark:* `TextFilter`, `Stemmer` and `Lemmatizer` are custom transformers, where `Stemmer` is based on `SnowballStemmer` and `Lemmatizer` is based on `WordNetLemmatizer` from NLTK.
  - Label: map to numerical indices
    - 'ham' -> 0, 'spam' -> 1
- Train-test-split: test_size=0.2
  - 4502 training, 1071 test

## Model
### Model Summary
<img src="https://github.com/lullaby1024/Spam_Detection/blob/master/img/models_summary.png" width="40%">

- Test metric: accuracy
- SVM and Naive Bayes have better performances and are more efficient in terms of runtime.
  - Indeed, these are among the most popular classifiers for spam detection.
- Tree-based models have poorer performances than the baseline, which might be attributed to overfitting.
- Model performances improved after tuning.

### Evaluation
<img src="https://github.com/lullaby1024/Spam_Detection/blob/master/img/svm_cm.png" width="30%">

Precision = 0.99 Recall = 0.92 F1 = 0.95

- As mentioned before, we would like to minimize FP (and thus maximize precision). Indeed, the tuned SVM has a lower FP (top-right cell) and a precision as high as 0.99.

### Feature Importance
Based on the coefficients, the top 30 words that will be predicted as spam by our model were plotted.

<img src="https://github.com/lullaby1024/Spam_Detection/blob/master/img/svm_fi.png" width="90%">

<img src="https://github.com/lullaby1024/Spam_Detection/blob/master/img/nb_fi.png" width="90%">

- Such a finding aligns with the wordcloud we formed before.
- In conclusion, common spam-trigger words are
  - Manipulative words, e.g., 'call', 'txt'/'text', 'msg', 'subscription'
  - Needy words, e.g., 'free'/'freephone'/'freemsg', 'new', 'prize'
  - Cheap words, e.g., 'win'
  - Far-fetched words, e.g., 'fancy', 'fantasy'
  - Sleezy words, e.g., 'order'
- In addition, spams seem to include links, e.g., 'link', 'www', 'com'.
