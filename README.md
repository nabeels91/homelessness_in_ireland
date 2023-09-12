# homelessness_in_ireland
Connecting to the data.gov.ie api and analysing data related to the homeless pandemic we are currently facing.
I did this with a combination of Python to gather the data, google sheets to store the data online to connect to the tableau for anlayzing and visualing. 


## Gathering of the Data
The data is taken from: 'https://data.gov.ie/dataset?sort=score+desc%2C+metadata_created+desc&q=homelessness&theme=Housing'

We can connect to the api directly from the HTTPs using the following code:
```
# To gather all the relevent data for analysis:
# 1. Query the database
q_url = 'https://data.gov.ie/api/3/action/package_search?q=homelessness'
packages = requests.get(q_url).json()['result']

# 2a. Process results
df = pd.json_normalize(packages['results'], 'resources', 'title')

# 2b. Filter results
mask = df['title'].str.startswith('Homelessness Report') & df['format'].eq('CSV')
df = df.loc[mask, ['title', 'url']]

# To save all the relevant data into the correct format we want, i.e CSV file
# 3a. Download data
data = {}
for idx, row in df.iterrows():
    dt = pd.to_datetime(row.title.strip('Homelessness Report '))
    data[dt] = pd.read_csv(row.url)

# 4a. Merge data
out = (pd.concat(data, axis=0).droplevel(1).rename_axis('Date')
         .sort_index().reset_index())
out.to_excel('Homelessness Report.xlsx', index=False)

```

[tableau image](homelessness_in_ireland/Tableau import.png)



Once this is complete we can see the Homelessness Report.xlsx

## Next we import data into to google sheets 
this can be see in the image below:
![Gsheets](https://github.com/nabeels91/homelessness_in_ireland/blob/main/Gsheets.png)

## Connecting to tableau public:
this is very simple in tableau. In the connect pan we pick connect to a server: google Drive and specify the location when prompted after entering your google credentials.
the data will look as follows:
![tableau](https://github.com/nabeels91/homelessness_in_ireland/blob/main/Tableau%20import.png)


## ensuring the correct display of the regions
We use the following to ensure that the regions surveyed by the government source of our data and the tableau mapping are in line so we can correctly use the map graphing feature in Tableau.
https://en.wikipedia.org/wiki/NUTS_statistical_regions_of_Ireland
any edits that need to be made should. I had to add regions into the map manually. check and correct.
![editing map](https://github.com/nabeels91/homelessness_in_ireland/blob/main/editing%20map.png)

![map](https://github.com/nabeels91/homelessness_in_ireland/blob/main/map.png)





