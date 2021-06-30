# Advanced SQL for Data Scientists
Examples of advanced SQL concepts that are rarely covered in data science curricula.


## [Problem 1](Problem_1.md)
Given is a set of state-change event data. The source generates a record every time the state of an alarm sensor changes between "ACTIVE" and "INACTIVE". Alarm sensors are identified by their `tag`. The source table has the format:

| Field | Type | Description |
|-------|------|-------------|
| `ts`    | `TIMESTAMP` | date and time of the event |
| `tag`   | `VARCHAR(32)` | identification string of the alarm sensor |
| `state` | `VARCHAR(8)` | values "ACTIVE" or "INACTIVE" |

Transform the source table into a table with records for every instance that an alarm activates. The record should show timestamps for activation and deactivation, as well as, the duration of the active period in seconds.

The desired output table has the format:

| Field | Type | Description |
|-------|------|-------------|
| `start_ts`    | `TIMESTAMP` | date and time of the alarm becoming active |
| `end_ts`    | `TIMESTAMP` | date and time of the alarm becoming inactive |
| `tag`   | `VARCHAR(32)` | identification string of the alarm sensor |
| `duration` | `DECIMAL(10,0)` | duration of active alarm in seconds |


For the first part of the problem you can assume that every state change from "ACTIVE" is followed by as state change to "INACTIVE", and vice-versa. For every tag the data series starts with an "ACTIVE" event and ends with "INACTIVE".

In a second step, improve your solution to allow additional state changes. The active period of an alarm is between the state change event to "ACTIVE" and the next state change to "INACTIVE". Ignore any another "ACTIVE" state change occurs after the alarm is already active, and the same if the alarm is inactive. Alarms are assumed to be "INACTIVE" at the beginning of the data set. Ignore alarm activations that are not followed with an "INACTIVE" event.


## [Problem 2](Problem_2.md)

Given are trip data from the New York City Taxi & Limousine Commission 
https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page 

The data table includes a number of fields, though we are only interested in a few:

| Field | Type | Description |
|-------|------|-------------|
| `tpep_pickup_datetime` | `TIMESTAMP` | date and time of the start of the ride, pickup |
| `tpep_dropoff_datetime` | `TIMESTAMP` | date and time of the end of the ride, dropoff |
| `PULocationID` | `DECIMAL(5,0)`| Numeric ID of the zone where passengers are picked up|  
| `DOLocationID` | `DECIMAL(5,0)`| Numeric ID of the zone where passengers are dropped off|  

The goal is to predict the hourly demand of rides per zone based on the hourly pick-up and dropp-off rates for the past four hours. 

Create a table that can be used to train a machine learning model with the following fields:

| Field | Type | Description | Comment | 
|-------|------|-------------|----------|
| `pickup_date` | `DATE` | date of pickup | not used for ML |
| `actual_pickup` |`DECIMAL(6,0)` | number of pickups during this hour| ML target (y) | 
| `hour_of_day` | `DECIMAL(2,0)` | hour of day, based on `pickup_date` | ML feature | 
| `day_of_week` | `DECIMAL(1,0)` | day of the week, based on `pickup_date` | ML feature | 
| `zone_id `| `DECIMAL(5,0)` | zone ID of respective pickups and drop-offs | ML feature | 
| `pickup_1` | `DECIMAL(6,0)` | hourly pickups for the past hour |ML feature |
| `pickup_2` | `DECIMAL(6,0)` | hourly pickups for the hour before `pickup_1` |ML feature |
| `pickup_3` | `DECIMAL(6,0)` | hourly pickups for the hour before `pickup_2` |ML feature |
| `pickup_4` | `DECIMAL(6,0)` | hourly pickups for the hour before `pickup_3` |ML feature |
| `dropoff_1` | `DECIMAL(6,0)` | hourly drop-offs for the past hour |ML feature |
| `dropoff_2` | `DECIMAL(6,0)` | hourly pickups for the hour before `dropoff_1` |ML feature |
| `dropoff_3` | `DECIMAL(6,0)` | hourly pickups for the hour before `dropoff_2` |ML feature |
| `dropoff_4` | `DECIMAL(6,0)` | hourly pickups for the hour before `dropoff_3` |ML feature |

This table can be used to train a regression model for `actual_pickup` based on these features. Assume that each sample in this table is independent.

## [Problem 3](Problem_3.md)
Given is a data set of deals made by sales people on multiple teams within the organization. Create a table that shows the average sales volume per team per week.

The source table has the format:

| Field | Type | Description |
|-------|------|-------------|
| `transaction_id` | `DECIMAL(12,0)`| ID of sales transaction (not needed for the report)| 
| `ts` | `TIMESTAMP` | date and time of a sale |
| `employee_id` | `VARCHAR(9)` | unique employee ID |
| `team_id` | `VARCHAR(9)` | team ID |
| `amount` | `DECIMAL(6,2)`| Dollar amount of the sale |  
 

 Each sales person is allowed to give a high discount to one of their customers once a week. The discounted since the discounted sale would pull the team's average down, you need to exclude the **lowest** transaction for the week for each sales person.

 Create a summary table that reflects the weekly performance of each team:

| Field | Type | Description |
|-------|------|-------------|
| `week` | `DATE` | date of the reporting week |
| `team_id` | `VARCHAR(9)` | team ID |
| `average_amount` | `DECIMAL(6,2)`| Average dollar amount of the team for the week, the lowest sale for each team-member has been removed | 

For the first part of the problem assume that each sales person has at least two sales per week. So you are safe removing one of the sales.

Improve your solution to drop the previous assumption. If a sales person has only one sale per week then the amount of that transaction should be reflected in the team's average.