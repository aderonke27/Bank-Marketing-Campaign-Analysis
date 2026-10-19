# Bank-Marketing-Campaign-Analysis
![Power BI](https://img.shields.io/badge/Tool-Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)
![Domain](https://img.shields.io/badge/Domain-Marketing%20Analytics-blue)

## Table of Contents
1. [Project Overview](#project-overview)
2. [Problem Statement](#problem-statement)
3. [Objectives](#objectives)
4. [Dataset Description](#dataset-description)
5. [Tools Used](#tools-used)
6. [Data Cleaning Process](#data-cleaning-process)
7. [Data Analysis](#data-analysis)
8. [Dashboard](#dashboard)
9. [Key Insights](#key-insights)
10. [Recommendations](#recommendations)
11. [Limitations](#limitations)
12. [Conclusion](#conclusion)
13. [Project Files](#project-files)
14. [Contact Information](#contact-information)

## Project Overview
Banks sell term deposits (savings products where money is locked in for a fixed period at a set interest rate) by phone, and it's an expensive way to do it. Every call costs agent time, and calls to the wrong people annoy customers without adding a single subscription.

This project analyzes the results of a completed telemarketing campaign run by a Portuguese retail bank. The goal is to find out what separated the customers who subscribed to a term deposit from those who didn't, and to turn that into advice for the next campaign.

## Problem Statement
The bank wants its next term deposit campaign to bring in more subscriptions from fewer calls, but it has no data-driven way to tell which customers are likely to say yes. Effort gets spread across the whole customer list instead of going where it pays off.

Two questions guide the analysis:
* Why do some customers subscribe to term deposits while others do not?
* Which customer groups are more likely to respond positively to marketing campaigns?

## Objectives
* Understand the dataset and check its quality (size, data types, missing values, duplicates).
* Calculate the conversion rate (share of customers who subscribed) across demographic, financial and campaign-contact variables.
* Identify the factors that influence subscription the most.
* Present the findings in an interactive Power BI dashboard.
* Give 3 to 5 recommendations the marketing team can act on.

## Dataset Description
* Size: 11,162 rows and 17 columns.
* Target column: `deposit`, which says whether the customer subscribed to a term deposit (yes or no). 5,289 customers said yes (47.4%) and 5,873 said no (52.6%).
* Missing values: none (no blank cells).
* Duplicate rows: none.

The 17 columns fall into four groups:
* Customer profile: `age`, `job`, `marital`, `education`
* Financial profile: `default` (credit default), `balance`, `housing` (housing loan), `loan` (personal loan)
* Current campaign contact: `contact` (channel), `day`, `month`, `duration` (length of the last call in seconds), `campaign` (number of calls made in this campaign)
* Previous campaign history: `pdays` (days since the customer was last contacted, -1 means never), `previous` (number of earlier contacts), `poutcome` (result of the earlier campaign)

## Tools Used
* Power BI: data cleaning, data analysis, DAX measures and dashboard development
* PowerPoint: presentation of findings to stakeholders
* Word: written case study report

## Data Cleaning Process
The data was already in good shape. The checks I confirmed:
* No missing values and no duplicate rows across all 11,162 records.
* Data types were correct: 7 numeric columns and 10 text columns.
* "unknown" values are present in four columns and were kept as their own category rather than deleted: job (70 rows), education (497), contact (2,346) and poutcome (8,326).
* Continuous fields were grouped into bands so patterns are easier to read:
  * Age: Under 30, 30-39, 40-49, 50-59, 60+
  * Account balance: Negative, Zero, 1-1,000, 1,001-5,000, 5,000+
  * Number of calls: 1, 2, 3, 4-5, 6-10, 11+
  * Days since last contact (pdays): Never contacted, <3 months, 3-6 months, 6-12 months, 12+ months
* Conversion rate: calculated as the number of customers who subscribed divided by the number of customers in that group.

## Data Analysis
The analysis was built inside Power BI on a single table called bank (loaded from bank.csv). The idea was simple: define one measure that calculates the conversion rate, create grouped columns so that continuous fields can be read at a glance, then drop each grouped column into a chart next to the conversion rate. Every chart on the dashboard answers one part of the problem statement.

Before building anything I checked row and column counts, data types, missing values and summary statistics. A few numbers shaped the rest of the work: The average customer is about 41 years old (range 18 to 95).
The average balance is about 1,528, but the middle customer holds only 550, so a few very large accounts pull the average up. That's why balance needed to be grouped into bands.
The average call lasts about 372 seconds. Customers who subscribed talked for around 537 seconds on average, against 223 for those who didn't.
The average customer was called 2.5 times, but a few were called as many as 63 times. Again, bands were needed.
8,324 customers (about 75%) had never been contacted before. For them pdays is -1, which is a code for "never", not a real number of days, so it had to be handled separately.

Three columns were renamed so that anyone reading the dashboard understands the labels without a data dictionary:
* job became occupation
* contact became contact method
* poutcome became previous campaign outcome

Four DAX measures sit behind the cards and every chart. The key one is Conversion Rate. Because it's a measure and not a fixed number, it recalculates for whatever group a chart is showing. Put it next to "student" and it gives the student rate. Put it next to "11+ calls" and it gives that rate. That's how one formula produces all seven charts:

### Measures
1. total customers counts every row in the current group, since each row is one customer.

DAX
total customers = COUNTROWS('bank')

2. Total Deposits counts only the customers who subscribed to a term deposit (the "yes" answers). This is the 5.29K on the dashboard.

DAX
Total Deposits =
CALCULATE(
    COUNTROWS('bank'),
    'bank'[deposit] = "yes"
)

3. Conversion Rate divides subscribers by customers. DIVIDE is used instead of a plain / so that an empty group returns blank instead of an error.

DAX
Conversion Rate = DIVIDE([Total Deposits], [total customers])

4. Average Call Duration is the average length of the last call in seconds (371.99 on the dashboard).

DAX
Average Call Duration = AVERAGE('bank'[duration])

### Calculated columns
Age, balance, number of calls and days since last contact are all numbers with a lot of distinct values. Plotted raw, they'd give dozens of tiny bars with no pattern. I created four calculated columns that sort each customer into a band. Each uses SWITCH(TRUE(), ...), which checks the conditions from top to bottom and stops at the first one that is true.

1. Age Group splits customers into life stages.

DAX
Age Group =
SWITCH(
    TRUE(),
    'bank'[age] < 30, "Under 30",
    'bank'[age] <= 39, "30-39",
    'bank'[age] <= 49, "40-49",
    'bank'[age] <= 59, "50-59",
    "60+"
)

2. Balance Group separates customers in debt, customers with nothing, and then three levels of savings.

DAX
Balance Group =
SWITCH(
    TRUE(),
    'bank'[balance] < 0, "Negative",
    'bank'[balance] = 0, "Zero",
    'bank'[balance] <= 1000, "1-1,000",
    'bank'[balance] <= 5000, "1,001-5,000",
    "5,000+"
)

3. Campaign Group bands the number of calls made to a customer in this campaign. The bands get wider as the numbers get rarer, so the heavily called customers (6 and above) still show up as their own groups.

DAX
Campaign Group =
SWITCH(
    TRUE(),
    'bank'[campaign] = 1, "1 call",
    'bank'[campaign] = 2, "2 calls",
    'bank'[campaign] = 3, "3 calls",
    'bank'[campaign] <= 5, "4-5 calls",
    'bank'[campaign] <= 10, "6-10 calls",
    "11+ calls"
)

4. Pdays Group turns "days since the customer was last contacted" into a readable recency band. The first test catches the -1 code so that "never contacted" customers don't get mixed in with customers contacted very recently.

DAX
Pdays Group =
SWITCH(
    TRUE(),
    'bank'[pdays] = -1, "Never contacted",
    'bank'[pdays] <= 90, "<3 months",
    'bank'[pdays] <= 180, "3-6 months",
    'bank'[pdays] <= 365, "6-12 months",
    "12+ months"
)

## Dashboard
<img width="1459" height="817" alt="Screenshot (262)" src="https://github.com/user-attachments/assets/e6a0c318-cc84-4654-b99a-095f647fa15f" />

The dashboard has four headline numbers at the top:
* 11,162 total customers
* 5.29K total subscriptions
* 371.99 seconds average call duration
* 47.38% conversion rate.

Below them, seven charts break the conversion rate down by customer group and contact pattern. The charts cover conversion rate by:
* Occupation
* Days since last contact (pdays group)
* Contact method
* Age group
* Number of calls
* Account balance
* Outcome of the previous campaign

## Key Insights
### Why do some customers subscribe and others don't?
1. A good past experience is the best predictor of a yes: Customers who subscribed in an earlier campaign said yes again 91.32% of the time. Even customers who turned the bank down before still converted at 50.33%, which beats the overall average. Customers the bank has never reached before converted at only 40.67%. About three out of four customers in this campaign fell into that last group, which means most of the effort went into the hardest sales.
2. Staying in touch keeps the relationship warm: Customers last contacted within the past 3 months converted at 76.29%, and those contacted 3 to 6 months ago at 71.58%. Conversion dipped to 60.14% at 6 to 12 months, then recovered to 74.60% for people last contacted more than a year ago. So an old relationship doesn't go cold as quickly as you'd expect. Customers who were never contacted converted at 40.65%.
3. Calling more doesn't convince more people: The first call works best at 53.38%. After that, conversion falls: about 46% on the second and third calls, 39.69% at four to five calls, 31.40% at six to ten, and just 22.38% once a customer has been called eleven times or more. Past three calls, the bank is mostly irritating people.
4. Money in the account matters: Customers with a negative balance converted at 30.52%, and those with zero balance at 37.73%. The rate climbs steadily with the balance: 44.87% for 1 to 1,000, 55.26% for 1,001 to 5,000 and 57.20% for 5,000 and above. This makes sense, since a term deposit locks money away and people with little spare cash can't commit it.
5. Reaching the customer on a known phone line makes a big difference: Based on the underlying data, customers contacted on a mobile (cellular) number converted at 54.3% and those on a landline (telephone) at 50.4%, while customers with an "unknown" contact method converted at only 22.6%. These unknown contacts are a sign of poor record keeping, and they waste calls.
### Which customer groups are most likely to respond well?
6. Students and retirees are the strongest groups by occupation: Students converted at 74.72% and retirees at 66.32%. The unemployed came next at 56.58%, then management at 50.70%. Both leaders probably have more free time and fewer financial commitments than people in full-time work.
7. The youngest and oldest customers say yes most often: Customers aged 60 and over converted at 76.92%, and those under 30 at 59.83%. The middle of the age range is much weaker: 44.30% for people in their 30s, 40.45% in their 40s and 41.64% in their 50s. This matches the occupation result and suggests that life stage matters more than income.
8. Some groups are called as often as the best ones but convert at a much lower rate: Blue-collar workers (36.42%), entrepreneurs (37.50%), housemaids (39.78%) and services staff (39.98%) all sit well below average. The bank is spending nearly as much effort here as on its best segments, for a far smaller return.

### What this means for the business
* Opportunity: the group of customers who said yes before is small but nearly certain to say yes again. It's the cheapest source of new subscriptions the bank has.
* Opportunity: students, retirees and customers over 60 are three groups where a tailored offer would likely land well.
* Risk: the biggest share of calls goes to customers with no history and, in many cases, no reliable phone record. That's exactly where conversion is weakest, so the cost per subscription is highest.
* Risk: repeated calling past three attempts lowers the success rate and can damage how customers feel about the bank.

## Recommendations
1. Call the customers who have said yes before, first. Build the start of the next campaign around customers whose earlier campaign was a success (91.32%), then those whose result was "other" (57.17%) or a failure (50.33%). Reach cold prospects only after that. This group is small, but it will give the best return per call.
2. Stop calling after three attempts. The numbers show conversion dropping sharply after the third call. Set a limit of three calls per customer. The time saved can go to fresh leads or to returning customers, where each call is worth more.
3. Make offers for students, retirees and customers over 60. These groups already respond far above average. Design something specific for each: for example, a smaller minimum deposit for students and a message about steady, safe income for retirees. Under-30s (59.83%) are also worth a dedicated message.
4. Screen out customers with little or no money in the account before dialing. Customers with a negative or zero balance convert at 30.52% and 37.73%. Remove them from the term deposit call list, or offer them a simpler savings product that fits their situation. This will lift the average success rate of the calls that are left.
5. Fix the phone records before the campaign starts. Customers with an unknown contact method convert at about 22.6%. Check and update phone numbers first so agents reach people on a working mobile or landline. It costs very little and removes a lot of wasted calls.
6. Cut back effort on the low-converting occupations. Blue-collar workers, entrepreneurs, housemaids and services staff convert at 36 to 40%. Don't drop them completely, but put them lower on the call list unless they also have a successful history with the bank.
7. Keep customers warm between campaigns. The dashboard shows that contact in the last 3 months gives 76.29% conversion. A light touch, such as a message or a short check-in, between campaigns would help keep people receptive.

## Limitations
* One campaign only. The data comes from a single past campaign, so patterns may not hold in a different year or market.
* Conversion rate shows a link, not a cause. A high rate among retirees doesn't prove that being retired makes someone subscribe. Other factors may be involved. The analysis shows which groups converted more, not why.
* The dashboard looks at one factor at a time. Real customers combine several traits (for example, a young student with a negative balance), and the dashboard doesn't show those mixes.
* Small groups. Some segments are small, so their rates are less reliable: 12+ months since last contact (252 customers), 11+ calls (210), December (110) and unknown jobs (70).
* Many unknowns. 8,326 customers have no previous campaign outcome, and 2,346 have no recorded contact method.
* No statistical testing or predictive model. The findings use conversion rates and visuals only.

## Conclusion
Customers subscribe when the bank has history with them, when it reaches them on a working phone line, when it doesn't overcall them, and when they have enough money in the account to commit. The groups most likely to respond well are past subscribers, students, retirees, customers aged 60 and over, and customers with balances above 1,000.

The bigger lesson is that the next campaign will do better by using what the bank already knows about its customers, instead of making more calls. Calling past subscribers first, capping calls at three, tailoring offers to the best groups, and cleaning up contact records can lift the subscription rate without raising the cost of the campaign.

## Project Files
* [View Dataset](./bank.csv)
* [View Power BI report](./Bank_marketing_campaign_BIreport.pbix)
* [View Power point report](./Bank_Marketing_Campaign_Analysis.pptx)

## Contact Information
* Email: aladeloyeesther616@gmail.com
* LinkedIn: https://linkedin.com/in/estheraderonke
