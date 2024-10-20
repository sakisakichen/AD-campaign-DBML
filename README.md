# AD-campaign-DBML
internal team needs to create a marketing dashboard filterable by campaign type, date, and hour for comparing advertising campaigns.

They want to examine:
1. Budget and Spend Information
2. Impression Share Performance
3. View Through Conversions by Ad Group Type and Keyword
4. Goal Counts

## Step 1 : Business Requirements Verify 
 <details>
  
 (1) The team needs to compare advertising campaigns.   -> List campaigns type down   
 (2) They want to filter data by campaign type, date, and hour. -> time duration filter not only 'Date', but also 'Hour'     
 (3) Budget and Spend Information ->  Budget allocated to which campaign, actual spending number & currency type    
 (4) Impression Share Performance->  Impression share, absolute top impression share, impressions received, and eligible impressions.   
 (5) View Through Conversions: Breakdown by ad group and keyword.   
 (6) Goal Counts: Number of goals completed.     
 
### Key Filters for Analysis:

#### (1) Campaign Type: 
Different types of campaigns （high level strategy) 
#### (2) Date: 
Specific dates or date ranges.
#### (3) Hour: 
Breakdown by time of day (e.g., hour granularity).
#### (4) AD Group Detail 
types about AD groups(e.g., Search, Display, Video)
#### (5) Goal Counts: 
Number of goals completed.
</details>

## Step 2 : Design Table
 <details>
  
Based on the input above, tables design idea  will be 
### Fact1 : Fact tables about Budget & Spend of which compaign 
budget, spend , currency , campaign ID, time (foreign key to Dat tables & hour table)   
### Fact2 : Impression Share Performance of which compaign 
ID(foreign key connecting to campaign & date & hour tables), Impression share, Absolute top impression share, Impressions received, Eligible impressions    
### Fact3 : View Through Conversions of which compaign ,keywords    
ID(foreign key connecting to campaign & date & hour tables) View through conversions, tracked by ad group and keyword.  
### Fact4 : Goal Counts of which compaign 
ID(foreign key connecting to campaign & date & hour tables) , counts   

Description tables (dimension Tables) will be 
### Dim1 : Campaigns 
ID, Campaign name, Campaign type (e.g., Search, Display, Social Media), Campaign status (active, paused, etc.)Start and end dates
### Dim2: Ad group name
ID, Campaign ID (foreign key linking to Campaigns)  AD group name, Ad group status  

### Dim3: Keywords  
ID, Key word description, Keyword status, Ad group ID   
### Dim4: Date:
ID,Full date ,Year, Quarter ,Month ,Week   
#### Dim5: Hour:  
ID , Hour (0–23)  
</details>


## Step 3 :DBML 
<details>   
 
 ```sql

Table campaigns_dim {
    id int [primary key] 
    name varchar
    type varchar 
    status varchar
    start_date date
    end_date date
}

Table ad_groups_dim {
    id int [primary key]
    campaign_id int [ref: > campaigns_dim.id]
    name varchar
    status varchar
}

Table keywords_dim {
    id int [primary key]
    ad_group_id int [ref: > ad_groups_dim.id]
    keyword varchar
    keyword_status varchar
}

Table date_dim {
    id int [primary key]
    full_date date
    year int
    quarter int
    month int
    week int
}

Table hour_dim {
    id int [primary key]
    hour int 
}

Table fact_budget_spend {
    campaign_id int [ref: > campaigns_dim.id]
    date_id int [ref: > date_dim.id]
    hour_id int [ref: > hour_dim.id]
    budget decimal
    spend decimal
    currency varchar
}

Table fact_impression_share {
    campaign_id int [ref: > campaigns_dim.id]
    ad_group_id int [ref: > ad_groups_dim.id]
    date_id int [ref: > date_dim.id]
    hour_id int [ref: > hour_dim.id]
    impression_share decimal
    absolute_top_impression_share decimal
    impressions_received int
    eligible_impressions int
}

Table fact_view_through_conversions {
    campaign_id int [ref: > campaigns_dim.id]
    ad_group_id int [ref: > ad_groups_dim.id]
    keyword_id int [ref: > keywords_dim.id]
    date_id int [ref: > date_dim.id]
    hour_id int [ref: > hour_dim.id]
    view_through_conversions int
}

Table fact_goal_counts {
    campaign_id int [ref: > campaigns_dim.id]
    date_id int [ref: > date_dim.id]
    hour_id int [ref: > hour_dim.id]
    goals_count int
}

 ```

</details>

## Step 4 :SQL Query

 
### 1. Budget and Spend Information
 <details>
  
 ```sql
SELECT 
    c.name AS campaign_name,
    c.type AS campaign_type,
    d.full_date,
    h.hour,
    fbs.budget,
    fbs.spend,
    fbs.month_to_date_spend
FROM fact_budget_spend fbs
JOIN campaigns c ON fbs.campaign_id = c.id
JOIN date_dim d ON fbs.date_id = d.id
JOIN hour_dim h ON fbs.hour_id = h.id
WHERE c.type = 'Search' --  filter
AND d.full_date BETWEEN '2024-10-01' AND '2024-10-19' -- filter
AND h.hour BETWEEN 9 AND 18 -- filter

 ```

</details>



### 2. Impression Share Performance
 <details>
  
 ```sql
SELECT 
    c.name AS campaign_name,
    ag.name AS ad_group_name,
    d.full_date,
    h.hour,
    fis.impression_share,
    fis.absolute_top_impression_share,
    fis.impressions_received,
    fis.eligible_impressions
FROM fact_impression_share fis
JOIN campaigns c ON fis.campaign_id = c.id
JOIN ad_groups ag ON fis.ad_group_id = ag.id
JOIN date_dim d ON fis.date_id = d.id
JOIN hour_dim h ON fis.hour_id = h.id
WHERE c.type = 'Display' -- filter
AND d.full_date = '2024-10-18' -- filter


 ```

</details>

### 3. View Through Conversions by Ad Group Type and Keyword
 <details>
  
 ```sql

SELECT 
    c.name AS campaign_name,
    ag.name AS ad_group_name,
    k.keyword,
    k.match_type,
    d.full_date,
    h.hour,
    fvtc.view_through_conversions
FROM fact_view_through_conversions fvtc
JOIN campaigns c ON fvtc.campaign_id = c.id
JOIN ad_groups ag ON fvtc.ad_group_id = ag.id
JOIN keywords k ON fvtc.keyword_id = k.id
JOIN date_dim d ON fvtc.date_id = d.id
JOIN hour_dim h ON fvtc.hour_id = h.id
WHERE c.type = 'Social Media'  -- filter
AND ag.bid_strategy_type = 'Maximize Clicks'  -- filter

 ```
</details>



### 4. Goal Counts
 <details>
  
 ```sql

SELECT 
    c.name AS campaign_name,
    d.full_date,
    h.hour,
    fgc.goals_count
FROM fact_goal_counts fgc
JOIN campaigns c ON fgc.campaign_id = c.id
JOIN date_dim d ON fgc.date_id = d.id
JOIN hour_dim h ON fgc.hour_id = h.id
WHERE c.type = 'Search' -- filter
AND d.full_date BETWEEN '2024-10-01' AND '2024-10-19' -- filter

 ```
</details>
