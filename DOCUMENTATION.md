# 📚 Project Documentation - Portfolio Optimization

## 📖 Table of Contents
1. [Data Collection & Preparation](#1-data-collection--preparation)
2. [Price Normalization](#2-price-normalization)
3. [Sharpe Ratio Analysis](#3-sharpe-ratio-analysis)
4. [Risk-Return Profile](#4-risk-return-profile)
5. [Correlation Analysis](#5-correlation-analysis)
6. [Monte Carlo Simulation](#6-monte-carlo-simulation)
7. [Efficient Frontier](#7-efficient-frontier)
8. [Portfolio Optimization](#8-portfolio-optimization)
9. [Final Report](#9-final-report)

---

## 1. Data Collection & Preparation

### Code Block
```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
import yfinance as yf
from datetime import datetime, timedelta

tickers = ['SPY', 'AAPL', 'NVDA', 'TSLA']
prices = yf.download(tickers, start="2020-01-01", end=None)['Close']
log_returns = np.log(prices / prices.shift(1)).dropna()
```

### What does this do?
- **Downloads historical price data** for 4 assets from January 1, 2020 to present
- **Calculates log returns** instead of simple returns for better statistical properties
- **Removes missing values** (first row has NaN because there's no previous day)

### Key Metrics
- **Assets analyzed**: SPY (S&P 500), AAPL (Apple), NVDA (NVIDIA), TSLA (Tesla)
- **Time period**: ~4+ years of daily data
- **Data points**: ~1,200+ trading days per asset

### Why log returns?
Log returns are:
- **Symmetric**: +10% and -10% cancel out mathematically
- **Additive**: Easy to calculate multi-period returns
- **Better for statistical modeling**: More normally distributed

---

## 2. Price Normalization

### Code Block
```python
normalized_prices = (prices / prices.iloc[0]) * 100

plt.figure(figsize=(12, 6))
normalized_prices.plot()
plt.title('Normalized Price Evolution')
plt.ylabel('Base 100')
plt.grid(True)
plt.show()
```

### What does this show?
This graph **normalizes all prices to start at 100** on day 1, making it easy to compare percentage growth across different assets regardless of their absolute price.

### How to interpret:
- **Value at 200**: Asset doubled (+100% return)
- **Value at 350**: Asset grew 3.5x (+250% return)
- **Value at 50**: Asset lost 50% of its value

### Example Results (2020-2024):
| Asset | Final Normalized Value | Total Return |
|-------|----------------------|--------------|
| NVDA  | ~3,200               | +3,100%      |
| TSLA  | ~1,400               | +1300%       |
| AAPL  | ~400                 | +300%        |
| SPY   | ~300                 | +200%        |

**Key Insight**: NVDA had explosive growth, but was it worth the risk? That's what we analyze next.

---

## 3. Sharpe Ratio Analysis

### Code Block
```python
average_daily_return = log_returns.mean()
annual_return = average_daily_return * 252
annual_volatility = log_returns.std() * np.sqrt(252)
sharpe_ratio = annual_return / annual_volatility
```

### What is Sharpe Ratio?
**Sharpe Ratio = Return / Risk**

It measures **how much return you get per unit of risk** taken.

### Formula:
```
Sharpe = (Annual Return) / (Annual Volatility)
```

### Interpretation:
- **Sharpe > 1**: Good risk-adjusted return
- **Sharpe > 2**: Very good
- **Sharpe > 3**: Excellent
- **Sharpe < 1**: You're not being compensated enough for the risk

### Example Results:
| Asset | Annual Return | Annual Volatility | Sharpe Ratio | Rating |
|-------|--------------|-------------------|--------------|---------|
| NVDA  | 58.73%       | 53.25%            | 1.10         | ⭐⭐⭐|
| AAPL  | 22.28%       | 32.01%            | 0.70         | ⭐⭐  |
| TSLA  | 45.34%       | 66.82%            | 0.68         | ⭐     |
| SPY   | 13.68%       | 20.95%            | 0.65         | ⭐     |

**Key Insight**: 
- NVDA has the best Sharpe (1.10) 
- TSLA has the most Volatility (66.82)% 
- **Better Sharpe = More efficient investment**

### Visualization
The bar chart shows:
- **Orange line (y=1)**: Minimum acceptable Sharpe
- **Green line (y=2)**: Excellent Sharpe threshold
- Values above bars show exact Sharpe for each asset

---

## 4. Risk-Return Profile

### Code Block
```python
plt.scatter(annual_volatility * 100, annual_return * 100)
plt.plot(x_line, x_line, 'k--', label='Sharpe = 1')
```

### What does this show?
A **scatter plot** where:
- **X-axis**: Risk (volatility)
- **Y-axis**: Return
- **Diagonal line**: Sharpe Ratio = 1 threshold

### How to interpret:
- **Top-left quadrant**: Best investments (high return, low risk)
- **Bottom-right quadrant**: Worst investments (low return, high risk)
- **Above diagonal line**: Sharpe > 1 (good)
- **Below diagonal line**: Sharpe < 1 (poor risk-adjusted return)

### Example Positions:
```
NVDA: High return, high risk (above diagonal ✓)
AAPL: Medium return, medium risk (below diagonal ✗)
SPY: Low return, low risk (below diagonal✗)
TSLA: High return, VERY high risk (below diagonal ✗)
```

**Key Insight**: Assets in the top-left are ideal. TSLA is too far right (too risky for its return).

---

## 5. Correlation Analysis

### Code Block
```python
correlation_matrix = log_returns.corr()

sns.heatmap(correlation_matrix, annot=True, cmap='RdYlGn')
```

### What is correlation?
Measures **how assets move together**:
- **+1.0**: Perfect positive correlation (move identically)
- **0.0**: No correlation (independent movements)
- **-1.0**: Perfect negative correlation (move oppositely)

### Why does it matter?
**Lower correlation = Better diversification**

If assets are highly correlated, they'll all drop together during market crashes (no protection).

### Example Results:
| Asset Pair | Correlation | Interpretation |
|-----------|-------------|----------------|
| SPY-AAPL  | 0.79        | High (both move together often) |
| SPY-NVDA  | 0.71        | High |
| SPY-TSLA  | 0.54        | Medium (TSLA more independent) |
| AAPL-NVDA | 0.58        | Medium (both tech stocks) |
| APPL-TSLA | 0.50        | Medium (best diversification pair) |
| NVDA-TSLA | 0.48        | Medium
### Heatmap Colors:
- 🟢 **Dark Green** (0.6-1.0): High correlation
- 🟡 **Yellow** (0.3-0.6): Medium correlation
- 🔴 **Red** (<0.3): Low correlation (ideal for diversification)

**Key Insight**: No perfect diversification pairs (no negative correlations), but TSLA provides some independence from tech stocks.

---

## 6. Monte Carlo Simulation

### Code Block
```python
num_portfolios = 10000
for i in range(num_portfolios):
    weights = np.random.random(num_assets)
    weights = weights / np.sum(weights)
    
    portfolio_return = np.sum(weights * mean_returns)
    portfolio_volatility = np.sqrt(np.dot(weights.T, np.dot(cov_matrix, weights)))
    sharpe = portfolio_return / portfolio_volatility
```

### What does this do?
Simulates **10,000 random portfolio combinations** by:
1. Generating random weights (e.g., 30% SPY, 20% AAPL, 40% NVDA, 10% TSLA)
2. Ensuring weights sum to 100%
3. Calculating return, volatility, and Sharpe for each combination

### Why 10,000 portfolios?
Because there are **infinite possible combinations** of weights. Simulating many helps us find patterns and near-optimal solutions.

### Example Random Portfolios:
| Portfolio | SPY | AAPL | NVDA | TSLA | Return | Volatility | Sharpe |
|-----------|-----|------|------|------|--------|------------|--------|
| #1        | 25% | 25%  | 25%  | 25%  | 45.8%  | 32.1%     | 1.43   |
| #2        | 10% | 10%  | 70%  | 10%  | 62.3%  | 41.5%     | 1.50   |
| #3        | 50% | 30%  | 10%  | 10%  | 28.4%  | 22.8%     | 1.25   |

### Key Variables:
- **portfolio_return**: Weighted average of individual returns
- **portfolio_volatility**: Considers both individual volatilities AND correlations (not just average)
- **Covariance matrix**: Captures how assets move together

**Key Insight**: Portfolio volatility is NOT just the average of individual volatilities. Diversification reduces total risk.

---

## 7. Efficient Frontier

### Code Block
```python
plt.scatter(portfolio_volatilities * 100, portfolio_returns * 100, 
            c=sharpe_ratios, cmap='viridis')
plt.colorbar(label='Sharpe Ratio')
```

### What is the Efficient Frontier?
The **curved upper edge** of the scatter plot showing portfolios that offer:
- **Maximum return for a given level of risk**, OR
- **Minimum risk for a given level of return**

### Visual Elements:
- **Purple/blue dots**: Low Sharpe portfolios (inefficient)
- **Yellow dots**: High Sharpe portfolios (efficient)
- **Red star ⭐**: Portfolio with maximum Sharpe Ratio
- **Blue diamond 💎**: Portfolio with minimum volatility
- **Green triangle 🔺**: Portfolio with maximum return
- **Large colored dots**: Individual assets (for comparison)

### Key Portfolios Identified:

#### 🏆 Maximum Sharpe Portfolio (BEST)
```
Composition:
- SPY:  9,2%
- AAPL: 79,6%
- NVDA: 2,1%
- TSLA: 9,1%

Metrics:
- Return: 53,206%
- Volatility: 47,823%
- Sharpe: 1,113
```

#### 🛡️ Minimum Volatility Portfolio (SAFEST)
```
Composition:
- SPY:  0,9%
- AAPL: 0,1%
- NVDA: 93,3%
- TSLA: 5,8%

Metrics:
- Return: 15,615%
- Volatility: 22,105%
- Sharpe: 0.706
```

#### 🚀 Maximum Return Portfolio (RISKIEST)
```
Composition:
- SPY:  2,6%
- AAPL: 95.22%
- NVDA: 0.2%
- TSLA: 8.4%

Metrics:
- Return: 71.2%
- Volatility: 48.9%
- Sharpe: 1.46
```

### How to interpret:
1. **Portfolios below the frontier**: Inefficient (you can get same return with less risk)
2. **Portfolios on the frontier**: Efficient (optimal choices)
3. **Choice depends on your risk tolerance**:
   - Conservative investor → Minimum volatility portfolio
   - Balanced investor → Maximum Sharpe portfolio
   - Aggressive investor → Higher-risk portfolios on the frontier

**Key Insight**: The optimal portfolio (red star) beats ALL individual assets in risk-adjusted returns.

---

## 8. Portfolio Optimization

### Mathematical Optimization vs Monte Carlo

#### Monte Carlo (Simulation):
- Tests 10,000 random combinations
- Finds "near-optimal" solution
- Easy to understand and visualize
- Result: **Sharpe = 1.518**

#### Mathematical Optimization (scipy):
- Uses calculus to find EXACT optimal weights
- Guaranteed to find the true optimum
- More precise but abstract
- Result: **Sharpe = 1.523**

### Comparison Results:
| Method | Sharpe Ratio | Return | Volatility | Difference |
|--------|--------------|--------|------------|------------|
| Monte Carlo | 1.518 | 42.1% | 27.9% | - |
| Mathematical | 1.523 | 42.3% | 27.8% | +0.3% better |

**Key Insight**: Both methods produce very similar results. Monte Carlo is sufficient for practical purposes and easier to understand.

### Optimal Weights (Mathematical):
```
SPY:  25.34%  ← Index fund for stability
AAPL: 29.68%  ← Growth + dividends
NVDA: 34.76%  ← High growth engine
TSLA: 10.22%  ← Speculative component
```

### Why these weights?
- **NVDA (35%)**: Highest weight because it has excellent Sharpe despite volatility
- **AAPL (30%)**: Balanced risk-return profile
- **SPY (25%)**: Provides diversification and stability
- **TSLA (10%)**: Small allocation due to poor Sharpe (high risk, low efficiency)

---

## 9. Final Report

### Complete Summary

#### 📊 Assets Analyzed
**Period**: 2020-01-01 to 2024-11-19  
**Assets**: SPY, AAPL, NVDA, TSLA  
**Data points**: 1,220+ trading days

#### 📈 Individual Performance

| Asset | Total Return | Annualized Volatility | Sharpe Ratio | Ranking |
|-------|--------------|----------------------|--------------|---------|
| NVDA  | +3,400%      | 54.76%               | 1.46         | 🥇 Best Sharpe |
| AAPL  | +150%        | 34.13%               | 1.12         | 🥈 |
| SPY   | +150%        | 20.04%               | 0.95         | 🥉 |
| TSLA  | +450%        | 60.21%               | 0.91         | ⚠️ High risk |

#### 🏆 Optimal Portfolio Performance

**Composition**:
- SPY: 25.34%
- AAPL: 29.68%
- NVDA: 34.76%
- TSLA: 10.22%

**Metrics**:
- Expected Annual Return: **42.3%**
- Annual Volatility: **27.8%**
- Sharpe Ratio: **1.52** ⭐ (beats all individual assets)

**Risk Reduction**: 35% lower volatility compared to investing 100% in NVDA

#### 🎯 Key Findings

1. **Diversification Works**:
   - Optimal portfolio Sharpe (1.52) > Best individual asset Sharpe (1.46)
   - 35% risk reduction without sacrificing returns

2. **Highest Return ≠ Best Investment**:
   - NVDA had 3,400% return but isn't the best standalone choice
   - TSLA had 450% return but worst Sharpe due to excessive volatility

3. **Correlation Matters**:
   - Assets with 0.38-0.65 correlation provide good (not perfect) diversification
   - No negative correlations found (stocks tend to move together)

4. **Optimal Allocation**:
   - Heavy tilt towards NVDA (35%) for growth
   - Balanced with stable SPY (25%) and AAPL (30%)
   - Minimal TSLA (10%) due to poor risk-adjusted returns

#### 💡 Investment Implications

**For a $10,000 investment using optimal portfolio**:
```
SPY:  $2,534  (Stability)
AAPL: $2,968  (Growth + Income)
NVDA: $3,476  (High Growth)
TSLA: $1,022  (Speculation)
```

**Expected outcomes (based on historical data)**:
- 1-year expected return: **+$4,230** (42.3%)
- Annual volatility: ±$2,780 (27.8%)
- Risk-adjusted performance: **Best possible** (Sharpe 1.52)

#### ⚠️ Important Disclaimers

1. **Past performance ≠ Future results**: Historical data doesn't guarantee future returns
2. **Market conditions change**: 2020-2024 was a strong bull market period
3. **Rebalancing needed**: Portfolios drift over time and need periodic adjustment
4. **Transaction costs not included**: Real-world trading has fees and taxes
5. **This is educational**: Not financial advice; consult a professional advisor

---

## 🎓 Conclusions

### What This Project Demonstrates

1. ✅ **Risk-Return Tradeoff**: Higher returns usually come with higher risk
2. ✅ **Sharpe Ratio Importance**: Better metric than raw returns for comparing investments
3. ✅ **Power of Diversification**: Reduces risk without sacrificing returns
4. ✅ **Efficient Frontier Concept**: Visual representation of optimal portfolios
5. ✅ **Monte Carlo Methods**: Practical approach to portfolio optimization
6. ✅ **Mathematical Optimization**: Precise calculation of optimal weights

### Practical Takeaways

- Don't chase the highest returns blindly (TSLA trap)
- Consider risk-adjusted returns (Sharpe Ratio)
- Diversify across assets with varying correlations
- Regular rebalancing maintains optimal allocation
- Use data-driven decisions over gut feelings

### Next Steps for Advanced Analysis

1. **Backtesting**: Test optimal portfolio on out-of-sample data
2. **Rolling optimization**: Recalculate optimal weights quarterly/annually
3. **Risk metrics**: Add VaR, CVaR, Maximum Drawdown
4. **Constraints**: Set realistic limits (e.g., max 40% in any single asset)
5. **Transaction costs**: Model realistic trading expenses
6. **Tax efficiency**: Consider tax implications of rebalancing

---

## 📚 References & Further Reading

- **Modern Portfolio Theory**: Markowitz, H. (1952). "Portfolio Selection"
- **Sharpe Ratio**: Sharpe, W. (1966). "Mutual Fund Performance"
- **Python for Finance**: Hilpisch, Y. - O'Reilly Media
- **Quantitative Finance**: Hull, J. - "Options, Futures, and Other Derivatives"

---

*Last updated: November 2025*  
*For questions or suggestions, please open an issue on GitHub*
