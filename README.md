### Fashion search demand: does last season's model still work this season?



A learning project on public data. Fashion demand is seasonal and shifts in ways no model sees coming, so I asked a simple question: if a forecasting model is trained on past seasons and then left alone, how quickly does it stop working, and does retraining it help?



##### Main finding

A forecasting model left unchanged lost accuracy gradually, from about 9.7% error in its first year to 10.9% afterwards, with no sudden failure. Retraining every week kept the error at about 10.3% and was better in 11 of the following 15 months, though the gain was small. Repeating last year's pattern was the least reliable approach.



##### Data

Weekly UK search interest from Google Trends for 24 fashion terms, from 24 October 2021 to 18 October 2026 (261 weeks). Retrieved on 25 October 2026.



Terms: trench coat, puffer jacket, winter coat, christmas jumper, scarf, gloves, boots, swimwear, bikini, sandals, sunglasses, shorts, maxi dress, linen trousers, wedding guest dress, party dress, jumper, cardigan, hoodie, blazer, raincoat, jeans, leggings, trainers.



This is search interest, not sales. Google Trends gives each term a relative 0 to 100 index, so values can be compared over time within one term but not between terms. I use it as a proxy for demand. Google Trends data is sampled, so a repeat download can differ slightly. The raw data file is not included here, and Google's terms of service apply to it.



##### How to run

pip install -r requirements.txt

python fashion\_search\_demand.py



The first run downloads the data (about 5 to 10 minutes, to stay within Google's rate limits) and saves it locally. Later runs reuse the saved file. Outputs go to results/.

##### 

##### Methods

* Baselines: the same week last year, last week, and the average of the last four weeks.
* Model: gradient boosting on the last four weeks and a 12-week average, plus time of year and the search term.
* Frozen versus retrained: the frozen model is trained once on the first 55% of weeks (those before 21 July 2024). The retrained model refits every week, using only earlier weeks. 
* Metric: WAPE (total absolute error divided by the total search index), shown by month. 



##### Results

|Approach|First 12 months after 21 July 2024|After 12 months|
|-|-|-|
|Same week last year|14.4%|15.7%|
|Average of last 4 weeks|15.4%|16.8%|
|Last week|10.9%|11.5%|
|Gradient boosting, frozen|9.7%|10.9%|
|Gradient boosting, retrained weekly|9.8%|10.3%|



* Both gradient-boosting models beat every baseline, but the gain over simply repeating last week is modest (about 1 percentage point).
* In the first year, frozen and retrained were roughly equal (9.7% and 9.8%).
* After the first year, retraining was better in 11 of the 15 months from August 2025 to October 2026 (October 2026 is a partial month), and in each of the last five months, by 0.6 to 2.3 percentage points.
* The "same week last year" baseline was erratic from month to month (for example, 29.5% in February 2026), which suggests the seasonal pattern shifts between years. 



Full tables are in results/summary\_wape.csv and results/monthly\_wape.csv.



##### Limitations

* Search interest is a proxy for demand, not sales, and each term is scaled separately.
* UK only, five years of weekly data, and 24 terms that I chose myself. The results describe this selection.
* The differences between frozen and retrained are small, and some months go the other way, so this shows a trend, not a proven effect. With about two years of testing, there are only two repeats of each season.
* Google Trends samples its data, so numbers can shift slightly between downloads.
* Fixed model settings with no tuning, and no external information such as weather, prices or promotions.
* This is not a production forecasting system.



##### Files

|File|Purpose|
|-|-|
|fashion\_search\_demand.py|Download, features, models, evaluation and plots|
|Requirements.txt|Python dependencies|
|results/summary\_wape.csv|Error by approach and period|
|results/monthly\_wape.csv|Error by month and approach|
|results/weekly\_search\_interest\_used.csv|The weekly series used (omit from the repository if you prefer not to redistribute Google Trends data)|
|results/seasonality.png, results/forecast\_error\_by\_month.png|Charts|



