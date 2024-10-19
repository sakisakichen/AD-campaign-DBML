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
Goal Counts: Number of goals completed.
 
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

### Fact3 : View Through Conversions of which compaign 

### Fact4 : Goal Counts of which compaign 

Description tables (dimension Tables) will be 
### Dim1 : Campaigns 
ID, Campaign name, Campaign type (e.g., Search, Display, Social Media), Campaign status (active, paused, etc.)Start and end dates
### Dim2: Ad group name
ID, Campaign ID (foreign key linking to Campaigns)  AD group name, Ad group status  

### Dim2: Keywords  
ID, Key word description, Keyword status, Ad group ID   
### Dim4: Date:
ID,Full date ,Year, Quarter ,Month ,Week   
#### Dim5: Hour:  
ID , Hour (0–23)  
</details>
