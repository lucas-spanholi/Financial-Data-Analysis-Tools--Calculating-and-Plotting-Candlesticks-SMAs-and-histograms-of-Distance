# Financial-Data-Analysis-Tools--Calculating-and-Plotting-Candlesticks-SMAs-and-histograms-of-Distance
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

bash
Copiar código
pip install pandas plotly scipy
Usage
Example Code
python
Copiar código
import pandas as pd
import plotly.graph_objects as go
import scipy.stats as stats

def SMA(data, *periods, column='Adj Close'):
    for period in periods:
        data[f'SMA_{period}'] = data[column].rolling(window=period).mean()
    return data

def calculate_distances(data, *periods, column='Adj Close'):
    for period in periods:
        sma_column = f'SMA_{period}'
        if sma_column in data.columns:
            data[f'Distance_SMA{period} %'] = ((data[column] - data[sma_column]) / data[sma_column]) * 100
        else:
            raise ValueError(f'Missing SMA column for period {period}. Make sure to calculate SMA before calling this function.')
    return data

def plot_candlestick_with_sma(data):
    # Verifica se as colunas necessárias estão presentes
    required_columns = ['Open', 'High', 'Low', 'Close']
    for col in required_columns:
        if col not in data.columns:
            raise ValueError(f"Data must contain '{col}' column.")
    
    # Identifica as colunas de SMA
    sma_columns = [col for col in data.columns if col.startswith('SMA_')]
    
    if not sma_columns:
        raise ValueError("No SMA columns found in the DataFrame. Make sure to calculate SMAs first.")
    
    # Cria a figura do candlestick
    fig = go.Figure(data=[go.Candlestick(
        x=data.index,
        open=data['Open'],
        high=data['High'],
        low=data['Low'],
        close=data['Close'],
        name='Candlestick'
    )])
    
    # Adiciona as SMAs ao gráfico
    for sma_column in sma_columns:
        fig.add_trace(go.Scatter(
            x=data.index,
            y=data[sma_column],
            mode='lines',
            name=sma_column
        ))
    
    # Atualiza o layout do gráfico
    fig.update_layout(
        title='Candlestick Chart with SMAs',
        xaxis_title='Date',
        yaxis_title='Price',
        template='plotly_white'
    )
    
    fig.show()

def plot_distance_histograms(data, nbins=50):
    # Identifica as colunas de distância
    distance_columns = [col for col in data.columns if col.startswith('Distance_SMA') and col.endswith('%')]
    
    if not distance_columns:
        raise ValueError("No distance columns found in the DataFrame. Make sure to calculate distances first.")
    
    # Paleta de cores para os histogramas
    colors = ['blue', 'green', 'red', 'purple', 'orange', 'brown', 'pink', 'gray', 'olive', 'cyan']
    
    # Cria subplots
    fig = make_subplots(rows=len(distance_columns), cols=1, subplot_titles=[f'Distribution of {col}' for col in distance_columns])
    
    # Adiciona histogramas
    for i, col in enumerate(distance_columns, start=1):
        color = colors[i % len(colors)]  # Seleciona a cor da paleta, repetindo se necessário
        fig.add_trace(go.Histogram(x=data[col], name=col, nbinsx=nbins, marker_color=color, opacity=0.75), row=i, col=1)
    
    # Ajusta o layout
    fig.update_layout(
        height=400*len(distance_columns),  # Altura ajustada para melhorar a visibilidade
        title_text="Histograms of Distance Percentages to SMAs",
        title_x=0.5,
        showlegend=False,
        template='plotly_white',
        margin=dict(l=50, r=50, t=80, b=50)
    )
    
    # Ajusta os títulos dos eixos
    fig.update_xaxes(title_text="Distance Percentage (%)")
    fig.update_yaxes(title_text="Frequency")
    
    fig.show()

def check_normality(data):
    # Identifica as colunas de distância
    distance_columns = [col for col in data.columns if col.startswith('Distance_SMA') and col.endswith('%')]
    
    if not distance_columns:
        raise ValueError("No distance columns found in the DataFrame. Make sure to calculate distances first.")
    
    normality_results = {}
    
    for col in distance_columns:
        stat, p_value = stats.shapiro(data[col].dropna())
        normality_results[col] = {'Statistic': stat, 'p-value': p_value}
    
    return normality_results

# Exemplo de uso:
data = pd.read_csv('your_data.csv')  # Substitua pelo caminho do seu arquivo de dados

# Calcular SMAs usando 'Adj Close'
data = SMA(data, 20, 50, 100, column='Adj Close')

# Calcular distâncias usando 'Adj Close'
data = calculate_distances(data, 20, 50, 100, column='Adj Close')
print(data.head())

# Plotar gráfico de candlestick com SMAs
plot_candlestick_with_sma(data)

# Plotar histogramas de distâncias
plot_distance_histograms(data, nbins=100)

# Verificar normalidade das distâncias
normality_results = check_normality(data)

# Exibir resultados dos testes de normalidade
for col, result in normality_results.items():
    print(f"{col}:")
    print(f"  Statistic: {result['Statistic']}")
    print(f"  p-value: {result['p-value']}\n")
Function Descriptions
SMA Calculation
python
Copiar código
def SMA(data, *periods, column='Adj Close'):
    for period in periods:
        data[f'SMA_{period}'] = data[column].rolling(window=period).mean()
    return data
Calculates the Simple Moving Averages for the specified periods and appends them to the DataFrame.

Distance Calculation
python
Copiar código
def calculate_distances(data, *periods, column='Adj Close'):
    for period in periods:
        sma_column = f'SMA_{period}'
        if sma_column in data.columns:
            data[f'Distance_SMA{period} %'] = ((data[column] - data[sma_column]) / data[sma_column]) * 100
        else:
            raise ValueError(f'Missing SMA column for period {period}. Make sure to calculate SMA before calling this function.')
    return data
Calculates the percentage distance of the adjusted closing prices from the SMAs.

Candlestick Chart with SMAs
python
Copiar código
def plot_candlestick_with_sma(data):
    # Verifica se as colunas necessárias estão presentes
    required_columns = ['Open', 'High', 'Low', 'Close']
    for col in required_columns:
        if col not in data.columns:
            raise ValueError(f"Data must contain '{col}' column.")
    
    # Identifica as colunas de SMA
    sma_columns = [col for col in data.columns if col.startswith('SMA_')]
    
    if not sma_columns:
        raise ValueError("No SMA columns found in the DataFrame. Make sure to calculate SMAs first.")
    
    # Cria a figura do candlestick
    fig = go.Figure(data=[go.Candlestick(
        x=data.index,
        open=data['Open'],
        high=data['High'],
        low=data['Low'],
        close=data['Close'],
        name='Candlestick'
    )])
    
    # Adiciona as SMAs ao gráfico
    for sma_column in sma_columns:
        fig.add_trace(go.Scatter(
            x=data.index,
            y=data[sma_column],
            mode='lines',
            name=sma_column
        ))
    
    # Atualiza o layout do gráfico
    fig.update_layout(
        title='Candlestick Chart with SMAs',
        xaxis_title='Date',
        yaxis_title='Price',
        template='plotly_white'
    )
    
    fig.show()
Plots a candlestick chart with the identified SMAs overlaid, providing a comprehensive view of price movements and trends.

Histogram Plotting
python
Copiar código
def plot_distance_histograms(data, nbins=50):
    # Identifica as colunas de distância
    distance_columns = [col for col in data.columns if col.startswith('Distance_SMA') and col.endswith('%')]
    
    if not distance_columns:
        raise ValueError("No distance columns found in the DataFrame. Make sure to calculate distances first.")
    
    # Paleta de cores para os histogramas
    colors = ['blue', 'green', 'red', 'purple', 'orange', 'brown', 'pink', 'gray', 'olive', 'cyan']
    
    # Cria subplots
    fig = make_subplots(rows=len(distance_columns), cols=1, subplot_titles=[f'Distribution of {col}' for col in distance_columns])
    
    # Adiciona histogramas
    for i, col in enumerate(distance_columns, start=1):
        color = colors[i % len(colors)]  # Seleciona a cor da paleta, repetindo se necessário
        fig.add_trace(go.Histogram(x=data[col], name=col, nbinsx=nbins, marker_color=color, opacity=0.75), row=i, col=1)
    
    # Ajusta o layout
    fig.update_layout(
        height=400*len(distance_columns),
        title_text="Histograms of Distance Percentages to SMAs",
        title_x=0.5,
        showlegend=False,
        template='plotly_white',
        margin=dict(l=50, r=50, t=80, b=50)
    )
    
    # Ajusta os títulos dos eixos
    fig.update_xaxes(title_text="Distance Percentage (%)")
    fig.update_yaxes(title_text="Frequency")
    
    fig.show()
Automatically identifies the distance columns and generates histograms for each.

Normality Testing
python
Copiar código
def check_normality(data):
    # Identifica as colunas de distância
    distance_columns = [col for col in data.columns if col.startswith('Distance_SMA') and col.endswith('%')]
    
    if not distance_columns:
        raise ValueError("No distance columns found in the DataFrame. Make sure to calculate distances first.")
    
    normality_results = {}
    
    for col in distance_columns:
        stat, p_value = stats.shapiro(data[col].dropna())
        normality_results[col] = {'Statistic': stat, 'p-value': p_value}
    
    return normality_results
Performs the Shapiro-Wilk test to check if the distances follow a normal distribution.

License
This project is licensed under the MIT License - see the LICENSE file for details.

Contributing
If you have any suggestions or improvements, feel free to create a pull request or open an issue.



