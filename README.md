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
In the python script, I tried to predict the support and resistance for AAPL stock from the dates of January 1, 2019 to April 1, 2019.  However, you can change that in lines 51 and 52 of the program.
```python
symbol = 'AAPL'
df = web.DataReader(symbol, 'yahoo', '2019-01-01', '2019-04-01')
```

## How it works
The closing prices of the Bank Of America stock from January 1, 2019 to April 1, 2019 look like this.
<p align="center">
  <img src="https://github.com/lil-zohee/SupportResistance/blob/main/Images/AAPL.png" alt="AAPL 2019-01-01 to 2019-04-01" width="50%" height="50%">
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
  <img src="https://github.com/lil-zohee/SupportResistance/blob/main/Images/AAPL_smooth.png" alt="AAPL smooth" width="50%" height="50%">
</p>
Here's what the two graphs look like in comparison.
<p align="center">
  <img src="https://github.com/lil-zohee/SupportResistance/blob/main/Images/AAPL_normal_smooth.png" alt="AAPL normal smooth" width="50%" height="50%">
</p>

### Step 2: Finding the local maximum and minimum points
To find the local maximum and local minimum, I have created a function, which takes up lines 21 - 49 in the `support_resistance.py`.  Essentially, this function loops through the given `pts` from indexes `1` to `-1`.  If the point is less than the point behind it and less than the point ahead of it, then it is a local minimum.  Similarly, if the point in greater than the point behind it and greater than the point ahead of it, it is a local maximum.  However, if we simply do this, then the algorithm will detect many local minimums and maximums.  Therefore, I have used the Pythagorean theorem to determine the distance between the previous point and current point, as well as the distance between the current point and next point.  I made sure to only consider a point as a local maximum or minimum if the distance between it and the next point was greater than half of the distance between it and the previous point.  Here is the result of plotting the local maximum and minimum points.
<p align="center">
  <img src="https://github.com/lil-zohee/SupportResistance/blob/main/Images/AAPL_locals.png" alt="AAPL locals" width="50%" height="50%">
</p>

### Step 3: Line of best fit between local minimum and maximum
To find the line of best fit between the local minimum and maximum points, I used the `sklean.linear_model.LinearRegression` model.  I have created a function on lines 14 - 19 in the `support_resistance.py`.
```python
def regression_ceof(pts):
    """
    Uses LinearRegression model to determine
    the best fit slope and y-intercept of
    the given points.
    """
    return slope, y_int

local_min_slope, local_min_int = regression_ceof(local_min)
local_max_slope, local_max_int = regression_ceof(local_max)
```

### Step 4: Creating support and resistance lines
Now that we have the slope and y-intercept of the local minimum and maximum, we can easily draw the support and resistance lines using the linear equation `y = mx + b`.  `m` is the slope and `b` is the y-intercept.
```python
support = (local_min_slope * np.array(series.index)) + local_min_int
resistance = (local_max_slope * np.array(series.index)) + local_max_int
```
After plotting the support and resistance lines, we get this.
<p align="center">
  <img src="https://github.com/lil-zohee/SupportResistance/blob/main/Images/AAPL_smooth_sr.png" alt="AAPL smooth sr" width="50%" height="50%">
</p>
This is what it looks like on the normal graph.
<p align="center">
  <img src="https://github.com/lil-zohee/SupportResistance/blob/main/Images/AAPL_sr.png" alt="AAPL sr" width="50%" height="50%">
</p>
