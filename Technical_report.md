[](screenshots/banner.png)
# Technical Report

## Introduction
One of the many responsibilities of a pharmacist is to advise patients about possible reactions that may occur with medicines.[1] However, where does this data come from? Where does the data go, when a patient experiences a side effect? 

### Yellow Card Scheme
Side effects are reported using a system called the Yellow Card Scheme.The Yellow Card Scheme is run by the MHRA. It is the UK system for collecting and monitoring information on safety concerns, for example, suspected side effects of medicines or adverse incidents involving medical devices. The scheme relies on voluntary reporting by anyone, including health care professionals, patients, parents and carers. [2]

### MHRA
The Medicines and Healthcare products Regulatory Agency (MHRA) is the executive agency of the Department of Health and Social Care, and acts on behalf of the Ministers to protect and promote public health and patient safety. This is done by ensuring medicines and medical devices meet approprioate safety, quality and efficacy. [2]


### Objectives
This project aims to utilise data on side effects of drugs. Objectives include:
- To predict whether certain types of drugs are linked to different severities of drug side effect
- To predict whether certain types of side effects are more severe.

### Metrics
This project uses classification model predictions and mean cross-validated accuracy scores.

### Criteria
This project investigates side effects involving:
1. medicinal products,
2. that have a marketing license in the United Kingdom,
3. in which side effects are reported to the Yellow Card Scheme.

### Potential audience
Potential audeince includes:
1. Drug companies - to look out for certain side effects or drug categories when researching and performing clinical trials.
2. MHRA - to target certain side effects or drugs to review.
3. Health care professionals - to monitor for certain side effects or drugs.


## Methodology
[](screenshots/methodology.png)

## Data collection
3 different sources were used for this project:
1. Yellow Card Interactive Drug Analysis Profiles
2. DrugBank
3. UCI dataset on Drugs.com reviews

### 1. Yellow Card
The first source used was Yellow Card Scheme, which contains all reports on side effects and safety incidents.

#### Web scrape uing Selenium
To automate the process of downloading all `.zip` files, Selenium was used to click onto each alphabet (e.g. A), then onto each subset of alphabet range (e.g. Aa-Ad), and onto each drug (e.g. Abacavir). The unique yellow card ID for each drug was identified and obtained using Selenium and Inspect, and the downloadable links for each drug were obtained. The zip files were downloaded and subsequently unzipped.

#### Join csvs using SQL 
Each unzipped file contains 3 different `.csv` files for each drug, including:
1. case.csv - summary of each report for the drug, including patient demographics
2. event.csv - adverse reactions within each report for the drug (multiple events per case/ report)
3. drug.csv - method of administration for the drug in each report.

I decided to join `case.csv` to `event.csv` in order to retain multiple side effect instances as individual data points (rows). This was performed using SQL `left join`. The process was repeated for all drugs to create the base of my data set.

For a more detailed step-by-step process, please see [this notebook](https://github.com/jocelynho/ga_capstone/1_data_collection/1a_yellow_card.ipynb).


### 2. DrugBank
DrugBank was used to obtain additional information on the drugs from Yellow Card Scheme, such as drug targets, drug category, and the DrugBank ID. These were added to the data set I created above.

For a more detailed step-by-step process, please see [this notebook](https://github.com/jocelynho/ga_capstone/1_data_collection/1b_drugbank.ipynb).

### 3. UCI data set - Drugs.com reviews
I also decided to incorporate the Drugs.com reviews into my data set. This is a readily available data set on [UCI Machine Learning Repository](https://archive.ics.uci.edu/ml/datasets/Drug+Review+Dataset+%28Drugs.com%29).

#### Convert brand names to drug names
A big challenge of utilising this data set is to convert the brand names (e.g. Calpol) of the medicinal products to their respective proprietary drug names (e.g. paracetamol). Many of the products contain more than one active ingredient/ drug, and therefore the conversion is complex.

In order to tackle this issue, I utilised `Selenium` to obtain the active ingredients of each product from [Drugs.com](https://www.drugs.com/). I used `.split()` to separate the string of active ingredients into a list of separate strings. Then, I used `.explode()` to duplicate the reviews, so that each active ingredient has a copy of all the reviews of the product it is contained in. Lastly, I re-grouped the reviews by the DrugBank ID, so that I am able to add the conglomerated reviews and ratings to the data set I initially created.

Here is a snippet of the codes I used:
[](screenshots/brand_to_drug.png).

For a more detailed step-by-step process, please see [this notebook](https://github.com/jocelynho/ga_capstone/1_data_collection/1c_reviews.ipynb).


## Biggest Challenge with Merging Data
The biggest challenge I encountered during the 6 weeks assigned to this project was to merge all the data together. With insufficient RAM, I had trouble merging the Drugs.com reviews to my data set. Methods including SQLite, Pandas `.merge()` `.concat()` failed.

I was then introduced to a new library called `Dask` (for full destails please visit [Dask](https://dask.org/)). It uses dynamic task scheduling while supporting Pandas dataframes, and avoids excess memory use by processing small chunks of data and throwing away intermediate values as soon as possible. It enables large dataset computations on low-power laptops, which is the issue at hand.

This is how a Dask dashboard looks like:
[](screenshots/dask.png)

## More challenges
Even though I eventually managed to merge my dataset using Dask, I still encountered other issues:
- Memory leak: While trialing Dask, I did not close down my clients properly and leaked memory. I kept running out of application memory when running models, and had only resolved the issue 3 days before the due date.
- Insufficient RAM: Although I had my final dataset ready, I did not have sufficient RAM to process it. Eventually, I dropped the reviews column, and instead used the number of reviews as an indicator.
- Insufficient time: As I spent a lot of time resolving the issue of insufficient RAM, I only had 3 days to run models. I decided to sample 10% of my dataset for data modelling.

## Final data set
The final data set has:
- 1,755,219 rows,
- 16 columns: 3 text columns, 6 categorical coclumns, 4 numeric columns,
- 3 classes within the target variable (non-serious, serious, fatal).

## EDA
### Histograms showing distributions of reporter details
[](screenshots/eda_1.jpg)
- Most reporters were female
- Most reporters were direct (not reported by pharmaceutical companies)
- Most reporters were consumers (patients and/or carers)
- Most reporters were health care professionals.

### Histograms showing distributions of continuous variables
[](screenshots/eda_2.jpg)
- Most reporters were within the age-range of 40 to 70 years old.
- Most commonly reported side effects were gastrointestional, general, skin, and nervous system related.
- Most reviews were rated from 6 to 9, with a peak at about 8.5.

### Box plots showing severity of side effects against continuous variables
[](screenshots/eda_3.jpg)
- Majority of fatal side effects were reported for patients at older age ranges, from 40 to 70 years old.
- Most non-serious side effects were reported earlier on from 1990s, meanwhile most serious and fatal side effects were reported from after 2000.
- Drugs with fatal side effects seem to have higher review ratings compared to non-serious and serious side effects.

### Box plots showing types of side effects against continuous variables
[](screenshots/eda_4.png)
- Congenital and pregnancy related side effects were reported for patients from the age of 0 to 30 years old, by definition these side effects occur at or near birth. Other side effects mostly occur from 30 to 60 years old.
- Drugs related to surgical side effects mostly have the highest ratings from 7 to 8.5; those related to pregnancy side effecs have lower ratings from below 6 to about 7.5.
- Congenital side effects were reported early on from about 1985, possibly due to fewer and less stringent clinical trials and regulations, or possibly due to the thalidomide scandal. Most product issues and social circumstances related side effects are reported latest, after 2010.

### Histogram showing distribution of classes of target variable
[](screenshots/eda_5.png)
- The classes within the target variable are very imbalanced, with over 74% being serious, and 0.06% being fatal side effects. This would be addressed at a later stage, using the library `imblearn`.

### NLP on text columns
[](screenshots/nlp.png)
- `CountVectorizer` was used on drug targets and drug indications. `ngram_range` of 2 was chosen after trial and error.
- For drug targets, subunit alpha, muscarinic acetylcholine, adrenergic alpha and growth factor were the msot frequent words.
- For drug indications, influenza prophylaxis, nausea and vomiting related to pregnancy, birth control, and high blood pressure were the most common words.
- `TfidfVectorizer` was used on the drugs.com reviews. Words including blood pressure, birth control, weight gain, dry mouth, mood swings had high scores. Such side effects could also be related to birth control medications (e.g. weight gain, mood swings) and blood pressure medications (e.g. dry mouth).

## Data modeling
[](screenshots/models.png)
Logistic Regression had the highest mean cross-validated accuracy score of 0.699.

## Results
### Confusion matrix
[](screenshots/confusion_matrices.jpg)
- Both training and test set results show that the model performed relatively well in determining non-serious and fatal side effects, however was unable to accurately predict serious side effects.
- This is also reflected by the low precision and recall scores for class s (serious).

## Receiver operating characteristic curves and precision-recall curves
[](screenshots/roc_curves.jpg)
- Serious side effects performed the worst out of the 3 classes and has the lowest area-under-curve.

## Feature importances
[](screenshots/feature_importances.jpg)
### Non-serious
Side effects involving the following were least likely to be predicted as non-serious side effects:
- the brain (cerebral haemorrhage, confusional state, loss consciousness, cerebrovascula accident),
- bleeding or blood clots (gastrointestinal haemorrhage, pulmonary embolism),
- anaphylactic reaction (swelling face, lip swelling),
- death (sudden death, complete suicide),
- heart attack (chest discomfort, myocardial infarction)

Drugs targeting fatty acid receptors were predicted to be less important in non-serious side effects; while those targeting proteasome receptors were more likely to result in non-serious side effects.

## Conclusion

## Future Work

## Citations
1. General Pharmaceutical Council. What does a pharmacist do? Retrieved March 23, 2021, from https://www.pharmacyregulation.org/raising-concerns/raising-concerns-about-pharmacy-professional/what-expect-your-pharmacy/what-does-0
2. MHRA. About yellow card. Retrieved March 23, 2021, from https://yellowcard.mhra.gov.uk/the-yellow-card-scheme/


