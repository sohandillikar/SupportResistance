# SupportResistance
A python script that estimates the support and resistance lines of a stock's prices or a period
## Prerequisites
1. Have `Python3` and `pip` installed on PC.  (Install from https://python.org)
2. Have `git` installed.  (Install from https://git-scm.com/downloads)

## Setup
1. `git clone` the repository
```sh
git clone https://github.com/lil-zohee/SupportResistance.git
```
2. Move in the new directory
```sh
cd SupportResistance/
```
3. Install all necessary modules
```sh
pip3 install -r requirements.txt
```
4. Now you can run the program
```sh
python3 support_resistance.py
```
In the python script, I tried to predict the support and resistance for BAC stock from the dates of January 1, 2015 to July 1, 2015.  However, you can change that in lines 51 and 52 of the program.
```python
symbol = 'BAC'
df = web.DataReader(symbol, 'yahoo', '2015-01-01', '2015-07-01')
```

## How it works
The closing prices of the Bank Of America stock from January 1, 2015 to July 1, 2015 look like this.
<p align="center">
  <img src="https://github.com/lil-zohee/SupportResistance/blob/main/Images/BAC.png" alt="BAC 2015-01-01 to 2015-07-01" width="50%" height="50%">
</p>

### Step 1: Smoothen the graph
We can use the `savgol_filter` function from the `scipy.signal` module, to make the graph smoother.  I have created a simple algorithm to determine how much we should smoothen a graph.  All the closing prices of a stock are stored in a `pd.Series` object.  To determine the level of smoothness we want in our graph, we have to find the difference in months of the 2 dates.  Then we multiply it with 2 and add it with 3.
```python
month_diff = series.shape[0] // 30
if month_diff == 0: # If we have 0 months worth of prices, edit the variable to = 1
    month_diff = 1
smooth = int(2 * month_diff + 3) # Level of smoothness in our graph
```
Now lets smoothen our graph
```python
pts = savgol_filter(series, smooth, 3) # 3 is the order of the polynomials
```
Here's the result of plotting `pts`.
<p align="center">
  <img src="https://github.com/lil-zohee/SupportResistance/blob/main/Images/BAC_smooth.png" alt="BAC smooth" width="50%" height="50%">
</p>
