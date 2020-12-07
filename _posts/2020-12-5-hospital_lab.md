---
layout: post
title: Hospital Lab
---

#### Which county has the most hospital beds per person (regardless of bed type)?

New York has the most hospital beds per person.


#### Discuss how you obtained and cleaned the dataset. Make sure to explain what methods you used and why.

I obtained the hospital.csv dataset through an API containing data on hospital beds in New York counties. To access the API, I used the requests library. Pulling the data required the [base URL with the "hospital" endpoint](https://hm-cs.herokuapp.com/hospital), as well as the request parameters, the API key and the id (line number from the dataset). The first function I created, get_response() would generate the response received upon sending the request to the API, using the link with the correct endpoint and a payload containing the request parameters. The next function, check_data(), would determine whether or not an error occurred due to an incorrect endpoint, lack of key, incorrect key, lack of id parameter, or invalid id parameter. If the status code for the response was 200, then data was accessed successfully. My final function, print_data(), would print the accessed line of data in list format. My objective was to print all of the rows of data from the csv on the API, so I used a while loop to print each row. Because all of the data was pulled from the same endpoint with the same API key, I only had to vary the id parameter in each go-through of the while loop. I initially did not know the number of rows in the dataset, so I had the while loop continue to increase the id parameter by 1 until check_error() demonstrated the presence of an error, which would occur due to attempting to use an invalid id value. Since all of the available rows of data would be printed to the console in a comma-separated format, I could use the terminal to turn the output of the python file into a csv. I used a spreadsheet to manually clean the data for use. Although I did not find any missing or contradictory values in my version of the data, some counties did not have information on certain bed types. I decided to leave their values for other bed types as zero, as I thought that using means or modes could skew the data if counties meant that they had no beds of a certain type by excluding them. Another source of error could occur due to a county recording no data at all for their beds, making them seem non-existent. The bed measures were also inconsistent. The three possible bed measures were 500HAB, 1000HAB, and 2000HAB, so I decided to convert all of the data into 1000HAB, or beds per 1000 people. If the row had the measure 500HAB, I used the spreadsheet formula to multiply its bed amount by two, and if the row had the measure 2000HAB, I divided its bed amount by two. This allowed me to have a standard measure for the bed amounts in each county, which would be more convenient throughout data analysis.

#### Process:

My process for this lab involved pulling the data from the API, cleaning the acquired data in csv format, and then conducting analysis on the cleaned csv. 

###### Data:
I first had to find a way to print all of the API dataset, allowing me to convert the responses from the API into a usable csv. Based on the documentation of the hospital API, I could access one row of the dataset at once, which was the line number specified by the id request parameter. As described above, I used the requests library in a while loop to get and print each line, at which point I made a csv that I could manually clean. I initially had a single function that would both check and analyze the data, but when I realized that the result of the data check was a condition for determining whether the while loop would continue, I decided to split this function into check_data() and print_data(). These two methods both had the step of generating a response, so I created a third function, get_response(), to remove the redundancy. The resulting csv file had the attributes country, state, county in alphabetical order, bed type, bed measure, bed amount, county population, and year. Because all of the information concerned New York counties, the country and state attributes were the same for all of the data. Although there were different bed types, this information was not necessary for determining which county had the most total beds per person. The bed measures were all -per some number of people, so I did not have to take the population of a county into account. My main focuses were the county, its bed measure, which I had to make standard for consistency, and bed amount. 

###### Data Analysis:
After manually standardizing the bed measures for the csv, I read it and analyzed it to answer my primary question of which New York county had the most beds. I started with three main variables, an empty string max_bed, which would contain the name of the county with the most beds per person, a float max_no_beds, which would start at 0 and contain the greatest beds per person value, and an empty dictionary counties, which would have each of the counties as keys and their amount of beds per 1000 people as values. After using the csv reader to collect the data and skipping the two headers, I created a for loop to iterate through the rows of the data:

```python
for row in data:
    bed_amt = find_bed_amount(row, counties)
    if bed_amt > max_no_beds: 
        max_no_beds = bed_amt
        max_bed = row[2]
```
The for loop begins by calling the find_bed_amount() function to find the beds per person for a particular county.

```python
def find_bed_amount(row, counties):
    county = row[2]
    bed_per_thousand = row[5]
    if county in counties: 
        counties[county] += float(bed_per_thousand)
    else:
        counties[county] = float(bed_per_thousand)
        
    bed_amt = find_beds_per_person(counties[row[2]])
    return bed_amt

```
First, each county name (the second index of each row) becomes a new key in the counties dictionary with the amount of beds (1000HAB) (the fifth index of each row) as its value. The counties dictionary is a global variable, and it is passed into the function each time to populate it with data from each new row. Since the dataset has multiple rows for each county (one for each available bed type), if a county name already exists in the dictionary, then the new bed value is added to the existing ones for that county, preventing the new value from overwriting the past one for that county. Thus, each value of the dictionary will end up containing the sum of the bed values for that county, which is useful because the primary question asks about the total beds for a county, as opposed to the beds of a specific type. When the dictionary has been populated with a certain row's data, the if statement in the for loop checks whether the county of the current row has a greater beds per person value than the current maximum number of beds. This means that each county's greatest bed value (the sum of all its bed values) will eventually be compared to the current max. If a county's bed value is greater than the max, then that county becomes the county name with the most beds per person and its bed value becomes the new greatest number of beds. The beds per person value is computed through the find_beds_per_person() function:

```python
def find_beds_per_person(beds_per_thousand):
    return beds_per_thousand/1000
```

This function converts a bed amount in beds per 1000 people into a bed amount in beds per 1 person, as a county's bed value will initially be in 1000HAB, the measure used in the dataset. 
After all of the rows have been iterated through, the max_no_beds and max_bed variables will reflect the greatest beds per person and county with the most beds per person from the dataset.

Upon completing the data analysis, I thought of a different way I could have potentially cleaned and analyzed the data. Instead of manually converting all of the data collected from the API into a csv file, I could have cleaned and analyzed each row as it was received from the API, as the analysis process was also conducted row-by-row. I noticed that much of my data cleaning involved using the spreadsheet formula to convert bed measures, which a python function also could have done. In one go-through of the while loop, a row would be received from the API, had its bed measures converted, added to a counties dictionary, and had its beds per person value compared to the current maximum. This method would also assume that no missing values or other issues existed in the dataset, but it may be usable with a mostly clean dataset.