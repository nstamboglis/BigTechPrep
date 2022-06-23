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
    * Assumption: I have a table users_payments with the following columns: a) plat_payment_date, b) user_id. plat_payment_date is valued monthly on the same day as the date of subscription. I interpreted month-to-month retention to be computed as follows: (active users (t) - new uesers (t)) / registered users (t-1) * 100. We therefore have the following:
    * Solution: 
    ```
    SELECT
        (tab3.active_users - tab3.new_users) / tab4.prev_users * 100 as retention_rate
        check_date
    FROM(
        SELECT
            COUNT(DISTINCT  users_payments.user_id) as active_users
            pay_date as check_date
        FROM users_payments tab1
        GROUP BY check_date
        LEFT JOIN(
            SELECT
                COUNT(DISTINCT user_id) as new_users
                reg_date
                FROM(
                    SELECT 
                        MIN(users_payments.pay_date) as reg_date
                        users_payments.user_id
                    FROM users_payments
                    GROUP BY reg_date)
        ) tab2
        on tab1.check_date = tab2.reg_date) tab3
     LEFT JOIN(
                SELECT
                COUNT(DISTINCT user_id) as prev_users
                reg_date
                FROM(
                    SELECT 
                        MIN(users_payments.pay_date) as reg_date
                        users_payments.user_id
                    FROM users_payments
                    GROUP BY reg_date)
     ) tab4
     ON tab3.check_date = (tab4.reg_date-1)
     GROUP BY tab3.check_date
     ORDER BY tab3.check_date asc;
    ```
* Describe different JOINs in SQL;
    * Assuming we have two tables: A (to the left) and B (to the right). Assume we Have A = [("ID", "Country"), (1, Austria), (2, UK), (3, Italy)] and B = [c("Country", "Capital"), (Austria, Vienna), (Italy, Rome), (Thailand, Bangkok)].Then the different join types are:  
    * Left join -> Returns all the rows of table A and all the matching records of table B that satisfy the matching condition. 
    Example:
    ``` Select * from A left join(B) on A.country = B.Country ```
    returns C = [("ID", "Country", "Capital"), (1, Austria, Vienna), (2, UK, NA), (3, Italy, Rome)]
    * Right join -> Returns all the rows of table B and all the matching records of Table A that satisfy the matching condition.
        Example:
    ``` Select * from A right join(B) on A.country = B.Country ```
    returns C = [("ID", "Country", "Capital"), (1, Austria, Vienna), (3, Italy, Rome), (NA, Thailand, Bangkok)]
    * Inner join ->  Returns all records which are in common across the two tables.
        Example:
    ``` Select * from A inner join(B) on A.country = B.Country ```
    returns C = [("ID", "Country", "Capital"), (1, Austria, Vienna), (3, Italy, Rome)]
    * Outer join -> returns all records that are not shared across the two tables
        Example:
    ``` Select * from A outer join(B) on A.country = B.Country ```
    returns C = [("ID", "Country", "Capital"), (2, UK, NA), (NA, Thailand, Bangkok)]
* What is the most advanced query (MAQ) you’ve ever written?
    * The MAQ I've ever written involved searching for the share of companies in a database which includes specific key words in their registered name. Assume we had table A = [c("Year", "ID", "Feature1")] and B = ["ID", "Reg_Name"]. The query went something along these lines:
    ```
    select
        tab3.year
        count(tab3.ID) as n_companies
        tab3.my_flag
    from(
        select 
            tab1.Year
            tab1.ID
            colesce(company_flag, 0) as my_flag
        from(
            select *
            from A
            where A.Year >= 2005
        ) tab1
        left join (
            select *
            from A
            where A.Year >= 2005
            left join (
                select B.ID
                case 
                    when B.Reg_Name (like ('%word1%') OR like ('%word2%') OR like ('%word3%')) then 1
                    else 0
                end as company_flag
            )
            on A.ID = B.ID
        ) tab2
        on tab1.Year = tab2.Year AND tab1.ID = tab2.ID
    ) tab3 
    group by
        tab3.year, tabe.my_flag
    order by
        tab3.year asc, tab3.myflag asc
    ```

* Given a table with three columns, (id, category, value) and each id has 3 or less categories (price, size, color); how can you find those id's for which the value of two or more categories matches one another? 
    * Proposed solution:
    ```
   SELECT
    combin,
    COUNT(DISTINCT combin) as n_occur
   FROM(
    SELECT 
        CONCAT(id,'_', id2) as combin
    FROM(
        SELECT
            tab.id,
            tab.cat,
            tab.val,
            tab2.id2
        FROM
            tab
        LEFT JOIN(
            SELECT
                tab.id as id2,
                tab.cat,
                tab.val
            FROM
                tab) tab2
         ON tab.cat = tab2.cat AND tab.val = tab2.val
     ) tab3
     WHERE tab3.id != tab3.id2)
    GROUP BY combin
    WHERE n_occur >= 2;

    ```

* I have table 1, with 1 million records, with ID, AGE (column names) , Table 2 with 100 records with ID and Salary, and the following script. How many records would be returned?

    SELECT A.ID,A.AGE,B.SALARY

    FROM TABLE 1 A

    LEFT JOIN

    TABLE 2 B

    ON A.ID = B.ID

    WHERE B.SALARY > 50000

    * The WHERE condition specified at the end is applied AFTER the left join is made, hence the results will depend on the join results across the two tables. Assuming that we have no duplicated records in table 1 and in table 2, then the answer is less than 100 records. More specifically, we will find the number of records in table 2 who are also in table 1 and that satisfy the salary condition. 

* Given a csv file with ID and Quantity columns, 50million records, and the size of the data is 2gig, write a program to aggregate the QUANTITY column.

    * SOLUTION: The below is a postgresql solution assuming that we have all super user access and that the server we're using can access the csv file directly.
        * FIRST: I specify the table where I'm storing all the data
            ```
            CREATE TABLE import_info (
                id_num SERIAL,
                ID CHAR(16),
                Quantity NUM,
                PRIMARY KEY (id_num)
            )
            ```
        * SECOND: I input the data (again, assuming that the server can read the file) using the COPY function
            ```
            COPY import_info(ID, Quantity)
            FROM 'file_path\my_file.csv'
            DELIMITER = ','
            CSV HEADER;
            ```
* How would you find the top 5 highest-selling items from a list of order histories?
    * SOLUTION: Assume I have a table orders with these columns: "order_id", "order_date", "customer_id", "item_id". I then create the query below
        ```
        SELECT
            count(order.order_id) as n_order,
            order.item_id
        GROUP BY order.item_id
        ORDER BY n_order DESC
        LIMIT 5;
        ```
        Assuming, instead, that we want to extract the top 5 by month, we have
        ```
        SELECT
            count(order.order_id) as n_order,
            order.item_id,
            to_char(order_date, 'YYYY-MM') as my_date,
            rank() as my_ranking
        GROUP BY my_date, order.item_id
        ORDER BY my_date ASC, n_order DESC
        WHERE my_ranking <= 5;
        ```        
        
* Given three columns of data, how would you compare the first three to the last three?
    * The question is unclear: are we comparing the first three columns of a dataset to the last three column of the dataset, or are we comparing the first three rows of those columns to the last three rows of the same columns? 
    *  First option: comparing columns
        ```
        SELECT
         CASE
            WHEN table1.col1 = table1.col4 then 'T'
            ELSE 'F'
         END AS check1,
         CASE
            WHEN table1.col2 = table1.col5 then 'T'
            ELSE 'F'
         END AS check2,
         CASE
            WHEN table1.col3 = table1.col6 then 'T'
            ELSE 'F'
         END AS check3
       FROM table1;
        ```
    * Second option: comparing rows
        ```
        SELECT
        data_col-last_data_col as my_comparison
        FROM (
            SELECT
                table1.data_col,
                (select table.data_col from table1 order by table.data_col desc) as last_data_col
            FROM table1
            LIMIT 3
        ) tab1;
        ```
* How do you calculate the median for a given column of numbers in a data set?
    * Solution: 
        ```
        SELECT PERCENTILE_CONT(0.5) WITHIN GROUP(ORDER BY tab.colum) FROM tab.colum;
        ```
* Provided a table with user_id and the dates they visited the platform, find the top 100 users with the longest continuous streak of visiting the platform as of yesterday.
    * Solution: Assume we have a table of this type table('user_id', 'visiting-date'), then the query becomes
        ```
        select
	        tab1.*
        from(
                select distinct on (customer_id) customer_id, min(order_time), max(order_time), max(seqnum) as thread_length
                from (select co.*,
                             row_number() over (partition by customer_id order by order_time) as seqnum
                      from pizza_runner.customer_orders co
                      where co.order_time <= '2020-01-07'
                     ) co
                group by customer_id, order_time - seqnum * interval '1 day'
                order by customer_id, count(*) desc
            ) tab1
        order by tab1.thread_length desc limit 100;
        ```

* Provided a table with page_id, event timestamp, and an on/off status flag, find the number of pages that are currently on.
    * SOLUTION: I assume we have the following table as a starting point: my_table (page_id, timestamp, status). The query solution thus becomes:
        ```
        SELECT
            tab1.page_id
        FROM(
            SELECT
             my_table.page_id,
             max(my_table.timestamp),
             my_table.status
            FROM my_table
            GROUP BY my_table.page_id
        ) tab1
        where tab1.status = 'on'
        ```
* What's the difference between a left join, a union, and a right join?
    * Left join -> returns all records of table A and the matching records of table B;
    * Right join -> returns all records of table B and the matching records of table A;
    * Union -> returns all records of select statement A with all the records of select statement B. 

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
    * In the case of a classification model, we know from the confusion matrix, that precision is defined as: TP / (TP + FP). Suffering from low precision therefore means that we have to increase the true positive rate. The problem can then be given by either TP or FP. Potential solutions:
        * 1. Check if the data is unbalanced and in case apply sampling techniques (such as under or over sampling);
        * 2. Find more informative featurs;
        * 3. Try a more complex model.
* We have two models, one with 85% accuracy, one 82%. Which one do you pick? (solution)
    * It depends on the objective of the analysis. In a classification model, accuracy is defined as: (TP + TN) / (TP + TN + FP + FN). Perhaps, however, we are facing a specific problem where identifying just one class is very important, such as spam-detection. In that case I'd consider looking for other metrics such as Precision = TP / (TP + FP) or Recall = TP / (TP + FN). If those two metrics are also in the same order as accuracy, I'd consider model 1 to be more informative. I would also take into consideration two additional factors: a) ease of model maintenance, b) prediction time.
* When you have time series data by month, and it has large data records, how will you find significant differences between this month and previous month?
	*  I would consider two options for this, both of which work on first differences in the time series data:
	*  1. Compare the last first difference to the previous ones computing a t-test, defined as: t = (d-d_hat) / ste(d), where d is the first difference I'm checking, d_hat is the average of previous differences, ste(d) is the empirical standard error of the first differences defined as: sqrt(sum _i (d-d_hat)^2 / (N-1)). The t-test can also be defined in its adjusted format as t = (d-d_hat) / (ste(d) / sqrt(N)), where N is total number of observations. I would then test the following assumption: H_0 = "d is equal to previous registered differences". As the time series is long, I would then compare the results to a standard normal distribution.
	*  2. Calculate the Normalized version of the first differences (z-score), defined as z = d-d_hat / se(d). If the last difference is higher than 1.96 in absolute value we have a statistical difference.
* How do you inspect missing data and when are they important?
	*  When analysing a new dataset, I always check the percentage of missing data of its columns; 
	*  Columns with a high % of missing are generally not used for the analysis (the % depends on the problem, however a rule of thumb is to avoid having more than 5% missing data);
	*  One analysis on missing data is to check whether they are missing at random or non-at-random (e.g. more missing data in specific classes / features combinations or cells). Having data missing non-at-random might be informative of the data generating process;
	*  Missing data are important when selecting the algorithm for the analysis, as some of them might not work well with missing data;
	*  Missing data might also be relevant when we need to do point-wise prediction (such as the rating score of a specific customer), in such cases it is best to have an approach for handling missing data using an imputation technique);
	*  Imputation techniques (which should be always used carefully) might include: a) getting the average of the distribution, b) getting a custom value such as 0 (problem-dependent), c) use a more complex technique such as a decision tree applied on the other dataset features;
	*  One alternative technique is also to encode missing data as an additional feature of the model.
* Assume you have a file containing data in the form of data = [{"one":a1, "two":b1,...},{"one":a2, "two":b2,...},{"one":a3, "two":b3,...},...] How could you split this data into 30% test and 70% train data?
	* I assume that the variables called "one", "two" etc refer to columns of the dataset. I assume that values a1, a2, b1, b2 etc. are possible values of such variables (scalar / numeric varibles, categorical variables ecc);
	* I would propose two alternative splitting methods:
		* at random -> we randomly select 30% of the records of our dataset, such records go to the test set, the remaining go to the train set. The selection occurs at random without re-insertion;
		* with cross-validation -> in this case we different "sets" of our datasource. Such "sets" are alternatively included in the training or the test set. Cross-validation is useful to assess whether the results of our model are robust to different sampling approaches.	  
* How would you create a model to find bad sellers on a marketplace?
	* It depends on what we define a "bad seller" to be. For instance, we could create a feature indicating whether a seller falls into to the last percentile of sellers by total sales for three consecutive periods (years, quarters etc). We could then consider seller's features as explicative variables, such as country, sectorial classification etc. as features for our predictive model. We could then use alternative machine learning models to test whether the seller falls into this category (e.g. logit model, decision treee etc..). The steps to implement this model would be:
		* 1. Create the classification feature,
		* 2. Explore its relation with alternative features;
		* 3. Run feature selection (correlation analysis, PCA etc);
		* 4. Split data into train, test and validation sets;
		* 5. Run alternative specifications of the model;
		* 6. Selecting the model that better satisfies our target measure: e.g. accuracy or precision or recall. 

**Machine learning questions**

* How do you interpret logistic regression?
	*  Logistic regression provides an estimate of the probability of a variable being in a certain class (abinary variable). The model is estimated using a logistic function of the form p(x) = 1/ (1+e^(wx+b)), wx+b are the regression parameters. Individual parameters can therefore be interpreted as the expected change in log-odds given a unit variation in the parameters. In terms of odds-ratio we can say: increasing the predictor by 1 unit increases the probability of the positive outcome by e^w.
* How does dropout work?
	* DK 
* What is L1 vs L2 regularization?
	* L1 (Lasso) and L2 (Ridge) are regularization methods which are used in regression models to penalize the introduction of a huge number of regressors. Including several variables as features in a regression model might have a number of draw-backs: a) they might be significant only by chance, b) they might lead to collinearity, c) they might lead to bias. An additional, practical, limitation is that having many variables might make the model more difficult to handle in terms of data flows. Regularization is therefore a technique to ensure that the model limits the number of regressors used. Regularization works by introducing a penalty for the introduction of additional regressors. L1 regularization (Lasso) introduces the sum of the absolute value of the regressors as a cost function in the regression objective function (sum of squared residuals in OLS). The L2 regularization (ridge) introduces the squared of the regressors as a cost function in the objective function (same as above). 
* What is the difference between bagging and boosting?
	* DK 
* Explain in detail how a 1D CNN works.
	* DK 
* Describe a case where you have solved an ambiguous business problem using machine learning.
	* Can't provide much details here. Let's just say that I had to make comparison across business entities based on their "similarity". No prior guidance on how this "similarity" had to be defined. As my team was in a "Proof-of-Concept" setting, I've explored different dimensions of the problem: financial, geographical and textual (features of the companies). I've then applied a different model for each of these "dimensions" of the problem: KNN, euclidean distance, and cosine similarity. 
* Having a categorical variable with thousands of distinct values, how would you encode it?
	* Having such variable could be tricky to use in a model. Its effectiveness might dependent on the characteristics of the feature, the business problem at hand and on the type of model we decide to use (regression vs decision tree for example). One way to group such variables may include:
		* Group encoding -> if for example the categorical variable has a sectorial or geographical classification, we could try to group it at a higher classification level (e.g. from municipality to province or region);
		* Frequency encoding -> encode only the most frequent categories and include the least frequent ones into an "other" category (although this approach might depend on the specifics of the business problem and on the data characteristics).
* How do you manage an unbalanced data set?
	* Having an unbalanced dataset might lead to biased estimation results. Two ways to handle an unbalanced dataset are:
		* Undersampling -> we reduce the number of samples in the most frequent category;
		* Over-sampling -> we repeat the records of the least represented category.
	* While the two approaches above can be useful in providing more robust results, they might also lead to overfitting. It is therefore always better to run the approaches above together with cross-validation.  
* What is lstm? Why use lstm? How was lstm used in your experience?
	* DK 
* What did you use to remove multicollinearity? Explain what values of VIF you used.
	* Multicollinearity might lead to biased regression results. This is because when regressors are correlated with each other, then the variance of the estimated coefficient is higher, leading to poorer t-tests for the significance of the regression coefficients. the best way to address collinearity is to remove the collinear variables, if possibles. In alternative, the analyst could check for an increased number of observations in the model.  
	* DK VIF.
* Explain different time series analysis models. What are some time series models other than Arima?
	* I know neural networks can be used, but DK why.
* How does a neural network with one layer and one input and output compare to a logistic regression?
	* DK 

**Statistics Questions**

* What is p-value?
	* In a t-test, p-value is the probabiity of observing a value of the estimated parameter higher in absolute terms than the one observed empirically, given the theoretical (or empirical) distribution of the parameters. By convention, a p-value lower than 5% indicates that the null hypothesis of the test we are running cannot be accepted. 
* What is the maximum likelihood of getting k heads when you tossed a coin n times? Write down the mathematics behind it.
	*  As we are working with a Bernoulli distribution (toss of a coin), our probability mass function is defined as $$f(x) = p^x*(1-p)^{(1-x)}$$.
	*  For the likelihood of observing a specific draw from the coin in a series of tosses, we have to consider the join probability mass fucntion, defined as the product of individual pmf, namely $$L(p, x_1, x_2, ..., x_n) = \prod _i ^{N} p^x_i*(1-p)^{(1-x_i)}$$.
	*  Applying the exponent properties to the expression above we obtain $$L(p, x_1, x_2, ..., x_n) = p^{\sum _i x_i} (1-p) \sum _i (1-x_i)$$
	*  The above function can be maximised by means of a monotone transformation, such as taking its log, as follows $$log L(p, x_1, x_2, ..., x_n) = log(p^{\sum _i x_i} (1-p) \sum _i (1-x_i)) = log(p)*\sum x_i + log(1-p) * \sum(1-x_i)$$.
	*  We can maximise the likelihood expression defined above by taking its first derivative and setting it to zero, such as 
* There are 4 red balls and 2 blue balls, what's the probability of them not being the same in the 2 picks?
* How would you explain hypothesis testing for a newbie?
* What is cross-validation?
* How do you interpret OLS regression results?
* Explain confidence intervals
* Name the five assumptions of linear regression
* Estimate the disease probability in one city given the probability is very low nationwide. Randomly asked 1000 people in this city, with all negative responses (NO disease). What is the probability of disease in this city?
* What is the difference between linear regression and a t-test?
