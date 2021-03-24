![](./banner.png)

# Predicting the severity of side effects of medicinal drugs marketed in the UK

### Overview
This capstone project was completed as part of the General Assembly Data Science Immersive course in London (completed remotely due to COVID lockdown restrictions).

I completed this end-to-end project within 6 weeks, from creating a problem statement, collecting data and creating a database from scratch, to presenting my findings to a non-technical audience.


### Files in this repository
- [Presentation slides](https://github.com/jocelynho/ga_capstone/blob/57f6d026c7c1f9d1b717007e2170079e1a1de7d3/slides/Jocelyn_Ho_Capstone_Presentation.pdf):
    - Slides were aimed at a non-technical audience, and were used for the final capstone presentation during the course.
- [Technical report](https://github.com/jocelynho/ga_capstone/blob/8d70a4947382c13230499eac0c6d7ca44fa506d2/technical_report/README.md):
    - This includes introduction, objectives, methodology, information on the dataset, EDA, findings, conclusion, and future work.
- Folders containing Jupyter notebooks `(.ipynb)`:
    1) [Data Collection](https://github.com/jocelynho/ga_capstone/blob/master/1_data_collection) - 3 notebooks 
    2) [Creating Dataset]()
    3) [Exploratory Data Analysis (EDA) and Data Modelling]()



### Prerequisites for this project
Key libraries used in this project include:
- For web scraping:
```python
requests
BeautifulSoup
Selenium
ChromeDriver/ WebDriver
tqdm
```
- For creating the database:
```python
sql from pandas.io
```
- For plotting graphs:
```python
seaborn
matplotlib
```
- Other general libraries:
```python
sklearn
pandas
numpy
```


### License
For full details of the respective licences, please visit the following pages:
- [Yellow Card Scheme](https://yellowcard.mhra.gov.uk/iDAP/)
- [UCI Drugs.com reviews Data Set](https://archive.ics.uci.edu/ml/datasets/Drug+Review+Dataset+%28Drugs.com%29)
- [DrugBank Online](https://go.drugbank.com/)


### Citations
> Wishart DS, Feunang YD, Guo AC, Lo EJ, Marcu A, Grant JR, Sajed T, Johnson D, Li C, Sayeeda Z, Assempour N, Iynkkaran I, Liu Y, Maciejewski A, Gale N, Wilson A, Chin L, Cummings R, Le D, Pon A, Knox C, Wilson M. DrugBank 5.0: a major update to the DrugBank database for 2018. Nucleic Acids Res. 2017 Nov 8. doi: 10.1093/nar/gkx1037.
> Felix Gräßer, Surya Kallumadi, Hagen Malberg, and Sebastian Zaunseder. 2018. Aspect-Based Sentiment Analysis of Drug Reviews Applying Cross-Domain and Cross-Data Learning. In Proceedings of the 2018 International Conference on Digital Health (DH '18). ACM, New York, NY, USA, 121-125. DOI: [Web Link](https://dl.acm.org/doi/10.1145/3194658.3194677)
