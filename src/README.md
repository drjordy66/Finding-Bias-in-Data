# Steps for Reproducing this Analysis

_The below steps directly reference the .ipynb in this directory and can also be found in the .ipynb file_

Import necessary libraries that will be used

## Step 1: Getting the article and population data

The wikipedia article dataset, "Politicians by Country from the English-language Wikipedia," was obtained from Figshare on 10/29/2017. It was downloaded as a zipped folder and the `page_data.csv` was extracted from /country/data.

https://figshare.com/articles/Untitled_Item/5513449

- CC-BY 4.0

The population dataset, "Population Mid-2015," was obtained from the Population Reference Bureau on 10/27/2017. The link for this data is NOT provided and it is NOT included in the repository as it is copyrighted.

- Copyright © 2016, Population Reference Bureau. All rights reserved.

The two datasets are read into pandas DataFrames below. The revid for the wikipedia article dataset is set to be a string for merging purposes later on. The population dataset excludes the first line which is a title for the dataset and removes all commas in numbers (population counts).

## Step 2: Getting article quality predictions using ORES

Data was gathered from the ORES (Objective Revision Evaluation Service) API, 2017. It was obtained on 10/29/2017. While no license was found on ORES, it has been attributed to the same license as the Wikimedia Foundation.

https://wikimediafoundation.org/wiki/Terms_of_Use/en

- CC-BY-SA 3.0

Passing the rev_ids through the ORES API, could be done individually or in batches. Based on the number of calls to the API, it is much quicker to batch the revids, however, the API only accepts a certain number of revids per call. After some trial and error, the acceptable number of revids seemed to be anything less than 140. For safety the revids were batched in groups of 100. Below a list of lists is created where each nested list contains 100 revids.

Next, two lists are created. One to store the article quality prediction and another to account for any missing revids. This was implemented after the wikipedia article dataset was updated and the ORES API call returned an error--debugging yielded the addition.

The revid list created above is then looped such that the API is sent a request with a batch of 100 revids. After each API call, the response is looped and each revid is parsed to obtain the prediction. That prediction is appended, as well as the revid, to the predictions list. If no prediction is found and a KeyError is returned, an 'NA' along with the revid is appended to the predictions list and the revid alone is appended to the missing revids list.

__NOTE: At the time the API was called and the cleaned dataset was created only two revids were missing. The ORES API call has been rerun since this time and is now showing more missing revids. The cleaned dataset is imported in a later step, and was created using the ORES API response from 10/29/2017.__

## Step 3: Combining the datasets

The predictions obtained from ORES are loaded into a pandas DataFrame with column names added. Duplicate revids were originally dropped from this DataFrame prior to the wikipedia article dataset being updated. The predictions obtained from ORES and the page_data obtained from Figshare are then merged on the revid, essentially appending the article quality prediction to the page_data. This new page_data is then merged with the population dataset on the country/Location ('country' is the attribute name in page_data and 'Location' is the attribute name in population_data) to obtain a cleaned dataset that is in the below format:

column name | value
--- | ---
country | str
article_name | str
revision_id | str
article_quality | str
population | str

## Step 4: Analysis

So as to reproduce the analysis, the cleaned data is loaded from a saved static file. The analysis could potentially change depending on data sources from earlier, such as noted regarding the ORES API response.

First all of the countries are identified and duplicates are removed. Then the number of articles for each country are counted using a `groupby` (__NOTE: This includes articles with missing revids from the API call__). Next, the high-quality articles are returned in a similar fashion, again listing the country and the count, with countries that have no high-quality articles having a '0' for the count. The population for each country is obtained by using a `groupby` and retrieving the `max` which is also the `min`, but since there is only one possible population value assigned to each country, this does not matter.

Now that the data has been obtained in a per country format, the simple calculation is performed for articles per population and percentage of high-quality articles for each country and loaded into an analysis DataFrame with the following structure:

column name | value
--- | ---
country | str
articles_per_population | int
percentage_hq_articles | int

## Step 5: Tables

The following function converts a 2-column pandas DataFrame into a markdown style table for easy implementation on github or any other markdown environment.

Here the four visualizations are created by sorting the analysis DataFrame, either ascending or descending depending on if it is for the highest-10 or lowest-10, and keeping only those first 10 records.

__NOTE: Upon performing this operation it was seen that there were more than 10 countries with 0.00% high-quality articles. As such, a bar graph representation of these would not be useful and instead a list was created. Also, as all 10 showed 0.00%, the analysis was performed in such a way that it listed all countries with 0.00% high-quality article percentage so as not to give favor to particular countries.__

The bar plots were included as they has already been created prior to the update that they were not necessary for the purposes of the assignment.

## Step 6: Reflection

After performing this analysis, we can easily look at the bar graphs/tables and come to some conclusions regarding which countries have more articles per population, and which countries have a higher percentage of high-quality articles. Whether or not these conclusions are valid brings up the concept of bias and its consequences. We will first look at the results from the analysis then we will look at the data that was used for this analysis and how bias may affect the data and ultimately our analysis and conclusions.

The first table, "10 highest-ranked countries in terms of number of politician articles as a proportion of country population," appears to be comprised of mostly small (low population) countries. This makes sense as we are looking at the number of politician articles as a percentage of the total population; a smaller denominator yields a higher percentage (assuming a static numerator). The numerator, number of politician articles, is not static, but the variance in number of articles for countries when compared to the variance in population for countries is so much smaller that it is negligible. After thinking about the first table in this way, we would expect that the second table, "10 lowest-ranked countries in terms of number of politician articles as a proportion of country population," would be dominated by countries with large populations. This also appears to be the case. These first two tables do not seem to show any insightful or significant information as the driving force behind them in the population of each country. Possibly using a better normalization other than percentage would yield interesting results.

Looking at the third and fourth tables with respect to high-quality articles as a percentage of total number of articles we see a different story than that of the first two tables. The countries that appear in the third table, "10 highest-ranked countries in terms of number of high-quality (GA/FA) articles as a proportion of all articles about politicians from that country," appear to be countries that are more popular in current events and the news. North Korea stands out as it has nearly double the number of high-quality articles as a percentage of total articles over the number two country. Using this insight from the third table, the fourth table, "10 lowest-ranked countries in terms of number of GA and FA-quality articles as a proportion of all articles about politicians from that country," was mostly countries that are not seen in current events and media streams. Tables 3 and 4 alone show that there are most likely some bias affecting high-quality articles.

I believe that there are biases in both Wikipedia articles and potentially the ORES API machine learning algorithm. Since most of these articles are written by English speaking people (possibly with American perspectives, or at least a "western" perspective), it does not surprise me that the countries with more "high-quality" articles are those that are deemed popular with respect to media and politics in western culture. I would expect that countries that are more popular in western culture media would receive more attention from editors on Wikipedia, resulting in more "high-quality" articles. This is biased in and of itself.

We also must think about the bias brought in by the ORES API. We are assuming that it is accurately scoring these articles. In the documentation it mentions that ORES is, "...training a model to replicate the article quality assessments that humans perform..." Humans are inherently biased! Potentially the model used for ORES has been trained on editors with bias political or cultural views, creating a bias model.

I found this analysis (specifically analyzing the percentage of high-quality articles) to be quite interesting. It helped me look at the process of data analysis from an overarching perspective in which we need to question the legitimacy of all sources of data. We need to recognize and account for any bias that may affect the analysis and report it. While it doesn't have to prevent us from performing the analysis, it should be acknowledged and considered.
