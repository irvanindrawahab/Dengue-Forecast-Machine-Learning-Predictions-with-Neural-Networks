# Predicting Dengue Cases

## Problem Statement

Dengue is a viral infection that spreads from mosquitoes to people. It is more common in tropical and subtropical climates. Most people who get dengue will not have symptoms. But for those who do, the most common symptoms are high fever, headache, body aches, nausea, and rash. Some people develop severe dengue and need care in a hospital, and in severe cases, dengue can be fatal.

Singapore an ideal environment for mosquito breeding due to

- High temperatures, rainfall, and humidity
- Densely populated city with three million of residents live in HDB flats

Therefore, it is crucial to predict dengue cases to allow government (NEA / MOH) and public to take necessary preventive measures or strategize the Dengue Prevention Program.

## Data Preparation

### Data Colletion

- Temperature data - monthly maximum, minimum and mean temperature of Singapore
- Dengue data - weekly diseases data which includes dengue and dengue heamorhagic fever
- Rainfall data - monthly total rainfall and total number of rainy days
- Google trends - dengue, dengue fever, and dengue cluster search trends across 2012 to 2022

### Data Cleaning and Preprocessing

- Filtering the diseases data to only include 'Dengue Fever' or 'Dengue Haemorrhagic Fever'
- Aggregating the weekly time series data into a monthly format

## Exploratory Data Analysis

### Dengue Trends Across Years

- Monthly Dengue Trend in Singapore from 2012 to 2022
- Multiple spikes in cases are accompanied by some years with lower incidence
- Each year is depicted by a different color in the graph

To understand the trend further, we evaluate for each period

#### Serotype Switch: 2013 - 2016

- A large epidemic stretching over two years in 2013 - 2014 associated with a switch from serotype 2 (DENV-2) to serotype 1 (DENV-1)
- Drastic drop in dengue cases from the gravitraps deployment, ramping up of inspections, and population immunity
- Outbreak following a switch from DENV-1 to DENV-2 but quickly brought under control

#### Controlled Dengue: 2017 - 2018

The lowest figure in the past several years with no uptick of cases observed, attributed to:

- Immunity built after a high number of cases from 2013-2014 and early 2016
- Community and NEA step up inspection
- Implementation of network of Gravitraps

#### Covid-19 Period: 2019 - 2022

- Uptick due to the switch to DENV-3 serotype and the warmer months in 2019
- In 2020, resurgence of epidemic dengue during Covid-19 due to the rise of DENV-3 and DENV-4 serotypes and the work-from-home arrangement increased probability of contact with mosquitos
- In 2022, another resurgence due to waning immunity, rise of DENV-3, and higher mosquito population due to increased construction

### Weather Conditions vs Dengue Cases

- Maximum and minimum temperature have strong correlation with mean temperature - hence, we are using mean temperature
- Total number of rainy days and total rainfall also have strong correlation - hence, we are using total rainfall

Research suggests that mean temperature and rainfall precede dengue cases by 3–4 months, hence, we apply shift on the dengue cases. However, we are seeing weak correlation between weather conditions to dengue cases and lead duration

### Dengue Clusters and Habitat

- Plot the GeoJSON data from data.gov.sg
- Clusters and habitats in close proximity
- There are clusters that are not near habitats

### Dengue Google Trends

- Keywords searched: Dengue, Dengue Fever, and Dengue Cluster
- The trends show that the dengue related google search exhibit similar trends with the dengue cases, all points being standardized
- Strong correlation between dengue cases and google trends

Applied lead 1 month of dengue cases and still observe strong correlation. This shows that Google Trends can potentially be used for an early warning system before dengue outbreak

## Modelling

Time Series modelling with ARIMA, SARIMAX, RNN and LSTM. Models were evaluated based on RMSE.

### ARIMA and SARIMAX - Baseline

ACF plot on dengue cases shows slow decay which suggests that the series is non-stationary, hence differencing is needed. After differencing, the series is now stationary.

For the ARIMA and SARIMAX modeling, we used Auto Arima modeling with result summarized as below:

- ARIMA (2, 1, 3): 2,764 RMSE
- SARIMA (4, 1, 0): 2,919 RMSE

### Neural Networks

Data pre-processing prior to fit into Neural Networks model:

- Log was used to standardise data
- Varied lookback period - indicates how many previous data points will be used to predict the current data point
- Train test split of 80/20 ratio

Result is summarized as follows:

- RNN - single feature: 1,176 RMSE
- **LSTM - single feature: 940 RMSE**
- RNN - multivariate model: 1,114 RMSE
- LSTM - multivariate model: 1,536 RMSE

### Evaluation

Log transformation was used to scale the data over Min Max scaler or standard scaler for the following reasons:

- A log transformation reduces the impact of outliers. Outliers (spikes in dengue cases) can have a large impact on the mean and standard deviation of the data, which can prove to be problematic.
- When using a log transformation as opposed to a MinMaxScaler, it is noticed that though the overall RMSE scores performed slightly worse for the test set, there was lesser overfitting and the RMSE scores for train and test sets were closer.

In the single feature LSTM vs RNN, the LSTM model did better than RNN because during backpropagation, RNNs suffer from the vanishing gradient problem, where the gradient can become very small and the weights of the neurons are not effectively updated. This makes it difficult for an RNN to learn and capture long-term dependencies in the data. LSTM networks, with their gating mechanisms, are designed to combat this problem.

## Recommendation

### Project Wolbachia

Release of non-biting male Wolbachia-carrying mosquitoes to mate with urban female mosquitoes. When they mate, the eggs derived from these matings do not hatch, thereby, reducing the population of Aedes mosquitoes in the field.

According to the initial Wolbachia phases by NEA as well as the implementation globally, Wolbachia can reduce dengue cases by 70%. The cost of Wolbachia implementation for the whole of Singapore is SGD 40 million.

Total Economic Impact from dengue is calculated by:

- Direct cost: Cost incurred from consultation and hospitalization - SGD 1,356 per dengue case
- Indirect cost: Reduction of work productivity and time required to provide caretaking - SGD 1,064 per dengue case
  The total economic impact per dengue case is calculated to be SGD 2,420.

The benefit of Project Wolbachia can be calculated by multiplying the total economic impact per dengue case with the 70% of the annual predicted dengue case in the coming years, and subtract it with the implementation cost of SGD 40 million.
