# homelessness_in_ireland

## Purpose
Ireland is currently experience its worst housing crisis in its 107 year history Infact the number of homeless people currently in ireland is worse than those recorded during and after the great famine of the 1850's [housing crisis](https://www.euronews.com/2023/07/06/irelands-housing-crisis-millennials-a-generation-sacrificed#:~:text=The%20housing%20crisis%20in%20Ireland,in%20the%20mid%2D19th%20century).

In these unprecedented cicumstances we find ourselves in, it is vital to raise awarness and do what we can to fix this impending disaster situation.
Currently there are estimated 12'847 homeless people in ireland the grate majority being in dublin. 
[Link](https://homelessnessinireland.ie/#:~:text=Homelessness%20is%20an%20issue%20experienced,accessing%20emergency%20accommodation%20in%20Ireland.)

This is a multifaceted issue but in my opinion is mainly due to massive shortages in available rooms and properties, coupled with greed of property owners. From personal experience, some landlords prefer cash payments as not to pay tax, and if a tenent is to report them they face getting evicted and evicting the other people suffering with them and living in the squalid conditions some stay in. The RTB, Threshold, citizens information are all powerless to this loophole and thus nothing will change until the government steps in and takes care of the citizens of the Ireland first to ensure the people born and raised here are taken care of for their effort to set up and grow in this, most beautiful country in the world.

### limitation in my analysis

1. The data source has some missing data, for childern numbers there are whole year gaps in the data, so it would be difficult to represent any trend. This is also the case for the homeless passport status, weather Irish, European or non-european citizen. they are therefore omitted from analysis.
2. we only have data that dates back to 2019. This is a govenment resource and i have not been able to find any more complete source.

   
## approach

1. Connecting to api using python
2. Gather the data into a csv file
3. Clean data in google sheets
4. Link data to tableau for visualization


## connecting to the APi and extracting data
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

Once this is complete and run it will produce a file called Homelessness Report.xlsx
Ensure that the data is cleanesed 

## Next we import data into to google sheets 

This can be see in the image below:
![Gsheets](https://github.com/nabeels91/homelessness_in_ireland/blob/main/Gsheets.png)


## Connecting to tableau public:

In the connect pane we pick connect to a server: google Drive and specify the location when prompted after entering your google credentials.
The data will look as follows in Tableau:
![tableau](https://github.com/nabeels91/homelessness_in_ireland/blob/main/Tableau%20import.png)

## Worksheets:

## Ensuring the correct display of the regions
We use the following to ensure that the regions surveyed by the government source of our data and the tableau mapping are in line so we can correctly use the map graphing feature in Tableau.

[nuts_regions](https://en.wikipedia.org/wiki/NUTS_statistical_regions_of_Ireland)

Any edits that need to be made should be done . I added missing regions into the map manually. check and correct.
![editing map](https://github.com/nabeels91/homelessness_in_ireland/blob/main/editing%20map.png)


## Dashboard creation
[Online dashoboard from tableau public on my computer to tableau public online](https://public.tableau.com/app/profile/nabeel4512/viz/HomelessnessinIreland/homelessnessinIreland?publish=yes)

![Dashboard](https://github.com/nabeels91/homelessness_in_ireland/blob/main/dashboard.png)






