# INDIA AIRLINE DATA ANALYSIS PROJECT

### Project Overview
This project explores the demand, price and availability of six different airlines in india and gives insights to factors affecting their operation and the possible solution.

### CONTENTS
- [INTRODUCTION](#introduction)
- [DATA CLEANING](#data-cleaning)
- [DATA PROCESSING](#data-processing)
- [INSIGHTS](#insights)
- [CONCLUSION](#conclusion)

 ### INTRODUCTION
 This dataset contains information of airline companies in india, highlighting their names, the amount they charged per trip which varies from time to time, location and also duration. This dataset contains 185,435 rows and 12 columns which are arranged with no particular pattern.
 
 In this project, we are going to use SQL(structured querry language) to clean, analyse and draw valuable insights from the dataset that will foster good decision and policy making among the concerned stakeholders, which can be the government, the company heads, passengers and researchers. We are going to check the airline that offer the cheapest price, the airline that the passengers patronise mostly and why they patronise him, we are going to check for the factors that can affect the price of airlines and how those factors could be best managed inorder to make travels by flight accessible to everyone.

Lastly in this project we are going to forecast key performances of the different airlines for the next ten years and gives recommendation that could help the passengers, airline companies and goverment in India.

### DATA CLEANING
Data cleaning is the process of identifying and correcting errors, inconsistencies, and inaccuracies in data to ensure its accurate, complete, and reliable for analysis and decision making.
```sql
SELECT count(`index`)
FROM stonepappy.india_airlines; -- This helps to find how many rows are in the dataset

SELECT *
FROM stonepappy.india_airlines; -- This brings out the whole dataset

create table indian_airlines_copy
like india_airlines; -- this create a new table with the same size and shape of the raw table so as to copy it for duplicate

insert indian_airlines_copy
select *
from india_airlines; -- This copy the raw dataset into the new table to have a duplicate

select *,
row_number()over( partition by airline, flight, source_city, departure_time, stops, arrival_time, destination_city, class, duration, days_left, price)
from indian_airlines_copy; -- To partition over each rows and columns

with duplicate_cte as 
(
select *,
row_number()over( partition by airline, flight, source_city, departure_time, stops, arrival_time, destination_city, class, duration, days_left, price) as row_num
from indian_airlines_copy
)
select *
from duplicate_cte
where row_num > 1; -- To check if there is any duplicate in the dataset

select distinct airline
from indian_airlines_copy; -- Congratulations, there are no duplicate in our dataset

```
Moreso, we check the colums one after the other to check for null values and we removes nulls using the isnull function and we make trims where necessary.

### DATA PROCESSING
Data processing is the process of converting raw data into meaningful information through a series of actions to extract insights and support decision making.

In this project, we are going to answer the following questions in order to properly analyse the dataset;
- How many airline companies do we have in india?
```sql
select distinct airline
from indian_airlines_copy; -- This shows the names of different airline companies in India. Hence, they are 6 in number
```

- what is the average price each airlines offer for a trip in economy class and which airlines offer the cheapest and most expensive?
```sql
select airline, avg(price) as average_price
from indian_airlines_copy
where class = "Economy"
group by airline
order by average_price; -- AirAsia offers the cheapest economy flight (4812 rupees) and Vistara offer the highest price (7515 rupees)
```

- Which airline has the most customers and which has the least customers in economy flight?
```sql
select airline,
       count(airline) as Number_of_passengers
from indian_airlines_copy
where class = "Economy"
group by airline
order by Number_of_passengers desc; -- Vistara(10602 passengers) has the highest number of customers and the lowest is Spicejet(1994 passengers)
```

- Which airlines has the highest number of flights?
```sql
select airline, count(distinct flight)
from indian_airlines_copy
group by airline; -- Indigo has the highest number of flight(181 flights) while AirAsia has the lowest number of flight(30)
```

- What time does the passengers travels the most?
```sql
select arrival_time, count(arrival_time)
from indian_airlines_copy
group by arrival_time; -- More passengers travels in the evening, followed by morning and the least is late night
```

### INSIGHTS
After thorough querrying and analysis of the datasets, the following insights were drawn;
1. Most passengers just travel without taking into consideration what time to travel and how the time could affect the price of the flight, for instance, it is cheaper to travel with Vistara and AirAsia at night than any other time of the day because they usuaally have less customers at night and Air_India is cheap in the evening.
```sql
-- What is the cheapest time to travel with Vistara?
select departure_time, avg(price) as average_price
from indian_airlines_copy
where airline = "Vistara"
group by departure_time
order by average_price; -- cheapest at night

-- What is the cheapest time to travel with Air_India?
select departure_time, avg(price) as average_price
from indian_airlines_copy
where airline = "Air_India"
group by departure_time
order by average_price; -- cheapest in the evening

-- What is the cheapest time to travel with Air_Asia?
select departure_time, avg(price) as average_price
from indian_airlines_copy
where airline = "AirAsia"
group by departure_time
order by average_price; -- cheapest at night
```

2. The analysis shows that India airline operations is rapidly growing and their some airlines have over hundred percent efficiency and performance ratio while some of them still need to do more in their service to also have hundred percent efficiency and more, like Indigo which has highest number of flights(181 flights) has the second lowest efficiency and performance ratio(39%).

However, for a company like Indigo, i will suggest they sell some of their airlines as it is clear that number of airlines is not proportional to price, they can use the money they realise from selling the flight to properly managed the business, do more advert, also start business class flight as it is seen that business class flight strive well in india. From the analysis, i realise that the passengers are really interested in good services than cheap services.
```sql
-- performance/ efficiency ratio
select airline, count(distinct flight), count(airline), count(airline)/count(distinct flight)
from indian_airlines_copy
group by airline; -- vistara has highest performance ratio, next to Air Asia and the lowest performing airline is Spicejet
```

### CONCLUSION
In conclusion, this project uncovered key insights into the airline operations in india. Despite challenges faced such as data quality issues, the findings provide valuable recommendations for decision makers. Future work can build upon these results to further improve their research and enquiries into india airline operations.
