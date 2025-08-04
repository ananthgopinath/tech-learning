import pandas as pd

# Sample input DataFrame structure (replace this with your actual data)
# df = pd.read_csv("your_tickets.csv")

# Example schema assumed:
# df.columns = ['category', 'sub_category', 'priority', 'time_to_close_hrs', 'text']

# Step 1: Aggregate ticket info
agg_df = df.groupby(['category', 'sub_category', 'priority']).agg(
    ticket_count=('text', 'count'),
    avg_time_to_close=('time_to_close_hrs', 'mean'),
    max_time_to_close=('time_to_close_hrs', 'max')
).reset_index()

# Step 2: Define rule-based recommendation logic
def category_based_recommendation(row):
    category = row['category'].lower()
    sub_category = row['sub_category'].lower()
    priority = row['priority'].upper()
    count = row['ticket_count']
    avg_time = row['avg_time_to_close']

    if category == 'monitoring':
        if count > 30:
            return "Review and optimize noisy alerts"
        else:
            return "Review alert rules"

    elif category == 'bau coordination':
        if sub_category == 'bau meetings':
            if count > 20:
                return "Merge or document repetitive BAU meetings"
            else:
                return "Standardize BAU meeting templates"
        elif count > 50 and avg_time < 2:
            return "Automate BAU coordination steps"
        else:
            return "SOP/document coordination tasks"

    elif category == 'alert/issue investigation':
        if sub_category == 'batch-job failure':
            if avg_time > 6:
                return "Implement retries and alert for early failure detection"
            else:
                return "Monitor job dependencies and reduce manual intervention"
        elif priority in ['P1', 'HIGH'] and avg_time > 12:
            return "Investigate root cause, consider automation"
        else:
            return "Build knowledge base for recurring issues"

    elif category == 'general request':
        if avg_time < 1:
            return "Move to self-service portal"
        else:
            return "Review request types for automation"

    elif category == 'feed query':
        if count > 20:
            return "Create dashboards for frequently queried feeds"
        else:
            return "Review and document feed access procedures"

    else:
        return "General recommendation – analyze further"

# Step 3: Apply logic to aggregated data
agg_df['recommendation'] = agg_df.apply(category_based_recommendation, axis=1)

# Optional: Save to file
agg_df.to_csv('ticket_recommendations.csv', index=False)

# Print sample
print(agg_df.head())
