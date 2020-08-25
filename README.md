# Spam Detection

## Introduction
The goal of this project is to identify spam messages using ML models. A walk-through of the project is given below.

## Data Import
- **Data Source**: [SMS Spam Collection Dataset](https://www.kaggle.com/uciml/sms-spam-collection-dataset), which is a set of SMS tagged messages that have been collected for SMS Spam research. 
- 5,574 English messages, tagged as ham (legitimate) or spam.

*Side note: Given the size of the dataset, it is not necessary to apply Spark. The use of Spark is for demonstration purpose.*

## Preprocessing & EDA
### EDA
- Target distribution: imbalanced. More ham messages.
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
- Train-test-split: test_size=0.2
  - 4502 training, 1071 test
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
      - For our task, **precision** is a priority, as users don’t want to miss important messages and therefore decreasing FP is priority. Hence, we will use lemmatizer to get the word roots.
    - Vectorization
      - CountVectorizer (`CountVectorizer`)/TF-IDF(`HashingTF`, `IDF`)
    - *Remark:* `TextFilter`, `Stemmer` and `Lemmatizer` are custom transformers, where `Stemmer` is based on `SnowballStemmer` and `Lemmatizer` is based on `WordNetLemmatizer` from NLTK.
  - Label: map to numerical indices
    - 'ham' -> 0, 'spam' -> 1

## Model
