## Overview

**Motivation**: Approximately 90% of the world's data exists in unstructured formats such as web pages, emails, customer complaints, corporate documents, scientific papers, and digital books.

**Key Distinction**: Search (finding specific information) vs. Discovery (uncovering patterns and insights)

## Text Mining Process Pipeline

```
Preprocessing → Feature Extraction → Feature Selection → Data Mining Tasks
```

---

## 1. Preprocessing

### Text Cleanup

- Remove punctuation marks
- Remove HTML tags
- Basic formatting normalization

### Tokenization

- Break text into individual words (unigrams)
- Can also create N-grams (sequences of N words)

### Stopword Removal

**What are stopwords?** The most frequently used words that carry little meaning (e.g., "the", "of", "and", "to", "is")

**Why remove them?**

- Reduce dataset size by 20-30%
- Improve effectiveness of text mining algorithms
- Prevent confusion in mining algorithms

**Note**: Domain-specific stopword lists may be needed for specialized applications

### Stemming

**Purpose**: Reduce words to their root form

- Example: "user", "users", "used", "using" → "use"
- Example: "engineering", "engineered" → "engineer"

**Benefits**:

- Improve matching of similar words
- Reduce term vector size significantly

**Common Rules**:

- Remove endings (e.g., delete "s" after consonants, remove "ing", remove "ed")
- Transform words (e.g., "ies" → "y")

### Advanced Linguistic Processing

**Word Sense Disambiguation**:

- Determine which meaning a word has in context
- Normalize synonyms (e.g., "United States", "USA", "US")
- Normalize pronouns (e.g., "he", "she", "it")

**Part of Speech (POS) Tagging**:

- Parse text according to grammar rules
- Determine function of each term
- Example: "John (noun) gave (verb) the (determiner) ball (noun)"

---

## 2. Feature Generation

### Bag-of-Words Model

**Concept**: Documents are treated as collections of words where order is ignored

**Representation**: Each document becomes a vector in a term-document matrix

||Doc 1|Doc 2|Doc 3|...|Doc n|
|---|---|---|---|---|---|
|Term 1|5|0|0||0|
|Term 2|0|0|15||0|
|Term m|20|0|1||0|

**Vector Creation Techniques**:

1. **Binary Term Occurrence**: Boolean (0/1) indicating presence/absence
2. **Term Occurrence**: Count of term appearances (problematic for varying document lengths)
3. **Term Frequency**: Occurrences divided by total words in document
4. **TF-IDF**: See below

### TF-IDF (Term Frequency - Inverse Document Frequency)

**Inverse Document Frequency (IDF)**:

```
IDF_i = log(n / n_i)
```

- `n` = total number of documents
- `n_i` = number of documents containing term i
- More frequent terms receive smaller weights

**Term Frequency Damping**: Apply damping function (square root or logarithm) to prevent single words from dominating:

```
h(x_i) = f(x_i) × IDF_i
```

**Purpose**: Balance the weight of terms across the corpus and within individual documents

### Document Similarity Measures

**Cosine Similarity**: Measures angle between document vectors

**Jaccard Coefficient**: Measures intersection over union of terms

### Word Embeddings

**Criticism of Bag-of-Words**:

- Words represented as one-hot vectors (sparse, high-dimensional)
- Synonyms treated as completely different words
- No semantic relationships captured
- Vector dimension equals vocabulary size (e.g., 500,000)
- Cannot capture similarity between "I like this movie" 👍 and "I don't like this movie" 👎

**Embedding Solution**:

- Represent words as dense vectors of real numbers
- Semantically related words (e.g., "dog", "puppy") positioned close in vector space
- Based on **Distributional Hypothesis**: "Words in similar contexts have similar meanings" (Firth, 1957)

**Popular Methods**:

- **Word2Vec** (Google, 2013)
- **fastText** (Facebook AI Research)
- **BERT** (Google, 2019)

**Implementation**:

- Python: Gensim library
- RapidMiner: Word2Vec extension

---

## 3. Feature Selection

**Challenge**: Not all features are helpful; high dimensionality can hinder learning

**Pruning Methods**:

- Remove too frequent words (like stopwords)
- Remove too infrequent words (rare terms)

**Part-of-Speech (POS) Filtering**:

- Focus on specific word classes for specific tasks
- **Adjectives** for sentiment analysis (e.g., "good", "bad", "great")
- **Nouns** for text clustering (e.g., identifying topic similarities)

---

## 4. Text Mining Tasks

### Key Challenges

1. **Sparseness**: Many zero-valued attributes in vectors
2. **Non-negativity**: Word frequencies are always ≥ 0; presence is more significant than absence
3. **Side Information**: Additional metadata (e.g., in web mining) can enhance mining

### Text Classification

**Definition**: Assign predefined categories to documents based on their content

**Process**:

- Given: Labeled document collection (training set)
- Find: Model mapping features to classes
- Goal: Accurately classify unseen documents

**Applications**:

- Spam detection
- Sentiment analysis
- Document categorization
- Failure classification

**Classification Models**:

- Naive Bayes
- Support Vector Machines (SVM)
- K-Nearest Neighbors (KNN)
- Long Short-Term Memory (LSTM)

### Naive Bayes Variants

|Model|Bernoulli|Multinomial|Gaussian|
|---|---|---|---|
|**Attributes**|Binary (0/1)|Discrete frequencies|Continuous values|
|**Best For**|Short documents, small lexicon|Longer documents, large lexicon|Continuous features|
|**Considers**|Presence/absence only|Word frequencies|Probability distributions|

**Bernoulli Bayes**:

- Uses only presence/absence of words
- Ignores word frequencies and TF-IDF
- Suitable for short documents

**Multinomial Bayes**:

- Considers word frequencies
- More effective for longer documents
- Accounts for repeated occurrences

---

## Summary

Text mining transforms unstructured text into structured data through:

1. **Preprocessing**: Clean, tokenize, remove stopwords, stem
2. **Feature Generation**: Create vectors (Bag-of-Words or Embeddings)
3. **Feature Selection**: Filter relevant features
4. **Mining Tasks**: Apply classification, clustering, or other algorithms

The choice between Bag-of-Words and Word Embeddings depends on:

- **Bag-of-Words**: Simpler, interpretable, good for many tasks
- **Word Embeddings**: Captures semantic relationships, better for complex language understanding