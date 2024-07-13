Financial Data Analysis Tools
This repository provides a set of Python functions designed for financial data analysis, specifically focusing on calculating Simple Moving Averages (SMA), computing the distance of stock prices from these SMAs, visualizing the distributions of these distances, testing for normality, and plotting candlestick charts with SMAs.

Overview
The repository includes the following functionalities:

SMA Calculation: Computes Simple Moving Averages for given periods.
Distance Calculation: Computes the percentage distance of the closing prices from the SMAs.
Histogram Plotting: Generates histograms to visualize the distributions of the distances.
Normality Testing: Performs the Shapiro-Wilk test to check if the distances follow a normal distribution.
Candlestick Chart with SMAs: Plots a candlestick chart with SMAs overlaid for visual analysis.
Features
Automatic SMA Calculation: The SMA function calculates and appends SMA columns to your data for the specified periods.
Automated Distance Calculation: The calculate_distances function computes the percentage distance of the adjusted closing prices from the SMAs and appends these distances as new columns.
Dynamic Histogram Plotting: The plot_distance_histograms function automatically identifies the distance columns and generates individual histograms with distinct colors for better visualization.
Efficient Normality Testing: The check_normality function runs the Shapiro-Wilk test on the distance columns and returns a detailed report of the test statistics and p-values.
Candlestick Chart with SMAs: The plot_candlestick_with_sma function plots a candlestick chart with SMAs overlaid, providing a comprehensive view of price movements and trends.
Installation
Ensure you have the necessary libraries installed:

pip install pandas plotly scipy
