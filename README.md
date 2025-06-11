# time_series_visualizer


import matplotlib.pyplot as plt
import pandas as pd
import seaborn as sns
import numpy as np

# 1. Use Pandas to import the data from "fcc-forum-pageviews.csv".
# Set the index to the date column.
# Parse 'date' column as dates and set it as the index.
df = pd.read_csv(
    "fcc-forum-pageviews.csv", 
    parse_dates=['date'], 
    index_col='date'
)

# 2. Clean the data by filtering out days when the page views were in the top 2.5%
# of the dataset or bottom 2.5% of the dataset.
# Calculate lower and upper bounds for filtering
lower_bound = df['value'].quantile(0.025)
upper_bound = df['value'].quantile(0.975)

# Filter the DataFrame based on the calculated bounds
df = df[(df['value'] >= lower_bound) & (df['value'] <= upper_bound)]


def draw_line_plot():
    # Create a copy of the DataFrame for this plot to ensure no modifications affect other plots
    df_line = df.copy()

    # 3. Create a draw_line_plot function that uses Matplotlib to draw a line chart.
    # The title should be 'Daily freeCodeCamp Forum Page Views 5/2016-12/2019'.
    # The label on the x axis should be 'Date' and the label on the y axis should be 'Page Views'.
    
    fig, ax = plt.subplots(figsize=(15, 6)) # Create a figure and a set of subplots
    ax.plot(df_line.index, df_line['value'], color='red') # Plot the line chart

    ax.set_title('Daily freeCodeCamp Forum Page Views 5/2016-12/2019') # Set the title
    ax.set_xlabel('Date') # Set x-axis label
    ax.set_ylabel('Page Views') # Set y-axis label

    # Save image (boilerplate usually calls fig.savefig)
    fig.savefig('line_plot.png')
    return fig


def draw_bar_plot():
    # Create a copy of the DataFrame for this plot
    df_bar = df.copy()

    # Prepare data for bar plot: calculate average daily page views for each month grouped by year.
    # Extract year and month from the index
    df_bar['year'] = df_bar.index.year
    df_bar['month'] = df_bar.index.strftime('%b') # %b gives abbreviated month name (Jan, Feb, etc.)

    # Calculate average page views grouped by year and month
    df_bar_avg = df_bar.groupby(['year', 'month'])['value'].mean().unstack()

    # 4. Create a draw_bar_plot function that draws a bar chart.
    # It should show average daily page views for each month grouped by year.
    # The legend should show month labels and have a title of 'Months'.
    # On the chart, the label on the x axis should be 'Years' and the label on the y axis should be 'Average Page Views'.

    fig, ax = plt.subplots(figsize=(12, 8)) # Create figure and axes
    
    # Plot the bar chart
    df_bar_avg.plot(kind='bar', ax=ax)

    ax.set_xlabel('Years') # Set x-axis label
    ax.set_ylabel('Average Page Views') # Set y-axis label
    
    # Define month order for the legend and consistency
    month_order = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
    ax.legend(title='Months', labels=month_order) # Set legend title and labels

    # Ensure layout is tight
    plt.tight_layout()

    # Save image
    fig.savefig('bar_plot.png')
    return fig


def draw_box_plot():
    # Create a copy of the DataFrame for this plot
    df_box = df.copy()

    # Prepare data for box plots: extract year and month
    df_box['year'] = df_box.index.year
    df_box['month'] = df_box.index.strftime('%b') # Abbreviated month name (Jan, Feb, etc.)

    # Define month order for correct display on x-axis
    month_order = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']

    # 5. Create a draw_box_plot function that uses Seaborn to draw two adjacent box plots.
    # The title of the first chart should be 'Year-wise Box Plot (Trend)'.
    # The title of the second chart should be 'Month-wise Box Plot (Seasonality)'.
    # Make sure the month labels on bottom start at 'Jan' and the x and y axis are labeled correctly.

    fig, axes = plt.subplots(1, 2, figsize=(20, 8)) # Create a figure with two subplots side-by-side

    # --- Year-wise Box Plot (Trend) ---
    sns.boxplot(x='year', y='value', data=df_box, ax=axes[0])
    axes[0].set_title('Year-wise Box Plot (Trend)') # Set title for the first subplot
    axes[0].set_xlabel('Year') # Set x-axis label
    axes[0].set_ylabel('Page Views') # Set y-axis label

    # --- Month-wise Box Plot (Seasonality) ---
    sns.boxplot(x='month', y='value', data=df_box, ax=axes[1], order=month_order) # Use month_order for correct sorting
    axes[1].set_title('Month-wise Box Plot (Seasonality)') # Set title for the second subplot
    axes[1].set_xlabel('Month') # Set x-axis label
    axes[1].set_ylabel('Page Views') # Set y-axis label

    # Ensure tight layout
    plt.tight_layout()

    # Save image
    fig.savefig('box_plot.png')
    return fig

# --- Main execution block (for local testing) ---
if __name__ == "__main__":
    # You need 'fcc-forum-pageviews.csv' in the same directory.
    # Download it from: https://raw.githubusercontent.com/freeCodeCamp/boilerplate-time-series-visualizer/main/fcc-forum-pageviews.csv

    # The initial loading and cleaning of df is done globally at the top of the file
    # as per typical FreeCodeCamp project boilerplate structure.
    
    print("Running line plot...")
    draw_line_plot()
    print("Running bar plot...")
    draw_bar_plot()
    print("Running box plot...")
    draw_box_plot()
    
    plt.show() # Display all plots (if not automatically displayed by savefig/backend)
