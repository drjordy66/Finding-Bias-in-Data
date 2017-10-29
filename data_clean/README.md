### Cleaned data

The cleaned data was created using the [raw data](../data_raw) on 10/28/2017.

The cleaned data file is in a `.csv` format and contains five columns in the following format:

column name | value
--- | ---
country | str
article_name | str
revision_id | str
article_quality | str
population | str

???The integers for each column other than 'year' and 'month' represent the number of views for the respective column. For dates where no data was given for a particular API pull, a value of 0 will be used.