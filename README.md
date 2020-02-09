#Data-Driven Risk Stratification for Clostridium Difficile Infection in Intensive Care Unit

This folder includes the original codes used to query MIMIC-III database, creating datasets, and final model training and validation.
This project does not provide access to MIMIC-III database and the ipython files don't reveal raw data of the database.


B.	Methods

The objective of this study is using technique of natural language processing (word embedding) to utilized information from unstructured data in EHR and other statistical learning methods to build a data-driven risk stratification for Clostridium Difficile infection in ICU. MIMIC database is used for model building and selection. Subsampling technique is used to tackle the imbalanced data. Several statistical learning methods are validated internally by AUC and c-statistics.


B.1.	Database

MIMIC-III is a large, single-center database of de-identified information pertaining to patients admitted to an intensive care unit at Beth Israel Deaconess Hospital from 2001 to 2012. It includes more than 40,000 patients data including vital signs, medications, laboratory measurements, notes charted by care providers, procedure codes, diagnostic codes, imaging reports, hospital length of stay, survival data, and more .


B.2.	Cohort Selection

For simplicity, only the first ICU stay of each hospital admission were included for analysis. Newborn and elective ICU admissions were excluded. ICU length of stay shorter than one day was used as proxy to elective ICU admission. The data was split into training set (80%), validation set (10%), and test set (10%). Test set was isolated and was used only to test and compare the performance of models.

B.3.	Features selection

CDI related symptoms were extracted from progression notes written by both physicians and nurses during ICU admissions. A list of synonyms lexicons extracted from the Unedified Medical Language System (UMLS) are used as a baseline vocabulary. It includes terms that describe the cardinal symptoms of Clostridium difficile infection (e.g., diarrhea.) and minor symptoms (e.g., abdominal cramping pain, nausea, and anorexia). For concept discovery and lexicon expansion, NimbleMinner , a software using machine learning methods (word embedding models and positive only labels learning) to identify similar clinical concepts, takes the baseline vocabularies as input and generates a list of candidate synonyms as output. The candidates are manually examined and selected to expend the vocabulary list. This process will iterate until no new concept is added. Common variation of concepts, misspelling, and abbreviations are included in the vocabulary list. In the final vocabulary list, diarrhea has 130 concepts, abdominal cramping pain 19, nausea 61, and anorexia 33. Finally, the notes are labeled at the note level for whether certain concepts exist in the context. Fever event is determined by a single observation of temperature > 100 F from charted data.
The other well-known risk factors were also extracted such as antibiotics use, PPI, steroid, intubation, GI tube, hemodialysis, age, hospital length of stay, and Elixhauser comorbidity scores. Records of drug prescriptions which’s duration was shorter than 1 day are excluded since it is likely to be an incorrect order entry which was canceled within a few hours. Antibiotics with higher risk of CDI include broad spectrum cephalosporin, clindamycin, amoxicillin, ampicillin, and fluoroquinolone. Elixhauser comorbidity scores is a weighted aggregated system which includes 31 categories of diseases.
There are around 27% of ICU encounters which doesn’t have any progression notes while only 5% of patients’ age are missing. Missing data in categorical variables are imputed as negative and those in continuous variables are imputed with mean.

B.4.	Primary outcome

The primary outcome of this study is the occurrence of CDI event. Since the MIMIC database does not provide information about the diagnostic tests used for CDI, several indicators are used as proxy as described in the literature . These are stool culture clostridium difficile positive, prescription of oral or rectal vancomycin longer than one day, prescription of oral or rectal metronidazole longer than one day with a final diagnosis of CDI ICD9 code, and ICU callout ㄐfor CDI precaution. If there are several incidences of outcomes, only the first event is used as the endpoint. For instance, a patient had a positive stool culture, then was prescribed with oral vancomycin, and had an ICU precaution callout before discharging. The time of the first event positive stool culture is used as the endpoint in this case. For those of negative cases, the data is collected until discharging from ICU.


B.5.	Model building

Resampling technique is used to tackle the imbalanced dataset (positive cases take up only 1.3% of the cohort). Down sampling with a fraction of 40 was used to create 20 subsets for building an ensemble classifier. The model is trained and validated on each subset. The classification is determined by the average probability of the result from each subset. The optimal threshold for classification is tested with a grid search manner to find the best AUR. Two ensemble classifiers are built. The first is a full model that includes all the variables. The second doesn’t include variables derived from unstructured variables (e.g. major_symptom, minor_symptoms). Lasso logistic regression, random forest classifier, and gradient boosting tree model are tested in both ensemble classifiers and are validated by c-statistics and AUC internally.


a. Johnson, A.E.W., et al., MIMIC-III, a freely accessible critical care database. Scientific Data, 2016. 3(1): p. 160035.
