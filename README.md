# 📊 Portfolio Optimization - Quantitative Finance

## 🎯 Overview
My first quantitative finance project. I analyze 4 assets (SPY, AAPL, NVDA, TSLA) and find the optimal portfolio using Markowitz's Modern Portfolio Theory.

## 🔍 What does this project do?
1. ✅ Downloads historical price data from 2020 to present
2. ✅ Calculates log returns, volatility, and Sharpe Ratio
3. ✅ Analyzes correlations between assets
4. ✅ Simulates 10,000 random portfolios (Monte Carlo simulation)
5. ✅ Identifies the optimal portfolio with the best Sharpe Ratio

## 📈 Key Results
- **NVDA** had the highest growth (35x) but with high volatility (55%)
- **SPY** grew "only" 2.5x but with low volatility (20%)
- **Optimal portfolio** achieved a better Sharpe Ratio than any individual asset
- **Diversification** significantly reduced risk

## 🛠️ Technologies Used
![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)
![Pandas](https://img.shields.io/badge/Pandas-Latest-green.svg)
![NumPy](https://img.shields.io/badge/NumPy-Latest-orange.svg)

- **Python 3.x**
- **pandas** - Data manipulation
- **numpy** - Numerical calculations
- **matplotlib/seaborn** - Data visualization
- **yfinance** - Real-time financial data

## 🚀 How to Run
### Option 1: Google Colab (Recommended)
1. Open the `portfolio_analysis.ipynb` notebook in this repository
2. Click on "Open in Colab"
3. Run all cells (Runtime → Run all)

### Option 2: Local Environment
```bash
# Install dependencies
pip install pandas numpy matplotlib seaborn yfinance
# Run the notebook
jupyter notebook portfolio_analysis.ipynb
```

## 📚 Concepts Learned

- ✅ Log returns vs simple returns
- ✅ Volatility as a measure of risk
- ✅ Sharpe Ratio (risk-adjusted return)
- ✅ Correlation and covariance matrix
- ✅ Portfolio diversification
- ✅ Markowitz's Efficient Frontier
- ✅ Monte Carlo simulation

## 🎓 Next Steps

- [ ] Implement strategy backtesting
- [ ] Add mathematical optimization with scipy
- [ ] Calculate VaR (Value at Risk)
- [ ] Create interactive dashboard

## 👨‍💻 Author

**[Diego García Margozzini]**  
First Quantitative Finance Project  
November 2024

## 📄 License

This project is open source under the MIT License.
