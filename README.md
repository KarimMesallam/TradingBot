# AI Trading Bot with Binance & LLM Integration

A scalable Python project for automated trading that combines traditional trading strategies with modern AI/LLM orchestration for decision making. The bot connects to Binance (starting with paper trading via the Testnet) and is designed to easily integrate multiple LLMs to refine trade signals and manage order execution.

## Overview

This project aims to take you from basic trading bot functionality to a robust system that leverages AI:
- **Paper Trading:** Start with simulated trades using Binance Testnet.
- **Scalable Architecture:** Modular design to support multiple strategies and future integration with various LLMs.
- **AI Orchestration:** A dedicated module to incorporate LLMs (e.g., DeepSeek R1, GPT-4o, o3-mini, Claude 3.7 Sonnet) for enhanced decision making.
- **Extensible:** Easily add new exchanges, trading strategies, and real-time data streams.
- **Advanced Backtesting:** Comprehensive backtesting module with multi-timeframe analysis, strategy optimization, and performance visualization.

## Features

- **Binance API Integration:** Uses the official Binance API (via python-binance) for fetching market data, placing orders, and managing accounts.
- **Paper Trading Mode:** Safely test strategies on Binance Testnet before going live.
- **Strategy Module:** Contains logic for trading signals (e.g., based on technical indicators) with an abstract layer for future enhancements.
- **LLM Manager:** A placeholder module to later integrate multiple language models for trade signal orchestration.
- **Robust Project Structure:** Clean separation of concerns with modules for configuration, API calls, strategy logic, order management, and service orchestration.
- **Testing Suite:** Unit tests using pytest to validate functionality as the project scales.
- **Backtesting System:** Sophisticated backtesting capabilities with:
  - Multi-timeframe analysis for more robust trading decisions
  - Strategy optimization through parameter grid search
  - Comprehensive performance metrics calculation
  - Monitoring and alerting system for detecting poor performance
  - Detailed trade logging for post-trade analysis
  - Visualization of equity curves, drawdowns, and trade performance
  - Strategy comparison across different symbols and timeframes

## Project Structure

```
trading_bot/
├── bot/
│   ├── __init__.py
│   ├── config.py           # Configuration loader (API keys, endpoints, etc.)
│   ├── binance_api.py      # Binance API wrapper (using python-binance)
│   ├── strategy.py         # Abstract trading strategy and sample strategy implementation
│   ├── llm_manager.py      # LLM orchestration placeholder (for decision support)
│   ├── order_manager.py    # Module for order execution and logging
│   ├── database.py         # Database management for storing trading data and analytics
│   ├── backtesting.py      # Advanced backtesting engine for strategy development
│   └── main.py             # Main entry point for running the bot
├── tests/                  # Unit tests (pytest)
│   ├── test_binance_api.py
│   ├── test_strategy.py
│   ├── test_llm_manager.py
│   └── test_backtesting.py
├── examples/               # Example scripts
│   └── backtesting_example.py  # Demonstration of backtesting capabilities
├── data/                   # Directory for storing market data
├── logs/                   # Trading and backtesting logs
├── requirements.txt        # List of dependencies
├── .env                    # Environment variables (API keys, etc.)
└── README.md               # Project documentation (this file)
```

## Installation

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/yourusername/ai-trading-bot.git
   cd ai-trading-bot
   ```

2. **Set Up a Virtual Environment:**

   ```bash
   python3 -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install Dependencies:**

   ```bash
   pip install -r requirements.txt
   ```

4. **Configure Environment Variables:**

   Create a `.env` file in the root folder with your API credentials:
   ```ini
   API_KEY=your_binance_testnet_api_key
   API_SECRET=your_binance_testnet_api_secret
   TESTNET=True
   SYMBOL=BTCUSDT
   ```

## Usage

### Running the Bot

Start the bot (for paper trading) by running either:

```bash
# Method 1: Using the run_bot.py script
./run_bot.py

# Method 2: Using the module directly
python -m bot.main
```

The bot will:
1. Initialize the Binance connection using your API keys
2. Check account balances and verify that the configured symbol can be traded
3. Enter a continuous trading loop that:
   - Retrieves current market data
   - Generates signals from both simple and technical analysis strategies
   - Uses LLM (or rule-based fallback) for decision support
   - Executes trades when signals and LLM decisions align
   - Implements exponential backoff for error handling

All activity is logged to both the console and a file named `trading_bot.log`.

### Backtesting Strategies

The backtesting module allows you to test and optimize trading strategies using historical data:

```python
from bot.backtesting import BacktestEngine

# Create a backtest engine
engine = BacktestEngine(
    symbol='BTCUSDT',
    timeframes=['1h', '4h'],  # Multiple timeframes for analysis
    start_date='2023-01-01',
    end_date='2023-03-31',
    initial_capital=10000,
    commission=0.001  # 0.1% commission
)

# Run the backtest with your strategy
results = engine.run_backtest(your_strategy_function)

# Generate a trade log
engine.generate_trade_log(results, filename="trade_log.csv")

# Plot the results
engine.plot_results(results, filename="backtest_plot.png")
```

#### Multi-Timeframe Analysis

Analyze trading data across multiple timeframes:

```python
# Prepare data with indicators
engine.prepare_data()

# Run multi-timeframe analysis
analysis = engine.multi_timeframe_analysis(engine.market_data)

# Access the consolidated view
bullish_timeframes = analysis['consolidated']['bullish_timeframes']
bearish_timeframes = analysis['consolidated']['bearish_timeframes']
```

#### Strategy Optimization

Find the optimal parameters for your trading strategy:

```python
# Define parameter grid
param_grid = {
    'short_period': [5, 10, 15, 20],
    'long_period': [20, 30, 40, 50]
}

# Run optimization
optimization_results = engine.optimize_parameters(
    strategy_factory=strategy_factory_function,
    param_grid=param_grid
)

# Access the best parameters
best_params = optimization_results['params']
best_sharpe = optimization_results['sharpe_ratio']
```

#### Running Multiple Backtests

Compare different strategies across multiple symbols:

```python
from bot.backtesting import BacktestRunner

# Create a backtest runner
runner = BacktestRunner()

# Run multiple backtests
results = runner.run_multiple_backtests(
    symbols=['BTCUSDT', 'ETHUSDT'],
    timeframes=['1h', '4h'],
    strategies={
        'SMA_Crossover': sma_strategy,
        'RSI': rsi_strategy,
        'Bollinger_Bands': bb_strategy
    },
    start_date='2023-01-01',
    end_date='2023-03-31'
)

# Compare strategies
comparison = runner.compare_strategies()

# Generate a summary report
report = runner.generate_summary_report(output_file='backtest_report.txt')
```

See the `examples/backtesting_example.py` file for comprehensive examples of the backtesting system.

### Running Tests

The project uses pytest for unit testing. To run the tests:

```bash
# Run all tests
python -m pytest

# Run tests with verbose output
python -m pytest -v

# Run tests in a specific file
python -m pytest tests/test_strategy.py

# Run a specific test
python -m pytest tests/test_llm_manager.py::test_make_rule_based_decision

# Generate test coverage report
python -m pytest --cov=bot tests/
```

Test categories:
- API tests: Tests for Binance API interactions
- Strategy tests: Tests for trading strategy logic
- LLM tests: Tests for LLM integration and decision making
- Backtesting tests: Tests for the backtesting engine functionality

## Future Improvements

- **Advanced Trading Strategies:** Implement additional strategies beyond the basic examples.
- **LLM Integration:** Expand `bot/llm_manager.py` to interface with models like DeepSeek R1, GPT-4, etc., for real-time decision support.
- **Live Trading:** After thorough paper trading, switch to live mode (with caution) by toggling configuration parameters.
- **Multi-Exchange Support:** Add wrappers for additional exchanges (e.g., using CCXT) for more diversified trading.
- **Enhanced Logging & Monitoring:** Integrate more robust logging and alerting for operational insights.
- **Reinforcement Learning:** Implement RL-based strategies that can learn and adapt to changing market conditions.
- **Web Interface:** Create a dashboard for monitoring trades, backtesting results, and adjusting bot parameters.

## Contributing

Contributions are welcome! Feel free to fork the repository and submit pull requests. Please follow the standard GitHub flow and include tests for any new functionality.

## License

This project is licensed under the MIT License.