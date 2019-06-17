---
permalink: /NI_property_analysis_cleaning
layout: default
title: "NI Property Value Analysis - Loading and Cleaning"
redirect_from:
  - /NI_property_analysis_cleaning/
  - /NI_property_analysis_cleaning.html
---

<h1><a name="title" href="#title">Northern Ireland Domestic Property Value Data <br>Loading and Cleaning</a></h1>

Loading and properly cleaning the data is maybe the most important step of good data analysis. If you don't know what the data looks like and what the columns actually contain, instead of what you think they contain, you can't trust any of your conclusions. Here we will be loading the raw data in and examining each column for mixed data types, inconsistent data entry (fixing capitalisation or spelling mistakes), making numeric columns actually numeric and looking at missing values and what can be done to fill them out.

[Main project page](NI_property_analysis#title)

<h1><a name="Contents" href="#Contents">Contents</a></h1>


1. [Loading the Data](#Loading-the-Data)
2. [Clean Up](#Clean-Up)
    1. [Look at the data](#Look-at-the-data)
    2. [Investigating NI's 6 new counties](#Investigating-NI-s-6-new-counties)
    3. [Missing data and what can be fixed](#Missing-data-and-what-can-be-fixed)
    4. [Filling missing postcodes](#Filling-missing-postcodes)
    5. [Filling missing towns](#Filling-missing-towns)
3. [Save to File](#Save-to-File)
<p></p>

<h1><a name="Loading-the-Data" href="#Loading-the-Data">Loading the Data</a></h1>
<a href="#title">Back to top</a>

We don't need everything in the dataset, such as district/ward/street IDs so only loading in specific columns



```python
import pandas as pd
import numpy as np
NI_domestic_properties = "DomesticPropertyValue.csv"

column_types = {'District': object, 'Ward': object, 'Street': object, 'Building Name': object, 
                'Sub-building Name': object, 'Building Number': object, 'Townland': object, 
                'Town': object, 'County': object, 'Postcode': object, 'Description': object, 
                'Capital Value Non-Exempt': object, 'Capital Value Exempt': object, 
                'Property Size': object, 'Central Heating': object, 'Garage': object,
                'Property Address': object}

properties_df = pd.read_csv(NI_domestic_properties, dtype=column_types, usecols=column_types.keys())
properties_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>District</th>
      <th>Ward</th>
      <th>Street</th>
      <th>Building Name</th>
      <th>Sub-building Name</th>
      <th>Building Number</th>
      <th>Townland</th>
      <th>Town</th>
      <th>County</th>
      <th>Postcode</th>
      <th>Property Address</th>
      <th>Description</th>
      <th>Capital Value Non-Exempt</th>
      <th>Capital Value Exempt</th>
      <th>Property Size</th>
      <th>Central Heating</th>
      <th>Garage</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Antrim and Newtownabbey Borough Council</td>
      <td>Abbey</td>
      <td>Abbeyglen Crescent</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>1</td>
      <td>White Abbey</td>
      <td>Newtownabbey</td>
      <td>ANTRIM</td>
      <td>BT37 0TJ</td>
      <td>1 Abbeyglen Crescent, White Abbey, Newtownabbe...</td>
      <td>House Garden</td>
      <td>70,000.00</td>
      <td>0.00</td>
      <td>107.00 Square Metres</td>
      <td>Yes</td>
      <td>No</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Antrim and Newtownabbey Borough Council</td>
      <td>Abbey</td>
      <td>Abbeyglen Crescent</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3</td>
      <td>White Abbey</td>
      <td>Newtownabbey</td>
      <td>ANTRIM</td>
      <td>BT37 0TJ</td>
      <td>3 Abbeyglen Crescent, White Abbey, Newtownabbe...</td>
      <td>House Garden</td>
      <td>90,000.00</td>
      <td>0.00</td>
      <td>154.95 Square Metres</td>
      <td>Full</td>
      <td>No</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Antrim and Newtownabbey Borough Council</td>
      <td>Abbey</td>
      <td>Abbeyglen Crescent</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2</td>
      <td>White Abbey</td>
      <td>Newtownabbey</td>
      <td>ANTRIM</td>
      <td>BT37 0TJ</td>
      <td>2 Abbeyglen Crescent, White Abbey, Newtownabbe...</td>
      <td>House Garden</td>
      <td>70,000.00</td>
      <td>0.00</td>
      <td>103.00 Square Metres</td>
      <td>Yes</td>
      <td>No</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Antrim and Newtownabbey Borough Council</td>
      <td>Abbey</td>
      <td>Abbeyglen Crescent</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>4</td>
      <td>White Abbey</td>
      <td>Newtownabbey</td>
      <td>ANTRIM</td>
      <td>BT37 0TJ</td>
      <td>4 Abbeyglen Crescent, White Abbey, Newtownabbe...</td>
      <td>Flat (Ground Floor)</td>
      <td>42,000.00</td>
      <td>0.00</td>
      <td>56.00 Square Metres</td>
      <td>Yes</td>
      <td>No</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Antrim and Newtownabbey Borough Council</td>
      <td>Abbey</td>
      <td>Abbeyglen Crescent</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>6</td>
      <td>White Abbey</td>
      <td>Newtownabbey</td>
      <td>ANTRIM</td>
      <td>BT37 0TJ</td>
      <td>6 Abbeyglen Crescent, White Abbey, Newtownabbe...</td>
      <td>Flat (1st Floor)</td>
      <td>42,000.00</td>
      <td>0.00</td>
      <td>56.00 Square Metres</td>
      <td>Yes</td>
      <td>No</td>
    </tr>
  </tbody>
</table>
</div>



<h1><a name="Clean-Up" href="#Clean-Up">Clean Up</a></h1>
<a href="#title">Back to top</a>

Straight away there are some issues evident in the top 5 rows. Property size should be a number but includes the text "square meters". Building Name and Sub-building Name are NaN are they all NaN? What is the difference between "Full" central heating and marked "Yes". Capital Value Non-Exempt has a comma in it so won't be treated as numeric.

There are probably lots of other errors in the rest of the columns that need fixed that isn't evident in the top 5 rows. Time to dig further

<h2><a name="Look-at-the-data" href="#Look-at-the-data">Look at the data</a></h2>
<a href="#title">Back to top</a>

The first thing to do is look at the shape of the data. How many rows are we working with? Number of unique values in each column? Number of nulls? What do we expect for these values?


```python
print("Dataset shape: " + str(properties_df.shape))
for item in properties_df.columns:
    print('{:24}  {:6} {}'.format(str(item),  properties_df[item].nunique(), properties_df[item].dtype))
```

    Dataset shape: (766350, 17)
    District                      11 object
    Ward                         432 object
    Street                     22472 object
    Building Name               2881 object
    Sub-building Name           3021 object
    Building Number             3988 object
    Townland                    7507 object
    Town                         419 object
    County                        12 object
    Postcode                   41402 object
    Property Address          763885 object
    Description                 6494 object
    Capital Value Non-Exempt     654 object
    Capital Value Exempt         128 object
    Property Size              23077 object
    Central Heating                7 object
    Garage                         4 object
    

So we have 766,350 rows of 16 columns, seems about right for every house in Northern Ireland. There should be 766,350 unique addresses too but there are 2,465‬ too few.

11 districts, 432 wards, 22472 streets. I know the districts is correct and the others look about right, though these will only be wards and streets with houses on them.

My knowledge of NI geography isn't great but I'm pretty sure there aren't 12 counties, so that will need further investigation.

And why are there 7 options for central heating and 4 for whether the property has a garage?


<h2><a name="Investigating-NI-s-6-new-counties" href="#Investigating-NI-s-6-new-counties">Investigating NI's 6 new counties</a></h2>
<a href="#title">Back to top</a>


```python
properties_df["County"].unique()
```




    array(['ANTRIM', 'Antrim', nan, 'DOWN', 'Down', 'ARMAGH', 'Armagh',
           'LONDONDERRY', 'Londonderry', 'TYRONE', 'Tyrone', 'FERMANAGH',
           'Fermanagh'], dtype=object)



So unsurprisingly the data is inconsistent with its capitalisation. lets fix that.


```python
properties_df['County'] = properties_df['County'].apply(lambda x: str(x).upper().capitalize())
properties_df['County'] = properties_df['County'].replace('Nan', np.nan)
properties_df["County"].unique()
```




    array(['Antrim', nan, 'Down', 'Armagh', 'Londonderry', 'Tyrone',
           'Fermanagh'], dtype=object)



That is much better. It is likely that this is also a problem with the towns too.


```python
print("Unique towns before: " + str(properties_df["Town"].nunique()))
properties_df['Town'] = properties_df['Town'].apply(lambda x: str(x).upper().capitalize())
properties_df['Town'] = properties_df['Town'].replace('Nan', np.nan)
print("Unique towns after: " + str(properties_df["Town"].nunique()))
```

    Unique towns before: 419
    Unique towns after: 313
    

As expected that cleaned up the towns nicely too.

The last one that looks off is the central heating and the garage columns. They are small enough that we can look and the unique values directly.


```python
print("Central Heating: " + str(properties_df["Central Heating"].unique()))
print("Garage: " + str(properties_df["Garage"].unique()))
```

    Central Heating: ['Yes' 'Full' 'No' 'ASSUMED' 'Partial' 'None' nan 'FUL']
    Garage: ['No' 'NO' 'Yes' nan 'YES']
    

This time we have a mixture of capitalization, spelling errors and semingly duplicate categories. Let's make Garage just Yes/No and Central Heating to Yes/Assumed/Partial/No. Full and FUL can become Yes, None can be No.


```python
properties_df["Central Heating"] = properties_df["Central Heating"].str.replace("Full","Yes")
properties_df["Central Heating"] = properties_df["Central Heating"].str.replace("FUL","Yes")
properties_df["Central Heating"] = properties_df["Central Heating"].str.replace("None","No")
properties_df["Central Heating"] = properties_df["Central Heating"].str.replace("ASSUMED","Assumed")

properties_df['Garage'] = properties_df['Garage'].apply(lambda x: str(x).upper().capitalize())
properties_df['Garage'] = properties_df['Garage'].replace('Nan', np.nan)

print("Central Heating: " + str(properties_df["Central Heating"].unique()))
print("Garage: " + str(properties_df["Garage"].unique()))
```

    Central Heating: ['Yes' 'No' 'Assumed' 'Partial' nan]
    Garage: ['No' 'Yes' nan]
    


Looking back at the table of unique values per column we also printed the data type. Most are objects as expected for strings but Capital Value Non-Exempt, Capital Value Exempt and Property Size should all be numeric. Lets fix that. First the commas in the numbers need removed and " Square Metres" needs dropped from the property size column. Then they can be converted to numeric.



```python
properties_df["Capital Value Non-Exempt"] = properties_df["Capital Value Non-Exempt"].str.replace(",","")
properties_df["Capital Value Exempt"] = properties_df["Capital Value Exempt"].str.replace(",","")
properties_df["Property Size"] = properties_df["Property Size"].str.replace(",","")
properties_df["Property Size"] = properties_df["Property Size"].str.replace(" Square Metres","")

properties_df["Capital Value Non-Exempt"] = pd.to_numeric(properties_df["Capital Value Non-Exempt"])
properties_df["Capital Value Exempt"] = pd.to_numeric(properties_df["Capital Value Exempt"])
properties_df["Property Size"] = pd.to_numeric(properties_df["Property Size"])
```

<h2><a name="Missing-data-and-what-can-be-fixed" href="#Missing-data-and-what-can-be-fixed">Missing data and what can be fixed</a></h2>
<a href="#title">Back to top</a>

Next we want to look at what data is missing. How many empty cells does each column have, which columns does that matter in and can we fix it?

Below the type of each column is listed along with the numer of nulls in each


```python
for item in properties_df.columns:
    print('{:24}  {:6}'.format(str(item),  properties_df[item].isnull().sum()))
    #print (item, ":\t", NI_dom_prop_df[item].isnull().sum(), "\t", NI_dom_prop_df[item].dtype)
```

    District                       0
    Ward                           0
    Street                         0
    Building Name             746429
    Sub-building Name         720583
    Building Number             7639
    Townland                     251
    Town                       16102
    County                      2003
    Postcode                   52182
    Property Address            1897
    Description                  165
    Capital Value Non-Exempt       0
    Capital Value Exempt           0
    Property Size                621
    Central Heating              839
    Garage                       841
    

There are quite a few values missing but for some columns such as Building Name and Sub-building Name that doesn't matter, it is to be expected. 52182 postcodes are missing though and I plan to use the postcodes for joining other data later. Fortunately we also have the full address which may have the postcode and there are only 1897 of those missing.

<h2><a name="Filling-missing-postcodes" href="#Filling-missing-postcodes">Filling missing postcodes</a></h2>
<a href="#title">Back to top</a>

First lets get all the rows where the postcode is missing.


```python
rows_to_fix = properties_df["Postcode"].isnull()
```

Now lets look at a few addresses to see if they follow the same pattern. We will use sample here instead of head so we get a more representative view of the data


```python
pd.set_option('display.max_colwidth', -1)
print(properties_df[rows_to_fix]["Property Address"].sample(n=15))
#reset the max_colwidth to default
pd.set_option('display.max_colwidth', 50)
```

    503872    64 Corramore Road, Muggalnagrow, Garrison, Enniskillen BT93 4BA,                            
    761682    5 Ballylough Road, Ballylough, Castlewellan BT31 9NN,                                       
    645857    15 Farm Lodge Park, Greenisland, West Division, Carrickfergus BT38 8YB,                     
    44554     Lock Up Garage 4, N11 Glanroy Crescent, Croghfern, Newtownabbey, Antrim,                    
    180153    31 Derry Park Lane, Derry, Lurgan, ARMAGH,                                                  
    465221    Lock Up Garage 7, N22 Pine Street, Clooney, Londonderry, Londonderry,                       
    61710     18 Forge Walk, Carrickmannan, Ballygowan, Newtownards BT23 6SX,                             
    173713    5 Granville Road, Killycomain, Portadown, Craigavon BT63 5DN,                               
    555853    The Manse, 55 Church Road, Boardmills, Carrickmaddyroe, Lisburn BT27 6UP,                   
    720583    24 Gallion Way, Moneymore, Magherafelt BT45 7WF,                                            
    582720    Apartment 1, 6 Seymour Street, Lisnagarvy, Lisburn BT27 4XF,                                
    649206    Lock Up Garage 5, N44 Longfield Gardens, Greenisland, West Division, Carrickfergus, Antrim, 
    206667    85 Carrigart Crescent, Tullygally, Craigavon BT65 5EG,                                      
    703494    8 Elm Terrace, Gortshalgan, Dungannon BT71 6FP,                                             
    317287    Flat 5A Moveen House, Benmore Drive, Finaghy, Ballyfinaghy, Belfast BT10 0EF,               
    Name: Property Address, dtype: object
    

For those that have postcodes they seem to be always at the end of the address followed by a comma. Postcodes are annoying to work with as they aren't fixed length and can have a space or not a space. For this we will rely on the format of the address being relatively consistent and strip the postcode from the end then check it looks like a postcode rather than resorting to complex regex. At least I can count on all Northern Ireland postcodes starting with BT which is nice.


```python
# get the last 10 characters of the address
fixed_postcodes = properties_df[rows_to_fix]["Property Address"].str.slice(-10,-1)
fixed_postcodes.sample(n=5)
```




    692051    BT80 8JG,
    700812    BT45 5FA,
    560388    , Antrim,
    451352    BT48 6AZ,
    214131    BT60 1BT,
    Name: Property Address, dtype: object




```python
#replace commas and strip leading and trailing whitespace
fixed_postcodes = fixed_postcodes.str.replace(",", "").str.strip()
fixed_postcodes.sample(n=5)
```




    523913    BT74 6BJ
    762720    BT30 8RE
    134245      Armagh
    403140    BT47 4TD
    372370     st Down
    Name: Property Address, dtype: object




```python
# get only the rows that start with "BT" 
right_rows = fixed_postcodes.str.startswith('BT', na=False)
fixed_postcodes[~right_rows] = np.nan
fixed_postcodes.sample(n=5)
```




    469881    BT82 9QU
    657810         NaN
    261851    BT15 5AS
    78977          NaN
    718814    BT45 8PY
    Name: Property Address, dtype: object




```python
# add back into the dataframe
properties_df.loc[rows_to_fix, 'Postcode'] = fixed_postcodes
print("Number of missing postcodes: " + str(properties_df["Postcode"].isnull().sum()))
```

    Number of missing postcodes: 10623
    

10,623 are still missing but that is a big improvement. We can go a little further though. The nice thing about postcodes is that they are geographically clustered. Houses on the same street have the same postcode. So I can group the data by district, ward and street and then apply any postcode found to all that are missing one on that street.


```python
def setPostcode(group):
    # only modify if there are nan's but not all nan's
    if group["Postcode"].isnull().any() and not group["Postcode"].isnull().all():
        # find the first not null postcode
        group_postcode = group.loc[group["Postcode"].first_valid_index()]["Postcode"]
        # apply the group postcode to each with a null postcode
        properties_df.loc[group.index[group['Postcode'].isnull()], 'Postcode'] = group_postcode
    return group


properties_df.groupby(['District','Ward', "Street"]).apply(setPostcode)
print("Number of missing postcodes: " + str(properties_df["Postcode"].isnull().sum()))
```

    Number of missing postcodes: 218
    

Down to only 218 missing postcodes from 52,182. Not bad!

<h2><a name="Filling-missing-towns" href="#Filling-missing-towns">Filling missing towns</a></h2>
<a href="#title">Back to top</a>

There are 16102 entris missing their town value. Similarly to postcodes this information is duplicated in the address


```python
pd.set_option('display.max_colwidth', -1)
print(properties_df[rows_to_fix]["Property Address"].sample(n=15))
#reset the max_colwidth to default
pd.set_option('display.max_colwidth', 50)
```

    701988    42 Creenagh Bridge Road, Creenagh, Dungannon BT71 6EY,            
    452375    35 Harding Street, Londonderry BT48 6SF,                          
    754738    33 Carneyhough Court, Carneyhough, Newry BT34 2TW,                
    759493    7 Heslips Court, Lisdrumliska, Newry BT35 8GR,                    
    674650    41 School Road, Eskragh, Beltany, Newtownsaville, Omagh BT78 2SD, 
    61476     Lock Up Garage 5, N12 Church Hill, Ballygowan, Down,              
    532798    16 Knocknamoe Bungalows, Mullaghmore, Omagh BT79 7LA,             
    407982    20 Neptune Crescent, Walworth, Ballykelly, Limavady BT49 9PY,     
    694962    64 Ballyronan Road, Killyfaddy, Magherafelt BT45 6EW,             
    78276     20 Cedarfield, Corporation, Bangor BT20 4WH,                      
    42243     64 Rathmore Road, Dunadry, Rathbeg, Antrim BT41 2HX,              
    441912    17 Woodend Meadow, Ballymagorry, Strabane BT82 0FB,               
    122391    74 Rathmore Road, Carnalea, Bangor, Down, BT19 1NU,               
    690347    24 Torrent Valley, Gortnaskea, Coalisland, Dungannon BT71 4FE,    
    122179    15 Lynne Road, Carnalea, Bangor BT19 1NT,                         
    Name: Property Address, dtype: object
    

It looks like we are in luck again and the property town is before the postcode, separated on either side by a space. We can't be sure this is always the case though so we should take some precautions.

First select the rows missing the town and that has an address. Filter the property addresses for these values, split each on the space character and take the 4th element from the end in the subsequent list.


```python
rows_to_fix = properties_df["Town"].isnull() & properties_df["Property Address"].notnull()
fixed_towns = properties_df[rows_to_fix]["Property Address"].apply(lambda x: str(x).split(" ")[-4])
fixed_towns.sample(n=7)
```




    551489        Craigavon
    649664    Carrickfergus
    407828      Londonderry
    23771        Ballyclare
    600740        Craigavon
    589701        Craigavon
    24189        Ballyclare
    Name: Property Address, dtype: object



Next we want to check we actually got the right towns so filter the list by checking each value is already a town that appears in the data. Any that don't appear can be replaced with NaN. Finally add the fixed towns to the data!


```python
towns = properties_df["Town"].unique()
right_rows = fixed_towns.isin(towns)
fixed_towns[~right_rows] = np.nan
properties_df.loc[rows_to_fix, 'Town'] = fixed_towns
print("Number of missing towns: " + str(properties_df["Town"].isnull().sum()))
```

    Number of missing towns: 167
    

Great! Down to 167 from 16102.

<h1><a name="Save-to-File" href="#Save-to-File">Save to File</a></h1>
<a href="#title">Back to top</a>

Save this to file so we don't have to run all this everytime we restart the kernel.


```python
properties_df.to_csv("DomesticPropertyValue 16 row cleaned.csv")
```
