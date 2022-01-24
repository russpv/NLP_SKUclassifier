# SAP Material Type NLP Neural Net Classifier
Neural net that classifies products based on text description


## Abstract

Classifying product materials into 100-1000 possible categories is typically a manual exercise at B2B chemical manufacturers, and results in incorrect classifications that are difficult to rectify once committed to the ERP. These errors propagate to downstream analytics and threaten the integrity of commercial decision-making. Contextual information is not presented to data owners at the time classifications are made. Owners can forget the heuristic classification rules (e.g. product A belongs in class M because of property X) or misread sometimes ambiguous or erroneous material text descriptions.

The goal of this project is data quality improvement and data construction acceleration by presenting data owners with initial predicted default values prior to commits as ERP master data.

Several approaches are considered:

    Random forest decision tree
    Naive Bayes n-gram (multinomial, Bernoulli)
    1d CNN
    RNN (GRU not LSTM)
    Transformer

Raw text is processed prior to modeling using pandas dataframes to do basic tokenization, removing unnecessary start and end chars. Dictionaries are built with an optional frequency threshold depending on training data size.

## Design

The classifier is trained in Google Colab and uploaded to a FastAPI server on Heroku. Because the data is consumed by the client organization in Excel, an M-language workbook query fetches model predictions into a spreadsheet.

## Model Selection

Non-NLP classifiers are ruled out to save implementation time. Accessing the proprietary product recipe database to obtain product features is time consuming, and would also entail subsequent feature engineering.

Unigram and bigram models are demonstrated as language model baselines.

For the neural nets, PyTorch Dataloaders are constructed with the dictionaries that feed the neural models in batches. The models are assembled with bidirectional GRU layers, the last hidden time step is concatenated and passed to a regularizing dropout layer prior to a linear output layer. Softmax is applied during training: done with the ADAM optimizer and cross-entropy loss.

## Data

In this real-world scenario, little existing training data was available to classify the several thousand materials. A random sampling of less than 1,000 materials was sent to a handful of individuals for manual spreadsheet completion given the calendar time allotted and used as training data.

Raw data is taken at a point in the material creation pipeline where a text description is created by the lab and is not yet sent to commercial production.

## Evaluation Method

Nothing complicated here. n-fold cross validation is done to randomize the splits and the accuracy scores are averaged (not demonstrated publicly).
