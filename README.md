# Python_workshop_condordia_june_2016
Exploring metagenomics data with pandas
```python
import pandas as pd
from pandas import *
#We could see it easier with a graph
# first we need other libraries to make plots possible:
import matplotlib #the main library
import matplotlib.pyplot as plt  #a shortcut that we define as plt

#So ipython notebook can display it
%matplotlib inline 
#just a design line so the graphs look like R's ggplot
#matplotlib.style.use('ggplot') 

pd.__version__

#These 2 files are text files, but they're a cut and paste from Excel spreadsheets
countfile="data/16S_Count_Table.txt"
conditions="data/16S_Conditions.txt"
counts = pd.read_csv(countfile, sep="\t")
conds = pd.read_csv(conditions, sep="\t")
```

```python
#Look at content of the tables (5 first lines)
counts.head()
```

```python
conds.head()
```

```python
#Let's rename counts Sample column to Samples
counts = counts.rename(columns = {'Sample':'Samples'})
counts.head(n=2)
```

```python
#check how pandas is seeing the tables
type(counts)
```

```python
#Are there any other objects?
mystring = "Oh the sky is so blue today"
type(mystring)
```

```python
mynumbers = "0,1"
type(mynumbers)
```

```python
mynumbers = [0,1]
type(mynumbers)
```

```python
mynumbers = (0,1)
type(mynumbers)
```

```python
#check how pandas is treating both tables
type(counts)
type(conds)
```

```python
#Only the last one appears. Use print to see what you want to see
print type(counts)
type(conds)
```

```python
#Let's have a more user friendly display
print "The count file is :", type(counts)
```

```python
#Hum, we just want the DataFrame part.
print "The count file is :", str(type(counts)).split(".")[-1] #-1 means the last object from the list
```

```python
#We don't want the last part
print "The count file is :", str(type(counts)).split(".")[-1].split('\'')[0]  #\ is an escape character
```

```python
#Cool! Let's do it for both of them
print "The count file is :", str(type(counts)).split(".")[-1].split('\'')[0]
print "The condition file is :", str(type(conds)).split(".")[-1].split('\'')[0]
```

```python
#Let's check the shape of the count table:
counts.shape
```

```python
#Fancier
print "Count table has:"
print counts.shape[0],"rows"
print counts.shape[1],"columns"
```

```python
#Display the column names
counts.columns.values

```

```python
#put them in a list
list(counts.columns.values)
```

```python
#how about row names? Our table if a table made of counts, but pandas reads it as a collection of columns
counts[[0]].head()

```

```python
#another way of calling the first column:
counts['Samples'].head()
```

```python
#How about looking at a particular cell: 3rd row, Samples column
counts.loc[2, 'Samples']
```

```python
#More rows?
counts.loc[2:5, 'Samples']
```

```python
#More columns?
counts.loc[2:5, 'Samples':'Staphylococcus_aureus']
```

```python
#More selective?
counts.loc[[0,2,4], ['Samples','Pseudoalteromonas']]
```

```python
#Even more!
counts.loc[counts['Pseudoalteromonas'] > 10000 , ['Samples','Pseudoalteromonas']]
```

```python
#We don't have to, but sometimes it is easier to specify that the first column are row names, i.e. index
indexed_counts = counts.set_index(['Samples'])
indexed_counts.head(n=3)
```

```python
#Pandas choose numbers as row names and calls it index. Each index number is unique, so each row is unique.
#You can't have duplicates as index
counts.index
```

```python
###########################################################################################################################
###########################################################################################################################
###########################################################################################################################
```

```python
#Let's look at the other table:
conds.head()

```

```python
#How many patient in total? Several methods:
len(conds)  #size of the dataframe

```

```python
#each row has 2 patients so we just have to divide by 2:
print "The number of patients is:", len(conds)/2
```

```python
#This method is a leap of faith: Are we sure there is a patient every 2 rows?
#let's checck the end of the dataframe:
conds.tail()
```

```python
#We still see 2 patients per row, but the table seems to suggest we have 33 patients.
#This method was not safe. Let's try another one:
#Display the content of the column Patient
conds["Patient"]
```

```python
#we have 48 rows and a patient each 2 lines
#this method is tedious. Imagine we have 100s of lines
#I'll repeat this one but I'll remove the duplicates
conds.Patient.drop_duplicates() #or conds["Patient"].drop_duplicates()

```

```python
#We see it better, but we have to count the number of lines, which again is tedious.
#Now let's count the column length once we removed the duplicates
print "The number of patients is:", len(conds["Patient"].drop_duplicates())
```

```python

```

```python
#Let's take a look at the conditions again
conds.head()
```

```python
#How many categories do we have in Cavities column:
conds.Cavities.drop_duplicates()
```

```python
#Only 2: cavities and No_cavities
#How many patients have cavities?
conds["Cavities"] == "cavities" #OR conds.Cavities == "cavities"
```

```python
#it works, but not user friendly again.
len(conds.Cavities == "cavities")
```

```python
#that one doesn't work and is quite misleading. 
# == sign is calling for a True False answer
#Now we want to count how  many lines have a "cavities" entry
#Let's first extract a new dataframe with only the cavities entries
conds[conds["Cavities"] == "cavities"]
```

```python
#Now we don't want to count 2 times a patient
#Note: the index is still the same as in the main dataframe
#We'll repeat the same command, but we'll remove duplicates
conds[conds["Cavities"] == "cavities"].drop_duplicates('Patient')
```

```python
#We just have to add commands separated by "." to make successive commands
len(conds[conds["Cavities"] == "cavities"].drop_duplicates('Patient'))
```

```python
#We have 17 patients with cavities and ... 7 without? (24 patients in total)
len(conds[conds["Cavities"] == "No_cavities"].drop_duplicates('Patient'))
```

```python
#A last but better and more powerful method: the groupby() function 
len(conds.groupby("Cavities"))
```

```python
#That means we have 2 categories. Indeed: cavities and No_cavities
#grouby splits the dataframe and create a groupby object
grouped = conds.groupby("Cavities")
type(grouped)
```

```python
#We can look into a groupby object with first and last
grouped.first()
```

```python
grouped.last()
```

```python
#The sum() function will add all values in columns for each group name
grouped.sum()
```

```python
#See what the groups are made of
grouped.groups
```

```python
#how many elements compose the groups
grouped.count()
```

```python
#Let's check how many patients are in the groups
grouped["Patient"].nunique()
```

```python
#Now let's check the data for patient08
conds.loc[conds.Patient == "P08"]
```

```python
#Now let's check the data for patient08 and 12
conds.loc[(conds.Patient == "P08") | (conds.Patient == "P12")]
```

```python
# "|" is the OR condition. What happens if I choose the & condition?
conds.loc[(conds.Patient == "P08") & (conds.Patient == "P12")]
```

```python
# Nothing. Indeed, patient8 and patient12 are not the same patient
#If I want to see the throat record for patient08
conds.loc[(conds['Patient'] == "P08") & (conds['SampleType'] == "Throat") ]
```

```python

```

```python

```

```python
#PAUSE

```

```python

```

```python
#Let's look at first 2 rows of the counts table, i.e. patient 01
counts.head(n=2)
```

```python
#pandas makes plot really easily. We just add .plot() to the dataframe
#(The time to display the figure depends on the computer: 5/20 seconds)
counts.plot(kind="bar", legend=False)
```

```python
#Remarks:
#1. the counts abundance varies a lot
#2. The numbers on the x-axis don't help identifying which is which

#Let's add the sample id in the x-axis
counts.plot(x="Samples", kind="bar", legend=False)
```

```python
#better but the graph is still messy. Let's make it larger
counts.plot(x="Samples", kind="bar", legend=False,figsize=(16,4))
```

```python
# One answer to remark1 could be to log+1 the abundance (+1 to remove log0)
#I have to remove the Sample column in order to be quicker. I'll put it as an index
log_counts = np.log(indexed_counts)
```

```python
log_counts.plot(kind="bar", legend=False,figsize=(16,4))
```

```python
#Let's look at patient1
log_counts.head(n=2).plot(kind="bar", legend=False,figsize=(16,4))
```

```python
#as stacked bars
log_counts.head(n=2).plot(kind="bar", legend=False,figsize=(16,4), stacked=True)
```

```python
#Let's look at the proportions: I'll divide every counts by the sum of the row ands multiply by 100
#First, let's make a duplicate of counts, that we will call prop_counts
prop_counts = indexed_counts.copy()
#let's define a new column which will be the sum of all rows
prop_counts["Total"] = prop_counts.sum(axis=1)
indexed_counts.head(n=2)
```

```python
#Before dividing by Total, we should remove any total that is zero
prop_counts = prop_counts.loc[prop_counts['Total'] > 0]
#Now let's divide every counts by the total value:
prop_counts = prop_counts.div(prop_counts["Total"], axis='index')
prop_counts.head(n=2)
```

```python
#Now will multiply by a hundred
prop_counts = prop_counts * 100
prop_counts.head(n=2)
```

```python
#And remove the column Total that we don't need anymore
prop_counts = prop_counts.drop('Total',1)
prop_counts.head(n=2)
```

```python
#Let's look at patient1
prop_counts.head(n=2).plot(kind="bar", legend=False,figsize=(16,4),stacked=True)
```

```python
#Let's look at patient12
# .loc["row_id", "column_id"] allows you to select rows and columns
# : means all (rows or columns)
patient12 = prop_counts.loc['Throat_P12':'Tongue_P12', : ]
patient12
```

```python
#Now let's represent the bacteria proportion
patient12.plot(kind="bar", legend=False,figsize=(16,4),stacked=True)
```

```python
#Not as many bacteria, right? Looks suspicious. Let's add the legend to identify the bacteria
patient12.plot(kind="bar", legend=True,figsize=(16,4),stacked=True)

```

```python
#awful. Pandas is good for exploring data, not the best when it goes to design. Let's apply a patch

```

```python
ax = patient12.plot(kind="bar", legend=False,figsize=(16,4),stacked=True)
patches, labels = ax.get_legend_handles_labels()
ax.legend(patches, labels, loc='best', prop={'size':6})
```

```python
#Well it's not really better. Let's remove all the bacteria that don't have a count
#Let's keep only the bacteria that do not add up to zero 
patient12_reduced = patient12.loc[ : , patient12.sum(axis=0) != 0]
patient12_reduced
```

```python
patient12_reduced.plot(kind="bar", legend=True,figsize=(16,4),stacked=True)
```

```python
#That does seem suspicious. Let's look at the count table for patient12
counts.loc[(counts["Samples"] == 'Throat_P12') | (counts["Samples"] == 'Tongue_P12')]
```

```python
#The counts are quite small. Let's look at the mean values
#The describe function will calculate a bunch of statistics
#Note: .T. is to transpose the dataframe so we don't look at the bacteria count statistics, but at the samples
# .astype(int) is to transform floats to integer (less messy)
sample_statistics = indexed_counts.T.describe().astype(int)
sample_statistics
```

```python
#Patient30 stats are way below the others. Let's look at patient12
sample_statistics[["Throat_P12","Tongue_P12"]]
```

```python
#We'll come back to patients 12 and 30 later
#Let's take patient 2. And look at the bar chart
patient2= prop_counts.ix[2:4,]
patient2.plot(kind='bar', legend = False)
```

```python
#We don't see the labels well enough because of the colouring.
#Let's try to see it as a pie chart. First the throat

patient2.ix["Throat_P02"].plot.pie(figsize=(9, 9))
```

```python
#Now the tongue
patient2.ix["Tongue_P02"].head()
patient2.ix["Tongue_P02"].plot.pie(figsize=(9, 9))
```

```python
#together.
#Note: I have to transpose the matrix in order to have only 2 plots

patient2.T.plot.pie(subplots=True, figsize=(10, 4), legend=False)
```

```python
#Some colors are the same. Let's write a color fix
from matplotlib.pyplot import cm 
cm_name = 'Pastel1'
color_map = cm.get_cmap(cm_name)
num_of_colors = len(patient2.columns)
mycolors = color_map([x/float(num_of_colors) for x in range(num_of_colors)])
```

```python
#Now we will add the percentages inside the pie
patient2.T.plot.pie(subplots=True, figsize=(18, 8), legend=False, colors=mycolors, autopct='%.2f', fontsize=8)
```

```python
#We could now compare the count scatter between the 2 samples
scatter_matrix(patient2.T, figsize=(10, 10), diagonal='kde', color=mycolors)
```

```python
#The biggest difference lies in counts below 10% of total abundance
patient2
```

```python

```

```python

```

```python
#We want to see more complex patterns. Let's join conditions and counts table using merge
data = pd.merge(conds, counts, on='Samples')
data.head(n=2)
```

```python
#Now we can create new tables depending on the question 
patients = data.groupby("Patient").sum()
patients.head()
```

```python
#Let's take a look at all the patients together
patients.plot(kind="bar", legend=False,figsize=(16,4), stacked=True)
```

```python
#Again, a huge difference in the counts between patients.
data[data["Patient"] == "P12"]
```

```python
#Ah! Patient12's samples are indeed problematic. They didn't pass the QC
#Let's remove all the failed QC from data
data = data[data["QC"] != "Failed"]
len(data)
```

```python
data[(data["Patient"] == "P12") | (data["Patient"] == "P30")]
```

```python
#Patients 12 and 30 have disappear.
#Let's look again at the patients count stacked bars
patients = data.groupby("Patient").sum()
patients.plot(kind="bar", legend=False,figsize=(16,4), stacked=True)
```

```python
#Much better, but we still have a big difference. let's take a look at the shifted log
log_patients = np.log(patients)
```

```python
#Satcked bars graph again
log_patients.plot(kind="bar", legend=False,figsize=(16,4), stacked=True)
```

```python
#Even better although we have to be careful
#Let's apply the log to the data
np.log(data)
```

```python
#The error comes from strings. We have to do a turn around.
#Let's select in a temporary dataframe all the numeric values
tmp = data.select_dtypes(include=[np.number])
#Add one so the log doesn't give an infinite number
tmp = tmp + 1 
tmp.head()
```

```python
data_log = data.copy() 
data_log.loc[:, tmp.columns] = np.log(tmp)
data_log.head()
```

```python

```

```python
#Now let's do a  spring tension graph to see if some pattern comes out from the data
from pandas.tools.plotting import radviz
#We need the data in the form of 
#one variable counts counts counts counts etc...
#Let's take the samples as variable and see how they 
data_log.head()
samples = data_log.drop(['Patient','Cavities','SampleType','QC'],1)
samples.head()
```

```python
radviz(samples,"Samples")
```

```python
#Hard to see a pattern, but that's not so anormal, we're looking at all the samples together.
#let's look at the location : throat vs Tongue
SampleType = data.groupby(["SampleType"]).sum()
SampleType.head()
```

```python
#This is not how we want the data to be. Let's remove SAmpleType from the index
SampleType = SampleType.reset_index(0, drop=False)
SampleType.head()
```

```python
#Ok we can try radviz
radviz(SampleType,"SampleType")
```

```python
#We do see a different pattern in the bacteria communities!
#The problem is that we could have all the samples per patient and not all added together
#We can do this by grouping by patients and sampleType together. Let's see
SampleType = data.groupby(["Patient", "SampleType"]).sum()
SampleType.head(n=2)
```

```python
#We have 2 index nested. We will remove Patient and keep SampleType
SampleType = SampleType.reset_index(0, drop=True)
SampleType.head(n=2)

```

```python
SampleType = SampleType.reset_index(0, drop=False)
SampleType.head()
```

```python
#All right, let's try radviz
radviz(SampleType,"SampleType")
```

```python
#Let's see if the cavities influence the bacterial communities in the Throat
cavities = data_log.groupby(["Patient", "SampleType", "Cavities"]).sum()
cavities.head()
```

```python
#We don't need the patient information
cavities = cavities.reset_index(0, drop=True)
cavities.head()
```

```python
#We will separate the Throat data from the Tongue so wee keep the indexes
cavities=cavities.reset_index(0, drop=False)
cavities.head()
```

```python
cavities=cavities.reset_index(0, drop=False)
cavities.head()
```

```python
#Let's create 2 dataframe and remove the SampleType columns
throat = cavities[cavities['SampleType'] == "Throat"]
throat = throat.drop(['SampleType'],1)
tongue = cavities[cavities["SampleType"] == "Tongue"]
tongue = tongue.drop(['SampleType'],1)
tongue.head(n=3)
```

```python
#Let's radviz the throat!
radviz(throat,"Cavities")
```

```python
tonguez = tongue.copy()
tonguez = tonguez.ix[:,'Cavities':'Prevotella_maculosa']
tonguez.head(n=30)
```

```python
#Now the tongue!
radviz(tonguez,"Cavities")
```

```python

```
