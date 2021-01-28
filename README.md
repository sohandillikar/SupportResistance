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
![BAC 2015-01-01 to 2015-07-01](https://github.com/lil-zohee/SupportResistance/blob/main/Images/BAC.png =250x250)
<img src="https://github.com/lil-zohee/SupportResistance/blob/main/Images/BAC.png" alt="BAC 2015-01-01 to 2015-07-01" width="50%" height="50%">
