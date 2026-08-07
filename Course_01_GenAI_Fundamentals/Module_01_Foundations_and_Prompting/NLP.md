# Natural Language Processing
## Cycle:
Unstructured text -> NLP -> Structured data
From left to right is NLU (Natural Language understanding).

From right to left is NLG (Natural Language generation).

## Use cases of NLU:
* Machine translation
* Virtual Assistance (Siri or Alexa) & chatbots
* Sentiment Analysis
* Spam Detection

# Stages of NLP
## 1st stage: Tokenization
Breakdown of a string into chunks
### Stemming
Ran, running, run are all stemmed into run by removing prefixes and normalizing the tense.
* Drawback: it dose not work well with words like universe and university but other tool is used for that like:
### Lemmatization
Takes a given token and learn its meaning through a dictionary definition and from there can drive its root or its lem. 
* example: better -> good, but if stem used it would be bet.
### Part of speech tagging
Helps to define if the word is verb or noun.
* What will u make? vs What make is your laptop? (Verb vs noun on same word)
### N.E.R (Named Entity Recognition)
For a given token, is there an entity associated with it?
* Arizona -> USA state, Ralph -> Person's name
