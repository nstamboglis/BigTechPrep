**Big tech data science questions prep**

As probably ever data scientist on earth has asked him/herself at least once in their lifetime I've reached the point where I've thought: 

    do I have what it takes to crack a techical interview at a Big Tech?

The kind of reasoning behind this was the result of a series of loops: yeap, I've managed to deliver that big dashboard with a complex ER schema that I've helped developing, ok, I know how to navigate most of my org's databases in SQL and get out also some fairly complex (fro me) queries, I've designed and shipped a parametrized reports that runs a standardised model for the estimation of a phenomena, but... do I have what it takes to consider myself a big tech level DS?

There is no real answer other than *probably not* or *it depends*. However, I'm too commited on empirics to give in to this impostor syndrome. 

Hence my rephrased answer: **practice**.

I've found myself a series of interview questions from a decent website, <a href="https://igotanoffer.com/blogs/tech/amazon-data-science-interview">this one</a> or <a href="https://igotanoffer.com/blogs/tech/data-science-interview-prep">this one</a>, and here we go! Let's crack it!

A note for myself as I'm not sure anybody will ever read this: the approach is not to have myself running against time to see whether I would pass an hypotetical interview in a short amount of time. 

My goal is **longterm**: honestly challenge myself to get out of my comfort zone, make an honest assessment of where I am and start improving in a structured way from there. 

This is the most honest self-thought I can think of for myself. Ok, enough chit-chat. Here we go!

**SQL Questions**

* Write a SQL code to explain month to month user retention rate.
    * Assumption: I have a table users_month with the following columns: a) plat_payment_date, b) user_id. plat_payment_date is valued monthly with the same day as the date of subscription. 
    * Solution: 
    * ```
    SELECT 
        tab1.user_id
        * tab1.user_start_date
        * tab1.user_date   
    * from(
        * SELECT 
        *   MIN(EXTRACT(MONTH FROM users_month.plat_payment_date)) AS user_start_date
        *   um.user_id
        * FROM users_month um
        * GROUP BY users_month.user_id
    *) tab1
    * inner_join(
        * SELECT 
        *   EXTRACT(MONTH FROM users_month.plat_payment_date) AS user_date
        *   um2.user_id
        * FROM users_month um2
        * GROUP BY users_month.user_id
    * ) tab2
    * by tab1.user_id = tab2.user_id;
    * ```
* Describe different JOINs in SQL;
* What is the most advanced query you’ve ever written?
* Given a table with three columns, (id, category, value) and each id has 3 or less categories (price, size, color); how can you find those id's for which the value of two or more categories matches one another? 
* I have table 1, with 1 million records, with ID, AGE (column names) , Table 2 with 100 records with ID and Salary, and the following script. How many records would be returned?

    SELECT A.ID,A.AGE,B.SALARY

    FROM TABLE 1 A

    LEFT JOIN

    TABLE 2 B

    ON A.ID = B.ID

    WHERE B.SALARY > 50000

* Given a csv file with ID and Quantity columns, 50million records, and the size of the data is 2gig, write a program to aggregate the QUANTITY column.
* How would you find the top 5 highest-selling items from a list of order histories?
* Given three columns of data, how would you compare the first three to the last three?
* How do you calculate the median for a given column of numbers in a data set?
* Provided a table with user_id and the dates they visited the platform, find the top 100 users with the longest continuous streak of visiting the platform as of yesterday.
* Provided a table with page_id, event timestamp, and an on/off status flag, find the number of pages that are currently on.
* What's the difference between a left join, a union, and a right join?

**Data structure and algorithms questions**

* Write a python code for recognizing if entries to a list have the same characters or not. Then what is the computational complexity of it?
* You have an array of integers and you want to find a certain element; what effective algorithm would you use and what is the efficiency of it?
* For a long sorted list and a short (4 element) sorted list, what algorithm would you use to search the long list for the 4 elements?
* Given an unfair coin with the probability of heads not equal to .5, what algorithm could you use to create a list of random 1s and 0s?
* Given a bar plot, imagine you are pouring water from the top. How do you qualify how much water can be kept in the bar chart? (solution)
* Write a Python function that displays the first n Fibonacci numbers. (solution)
* Suppose you have a list of strings, each of which is an English sentence. # Output a dictionary out_dict that maps a key n to the list of words that occur in n different sentences. # E.g. # Input: str_list = [ “The cat ate the fish”, “The cat saw the roses”, “The roses are red” ]
* If given an integer n and an array of numbers, give out the histogram divided into n bins.
* How do you invert a binary tree? (*Frequent question. Solution)
* Given a bar plot, imagine you are pouring water from the top. How do you qualify how much water can be kept in the bar chart? (Solution)
* Write a Python function that displays the first n Fibonacci numbers. (Solution)
* Given a list, search for consecutive numbers (n) whose sum is equal to a specific number (x).
* Write a function to generate N sample from a normal distribution and plot the histogram. (Solution)
* Write code to generate iid draws from distribution X when we only have access to a random number generator.
* Given a list of characters, a list of prior probabilities for each character, and a matrix of probabilities for each character combination, return the optimal sequence for the highest probability.

**Modeling**

* How would you improve a classification model that suffers from low precision?
* We have two models, one with 85% accuracy, one 82%. Which one do you pick? (solution)
* When you have time series data by month, and it has large data records, how will you find significant differences between this month and previous month?
* How do you inspect missing data and when are they important?
Assume you have a file containing data in the form of data = [{"one":a1, "two":b1,...},{"one":a2, "two":b2,...},{"one":a3, "two":b3,...},...] How could you split this data into 30% test and 70% train data?
* We have two models, one with 85% accuracy, one 82%. Which one do you pick? (Solution)
* How would you improve a classification model that suffers from low precision?
* How would you create a model to find bad sellers on marketplace?
* Assume you have a file containing data in the form of data = [{"one":a1, "two":b1,...},{"one":a2, "two":b2,...},{"one":a3, "two":b3,...},...] How could you split this data into 30% test and 70% train data?

**Machine learning questions**

* How do you interpret logistic regression?
* How does dropout work?
* What is L1 vs L2 regularization?
* What is the difference between bagging and boosting?
* Explain in detail how a 1D CNN works.
* Describe a case where you have solved an ambiguous business problem using machine learning.
* Having a categorical variable with thousands of distinct values, how would you encode it?
* How do you manage an unbalanced data set?
* What is lstm? Why use lstm? How was lstm used in your experience?
* What did you use to remove multicollinearity? Explain what values of VIF you used.
* Explain different time series analysis models. What are some time series models other than Arima?
* How does a neural network with one layer and one input and output compare to a logistic regression?

**Statistics Questions**

* What is p-value?
* What is the maximum likelihood of getting k heads when you tossed a coin n times? Write down the mathematics behind it.
* There are 4 red balls and 2 blue balls, what's the probability of them not being the same in the 2 picks?
* How would you explain hypothesis testing for a newbie?
* What is cross-validation?
* How do you interpret OLS regression results?
* Explain confidence intervals
* Name the five assumptions of linear regression
* Estimate the disease probability in one city given the probability is very low nationwide. Randomly asked 1000 people in this city, with all negative responses (NO disease). What is the probability of disease in this city?
* What is the difference between linear regression and a t-test?
