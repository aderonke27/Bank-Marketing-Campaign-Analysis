# Bank-Marketing-Campaign-Analysis

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
The analysis centers on the conversion rate: the number of customers who subscribed divided by the number of customers contacted. The overall rate is 47.38%. I calculated it for each major field and compared each group against that baseline.

Dataset summary

Total customers: 11,162
Subscriptions: 5,289
Overall conversion rate: 47.38%
Average call duration: 371.99 seconds (about 6.2 minutes)
Average age: 41.2 years (median 39)
Median account balance: 550 (mean 1,528.54)
Average calls per customer this campaign: 2.51 (median 2)
Conversion rate by segment (all figures checked against bank.csv)

Previous outcome: success: 91.32% (1,071 customers)
Previous outcome: unknown (never contacted before): 40.67% (8,326 customers)
Student: 74.72% (360 customers)
Retired: 66.32% (778 customers)
Blue-collar (lowest job group): 36.42% (1,944 customers)
Age 60+: 76.92% (780 customers)
Age 40-49 (lowest age group): 40.45% (2,628 customers)
1 call: 53.38% (4,798 customers)
11+ calls: 22.38% (210 customers)
Negative balance: 30.52% (688 customers)
Balance 5,000+: 57.20% (771 customers)
Contact: cellular: 54.33% (8,042 customers)
Contact: telephone: 50.39% (774 customers)
Contact: unknown: 22.59% (2,346 customers)
Contacted under 3 months ago: 76.29% (329 customers)
Never contacted before: 40.65% (8,324 customers)
Other patterns found in the data:

Education: tertiary 54.11%, secondary 44.74%, primary 39.40%.
Marital status: single 54.35%, divorced 48.11%, married 43.38%.
Loans: customers with a housing loan convert at 36.64% (vs 57.03% without). Customers with a personal loan convert at 33.15% (vs 49.53% without).
Month: conversion ranges from 32.75% in May to 90.91% in December, though December has only 110 customers.
Call duration: subscribers averaged 537 seconds per call and non-subscribers 223 seconds.
[TO FILL: the project brief asks for histograms, box plots and a correlation heatmap. Add them here if you created them (in Power BI, Python or Excel) and link the images. If you didn't, either create them or delete this note. For reference, the largest correlation between numeric fields is pdays and previous at 0.51, and no numeric field correlates strongly with age or balance.]
I started with a standard exploratory pass: row and column counts, data types, missing values, summary statistics and the distribution of the main variables.

A few numbers from that first look:

- The average customer is about 41 years old (range 18 to 95).
- The average account balance is about 1,528, but the middle customer holds only 550, so a small number of very wealthy accounts pull the average up.
- The average call lasts about 372 seconds (just over 6 minutes). Customers who subscribed talked for around 537 seconds on average, against 223 seconds for those who didn't.
- The average customer was called 2.5 times in the campaign, but a few were called as many as 63 times.
- 8,324 customers (about 75%) had never been contacted in an earlier campaign.

From there I calculated the conversion rate for every group in each field, which is what the dashboard shows. I also trained a Random Forest model to rank which fields matter most. Past campaign history, contact method, timing and financial profile came out as the strongest signals that the bank knows before it picks up the phone.

One caution: call duration is the single strongest predictor of a yes, but the bank only knows it after the call is over. It can't be used to decide who to call, so the recommendations below rely on information available beforehand.

## Dashboard

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

1. Campaign history is the strongest predictor. Customers who said yes in a previous campaign converted at 91.32%, against 40.67% for customers with no previous outcome. That gap is bigger than the spread within any demographic variable. Recent contact helps too: customers contacted within the last 3 months converted at 76.29%.
2. More calls means lower conversion. The rate falls from 53.38% on one call to 22.38% at 11 or more. The decline isn't perfectly smooth: 2 calls (46.27%) and 3 calls (46.78%) are about the same, but after 3 calls it drops steadily (4 calls 41.12%, 5 calls 36.77%, 6 to 10 calls 29.16%).

3. Life stage matters more than income. Students (74.72%) and retirees (66.32%) convert far above the 47.38% average. Age follows a U-shape: under 30 (59.83%) and 60+ (76.92%) do well, while the 40-49 group is lowest at 40.45%.

4. Financial capacity matters. Conversion climbs from 30.52% for negative balances to 57.20% for balances above 5,000. Customers with a housing loan or personal loan convert noticeably less than those without.

5. Blue-collar, housemaid and entrepreneur customers are a weak spot. Together they make up 22.8% of the customer base and convert at 36.92%, about 10 points below average.

6. Contact data quality is a problem. 21.0% of customers (2,346) have an unknown contact method, and they convert at only 22.59%, compared with 54.33% for cellular.

7. Timing shows a pattern. May carries the heaviest call volume (2,824 customers) and has the lowest conversion (32.75%). September to December converts at 62.1% overall, but on a much smaller volume (1,764 customers).

## Recommendations
1. Call the customers who have said yes before, first. Build the start of the next campaign around customers whose earlier campaign was a success (91.32%), then those whose result was "other" (57.17%) or a failure (50.33%). Reach cold prospects only after that. This group is small, but it will give the best return per call.
2. Stop calling after three attempts. The numbers show conversion dropping sharply after the third call. Set a limit of three calls per customer. The time saved can go to fresh leads or to returning customers, where each call is worth more.
3. Make offers for students, retirees and customers over 60. These groups already respond far above average. Design something specific for each: for example, a smaller minimum deposit for students and a message about steady, safe income for retirees. Under-30s (59.83%) are also worth a dedicated message.
4. Screen out customers with little or no money in the account before dialing. Customers with a negative or zero balance convert at 30.52% and 37.73%. Remove them from the term deposit call list, or offer them a simpler savings product that fits their situation. This will lift the average success rate of the calls that are left.
5. Fix the phone records before the campaign starts. Customers with an unknown contact method convert at about 22.6%. Check and update phone numbers first so agents reach people on a working mobile or landline. It costs very little and removes a lot of wasted calls.
6. Cut back effort on the low-converting occupations. Blue-collar workers, entrepreneurs, housemaids and services staff convert at 36 to 40%. Don't drop them completely, but put them lower on the call list unless they also have a successful history with the bank.
7. Keep customers warm between campaigns. The dashboard shows that contact in the last 3 months gives 76.29% conversion. A light touch, such as a message or a short check-in, between campaigns would help keep people receptive.

* Re-engage prior-success customers first. Build a dedicated outreach list from the 1,071 customers with a previous success. At 91.32%, no other segment offers a better return.
* Limit follow-up calls to about 3 per customer. Conversion stays at about 46% or higher through the third call and falls to 41% at the fourth. Stopping after 3 unsuccessful attempts frees up calls for better leads.
* Tailor offers to students and retirees. Both convert well above average. Consider offer structures that fit each group, such as smaller minimum deposits for students and income-focused messaging for retirees. (This is a suggestion, not something the data tests.)
* Screen out or redirect low-balance leads. Customers with negative (30.52%) or zero (37.73%) balances convert well below average. Remove them from the call list or offer them a different product.
* Clean contact records and review campaign timing. Fix unknown contact methods before launch, and look at moving call volume away from May toward the months where conversion was stronger. [TO FILL: this data covers one campaign, so check whether you want to word the timing advice as "test" instead of "move".]

## Limitations
* One campaign only. The data comes from a single past campaign, so patterns may not hold in a different year or market.
* Correlation, not causation. The analysis shows which groups converted more, not why.
* Call duration can't be used for targeting. Longer calls go with subscriptions (537 vs 223 seconds), but the duration is only known after the call happens, so it can't help decide who to call.
* Small groups. Some segments are small, so their rates are less reliable: 12+ months since last contact (252 customers), 11+ calls (210), December (110) and unknown jobs (70).
* Many unknowns. 8,326 customers have no previous campaign outcome, and 2,346 have no recorded contact method.
* No statistical testing or predictive model. The findings use conversion rates and visuals only. [TO FILL: if you ran any statistical tests or built a model, describe them here.]
* The data covers one campaign. Results may not hold for a different time, a different product or a different country. The bank in this dataset is Portuguese.
* Conversion rate shows a link, not a cause. A high rate among retirees doesn't prove that being retired makes someone subscribe. Other factors may be involved.
* The dashboard looks at one factor at a time. Real customers combine several traits (for example, a young student with a negative balance), and the dashboard doesn't show those mixes.
* Group sizes differ. A few groups are small, so their percentages can swing more than the larger groups. The dashboard doesn't show group sizes.
* Call duration can't be used for targeting. It's known only after the call ends, so it helps explain results but not who to call.
* Unknown values. Some customers have unknown occupation, education or contact method, which blurs the picture for those fields.
* The contact method donut chart shows rates as shares of a total, not true conversion rates (see the note under Key Insights).
* Timing isn't on the dashboard. The raw data shows big differences by month (May converts at 32.8% while December, March, September and October convert at 82 to 91%), but this isn't in the dashboard yet. It's worth adding.

## Conclusion
Customers subscribe when the bank has history with them, when it reaches them on a working phone line, when it doesn't overcall them, and when they have enough money in the account to commit. The groups most likely to respond well are past subscribers, students, retirees, customers aged 60 and over, and customers with balances above 1,000.

The bigger lesson is that the next campaign will do better by using what the bank already knows about its customers, instead of making more calls. Calling past subscribers first, capping calls at three, tailoring offers to the best groups, and cleaning up contact records can lift the subscription rate without raising the cost of the campaign.

## Project Files

## Contact Information
* Email: aladeloyeesther616@gmail.com
* LinkedIn: https://linkedin.com/in/estheraderonke
