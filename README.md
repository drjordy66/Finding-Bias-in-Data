# A2_Bias_in_data

## Organization of the project

The project has the following structure:

```
data-512-a2/
  |- analysis/
     |- README.md
     |- viz1.png
     |- viz2.png
     |- viz3.png
  |- data_clean/
     |- README.md
     |- combined_data.csv
  |- data_raw/
     |- README.md
     |- page_data.csv
  |- src/
     |- README.md
     |- hcds-a2-bias.ipynb
```

### Goal

The goal of this assignment is to explore the concept of 'bias' through data on Wikipedia articles - specifically, articles on political figures from a variety of countries.

### Licenses and links

The wikipedia article dataset, "Politicians by Country from the English-language Wikipedia," was obtained from Figshare on 10/29/2017. It was publicly posted by Oliver Keyes.

https://figshare.com/articles/Untitled_Item/5513449

- CC-BY 4.0

The population dataset, "Population Mid-2015," was obtained from the Population Reference Bureau on 10/27/2017. The link for this data is NOT provided and it is NOT included in the repository as it is copyrighted.

- Copyright © 2016, Population Reference Bureau. All rights reserved.

Data was gathered from the ORES (Objective Revision Evaluation Service) API, 2017. It was obtained on 10/29/2017. While no license was found on ORES, it has been attributed to the same license as the Wikimedia Foundation.

https://wikimediafoundation.org/wiki/Terms_of_Use/en

- CC-BY-SA 3.0

### Relevant API documentation

The __ORES ("Objective Revision Evaluation Service") Wikimedia API__ ([documentation](https://www.mediawiki.org/wiki/ORES), [endpoint](https://ores.wikimedia.org/v3/#!/scoring/get_v3_scores_context)) estimates the quality of an article. The quality estimate for an article falls into one of the six following categories:

prediction | description
--- | ---
FA | Featured article
GA | Good article
B | B-class article
C | C-class article
Start | Start-class article
Stub | Stub-class article

### Cleaned data

The [cleaned data](/data_clean) file is in a `.csv` format and contains five columns in the following format:

column name | value
--- | ---
country | str
article_name | str
revision_id | str
article_quality | str
population | str

### Issues/special considerations

Combining the datasets removed rows...

???While the data requested from the API pull spans 01/01/2008 to 10/01/2017 (MM/DD/YYYY), the pagecounts data only spans 01/01/2008 to 08/01/2016. During this final month, the number of views appear significantly lower such that they could be deemed an outlier as the API pull may not have pulled views for the entire month of 08/2016 (MM/YYYY). As such, this month was included in the cleaned data, but is removed when visualizing during the analysis.

### Additional attribution(s)

Relevant information pertaining to this assignment and API documentation was gathered from [HCDS (Fall 2017) Assignments](https://wiki.communitydata.cc/HCDS_(Fall_2017)/Assignments#A2:_Bias_in_data). CC-BY-SA 3.0