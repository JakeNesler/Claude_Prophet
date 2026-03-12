# Prophet Trader


THIS IS NOW DEPRECATED, please refer to https://github.com/JakeNesler/OpenProphet?tab=readme-ov-file

OpenProphet is a fully agentic harness running OpenCode. It makes it easier to access remotely via web ui and hearbeat system similar to openclaw. 

This repo still works fine for an mcp, but all updates to the prophet mcp will now be inside of OpenProphet. 


























**AI-powered autonomous options trading system with MCP integration**

> ⚠️ **WARNING:** This is an experimental AI-powered trading system. Trading options involves significant risk of loss. **I strongly recommend using this for paper trading only.** Do not use real money. The author provides no warranties and assumes no responsibility for financial losses.

---

## Overview

Prophet Trader is an **aggressive discretionary options trading system** that combines:
- **Multi-timeframe options** - LEAPS (60-90+ DTE) + Scalping (0-5 DTE) + Active hedging
- **Trades both directions** - Calls on rallies, puts on selloffs, not married to one side
- **Pattern Day Trader status** - Unlimited day trades with $25K+ equity
- **MCP server integration** - Full Claude Code control via Model Context Protocol
- **AI agents** - CEO, Strategy, Consultant, Engineer for analysis and pressure-testing
- **Vector similarity search** - AI memory of past trades for pattern recognition
- **Real-time logging** - All decisions and activity tracked for review

---

## Architecture

```
Prophet Trader
├── cmd/bot/main.go              # Entry point (Go backend)
├── controllers/                  # HTTP handlers (48 functions)
│   ├── activity_controller.go   # Activity logging endpoints
│   ├── intelligence_controller.go # News & analysis endpoints
│   ├── news_controller.go       # News aggregation
│   ├── order_controller.go      # Trading operations
│   └── position_management.go   # Managed positions
├── services/                     # Business logic (63 functions)
│   ├── activity_logger.go       # Trade journaling
│   ├── alpaca_data.go           # Market data service
│   ├── alpaca_options_data.go   # Options chain data
│   ├── alpaca_trading.go        # Order execution
│   ├── gemini_service.go        # AI news cleaning
│   ├── news_service.go          # News aggregation
│   ├── position_manager.go      # Stop-loss/take-profit automation
│   ├── stock_analysis.go        # Technical analysis
│   └── technical_analysis.go    # RSI, MACD, momentum
├── database/                     # SQLite storage (15 functions)
│   └── storage.go               # Bars, orders, positions, embeddings
├── models/                       # Data entities (7 types)
│   └── models.go                # DB models
├── interfaces/                   # Type definitions (80 types)
│   ├── trading.go               # Order, Position, Account
│   └── options.go               # OptionContract, OptionChain
├── config/                       # Configuration
│   └── config.go                # Environment loading
├── mcp-server.js                 # MCP server (Claude Code integration)
├── .claude/agents/               # AI agent definitions
├── activity_logs/                # Daily trading journals
├── decisive_actions/             # Trade decision logs
└── data/prophet_trader.db        # SQLite database
```

### System Stats

| Metric | Value |
|--------|-------|
| Language | Go (net/http) |
| Packages | 8 |
| Functions | 152 |
| Types | 80 |
| Lines of Code | 3,623 |
| Avg Complexity | 3.3 |

---

## How It Works

### 1. MCP Server Layer (`mcp-server.js`)

The MCP (Model Context Protocol) server acts as a bridge between Claude Code and the Go trading backend:

```
Claude Code <---> MCP Server (Node.js) <---> Go Backend <---> Alpaca API
```

- Exposes 40+ trading tools to Claude Code
- Handles request/response formatting
- Manages authentication and rate limiting
- Runs on port 4534 by default

### 2. Go Backend (`cmd/bot/main.go`)

The trading bot backend handles:
- **Order Execution** - PlaceOrder, CancelOrder via Alpaca API
- **Market Data** - Real-time quotes, historical bars, options chains
- **Position Management** - Automated stop-loss/take-profit monitoring
- **News Aggregation** - Google News + MarketWatch feeds
- **AI Integration** - Gemini for news cleaning/summarization
- **Activity Logging** - Trade journals and decision logs

### 3. Services Architecture

| Service | Purpose | Key Functions |
|---------|---------|---------------|
| `AlpacaTradingService` | Order execution | PlaceOrder, CancelOrder, GetPositions |
| `AlpacaDataService` | Market data | GetHistoricalBars, GetLatestQuote |
| `AlpacaOptionsDataService` | Options data | GetOptionChain, GetOptionSnapshot |
| `PositionManager` | Automation | MonitorPositions, CloseManagedPosition |
| `StockAnalysisService` | Analysis | AnalyzeStock, GetTechnicalAnalysis |
| `NewsService` | Intelligence | GetCleanedNews, AggregateNews |
| `GeminiService` | AI processing | CleanNewsForTrading |
| `ActivityLogger` | Journaling | LogDecision, LogActivity |

### 4. Data Flow

```
1. User Request (Claude Code)
   ↓
2. MCP Tool Call (e.g., place_options_order)
   ↓
3. HTTP Request to Go Backend
   ↓
4. Controller (OrderController.PlaceOptionsOrder)
   ↓
5. Service (AlpacaTradingService.PlaceOrder)
   ↓
6. Alpaca API (live/paper trading)
   ↓
7. Response back through chain
   ↓
8. Result displayed in Claude Code
```

---

## Quick Start

### 1. Environment Setup

Create `.env` file:
```bash
ALPACA_PUBLIC_KEY=your_public_key
ALPACA_SECRET_KEY=your_secret_key
ALPACA_ENDPOINT= # Start with paper trading
GEMINI_API_KEY=your_gemini_key  # For AI news cleaning

```

### 2. Build & Run Go Backend

```bash
# Build the bot
go build -o prophet_bot ./cmd/bot

# Run the bot
./prophet_bot
```

### 3. Start MCP Server

The MCP server runs automatically when Claude Code starts via `.mcp.json` configuration.

### 4. Start Trading

Open Claude Code and use MCP tools:
```
get_account           # Check portfolio status
get_options_positions # Review open positions
get_quick_market_intelligence  # Get market news
analyze_stocks        # Technical analysis
place_options_order   # Execute trades
```

---

## MCP Tools Reference

### Trading Operations

| Tool | Description |
|------|-------------|
| `place_options_order` | Buy/sell options contracts |
| `place_managed_position` | Position with auto stop-loss/take-profit |
| `close_managed_position` | Close managed position at market |
| `cancel_order` | Cancel pending order |
| `place_buy_order` | Buy stock (not used - options only) |
| `place_sell_order` | Sell stock (not used - options only) |

### Market Data

| Tool | Description |
|------|-------------|
| `get_account` | Portfolio value, cash, buying power |
| `get_options_positions` | All open options positions |
| `get_options_position` | Single option position details |
| `get_options_chain` | Available contracts for underlying |
| `get_orders` | Order history |
| `get_quote` | Real-time stock quote |
| `get_latest_bar` | Latest OHLCV bar |
| `get_historical_bars` | Historical price data |
| `get_managed_positions` | All managed positions with status |

### Intelligence

| Tool | Description |
|------|-------------|
| `get_quick_market_intelligence` | AI-cleaned MarketWatch news (fast) |
| `analyze_stocks` | Technical analysis + news + recommendations |
| `search_news` | Google News search by keyword |
| `get_cleaned_news` | Aggregated news from multiple sources |
| `get_marketwatch_topstories` | MarketWatch top stories |
| `get_marketwatch_realtime` | Real-time headlines |
| `get_marketwatch_bulletins` | Breaking news |
| `get_marketwatch_marketpulse` | Quick market updates |

### Vector Search (AI Memory)

| Tool | Description |
|------|-------------|
| `find_similar_setups` | Find past trades similar to current setup |
| `store_trade_setup` | Store completed trade for future reference |
| `get_trade_stats` | Win rate, profit factor by filters |

### Logging

| Tool | Description |
|------|-------------|
| `log_decision` | Log trading decision with reasoning |
| `log_activity` | Log activity to daily journal |
| `get_activity_log` | Get today's activity log |

### Utilities

| Tool | Description |
|------|-------------|
| `wait` | Pause execution (max 300 seconds) |
| `get_datetime` | Current time in US Eastern timezone |

---

## AI News Intelligence (Gemini)

The system uses Google Gemini to transform raw news feeds into actionable trading intelligence.

### What It Does

- **Cleans noisy RSS feeds** into structured summaries
- **Extracts key themes** and sentiment by sector
- **Identifies trading-relevant catalysts** (earnings, FDA decisions, macro events)
- **Reduces token usage by 80-90%** compared to raw news

### Tools That Use It

| Tool | Description |
|------|-------------|
| `get_quick_market_intelligence` | Fast 15-article MarketWatch summary |
| `get_cleaned_news` | Multi-source aggregated intelligence |
| `analyze_stocks` | Includes news context per ticker |
| `aggregate_and_summarize_news` | Custom topic/symbol aggregation |

### Example Output

Raw news: 50+ noisy articles with ads, duplicates, irrelevant content

Cleaned output:
```json
{
  "summary": "Tech rallying on NVDA earnings beat...",
  "key_themes": ["AI infrastructure spending", "Fed pause expectations"],
  "sentiment_by_sector": {"technology": "bullish", "energy": "neutral"},
  "actionable_tickers": ["NVDA", "AMD", "SMCI"]
}
```

### Configuration

Set `GEMINI_API_KEY` in your `.env` file:
```bash
GEMINI_API_KEY=your_gemini_api_key
```

**Optional:** If no key is set, intelligence tools return raw news instead of AI-cleaned summaries.

---

## Trading Strategy

### Core Approach: Multi-Timeframe Options Trading

The system trades **both directions** (calls AND puts) based on market conditions. Not married to bullish or bearish - if the market dumps, buy puts; if it bounces, flip to calls.

**1. LEAPS Foundation (60-90+ DTE)**
- Hold longer-dated calls on high-conviction names (tech, broad market ETFs)
- These ride broader trends and survive short-term volatility
- Provides baseline exposure while scalps generate active returns

**2. Intraday Scalping (0-5 DTE)**
- Trade both directions based on momentum
- Quick entries, quick exits, tight stops
- Capture overnight gaps and intraday swings

**3. Active Hedging**
- Hold protective puts against bullish exposure
- When market sells off, hedges cushion losses
- Provides flexibility to wait for bounce opportunities

**4. Disciplined Exits**
- Take profits aggressively on winners (+25-50%)
- Cut losers fast (-15% or thesis break)
- Never average down without regime confirmation

**5. Capital Preservation Mode**
- Maintain 50-70% cash at all times
- Scale down when volatility spikes or market gets choppy
- Fed weeks / major events = smaller positions, more hedges

### Risk Management

| Rule | Value |
|------|-------|
| Max position size | 15% of portfolio |
| Max total deployment | 50% of portfolio |
| Stop loss | -15% or thesis break |
| Daily loss limit | -5% triggers halt |
| Cash reserve | 50-70% at all times |

---

## AI Agents

Located in `.claude/agents/`:

### CEO Agent (`paragon-trading-ceo`)
- Portfolio-level risk assessment
- Capital allocation decisions
- Behavioral pattern recognition
- Use before deploying >$20K

### Strategy Agent (`stratagem-options-scalper`)
- Technical analysis and setups
- Entry/exit price recommendations
- Risk/reward calculations
- Use for finding trade setups

### Consultant Agent (`daedalus-intelligence-director`)
- Adversarial thinking
- Blind spot identification
- Pressure-testing trade thesis
- Use when uncertain or emotional

### Engineer Agent (`forge-go-engineer`)
- Go code development
- Trading infrastructure
- Tool building
- Use for system improvements

---

## Vector Search (AI Memory)

The system includes semantic search for finding similar historical trades:

### How It Works

1. Trade decisions in `decisive_actions/` are embedded using local AI model
2. 384-dimensional vectors stored in SQLite (`data/prophet_trader.db`)
3. AI queries past trades using natural language

### Example Queries

```javascript
// Find similar SPY scalp setups
find_similar_setups("SPY gap up scalp for quick profit")

// Find similar stop-loss decisions
find_similar_setups("cut loss fast to preserve capital")

// Find profit-taking patterns
find_similar_setups("take profit on winning position")
```

### Benefits

- AI learns from experience
- Pattern recognition across different wording
- Local model (no API costs)
- Automatic updates via `store_trade_setup`

### Seeding Your Own Knowledge

You can pre-populate the vector database with your own trading principles or example trades. Create a JSON file with entries like:

```json
[
  {
    "symbol": "GENERAL",
    "action": "KNOWLEDGE",
    "strategy": "RISK_MANAGEMENT",
    "reasoning": "Risk 1-2% of capital per trade maximum. Position sizing is more important than entry price.",
    "market_context": "Survive to trade another day."
  },
  {
    "symbol": "NVDA",
    "action": "BUY",
    "strategy": "SWING",
    "result_pct": 42.3,
    "result_dollars": 3180,
    "reasoning": "Bought 30-45 DTE calls with delta 0.35 on pullback to VWAP after earnings beat.",
    "market_context": "Tech sector leading, VIX at 14, AI narrative strengthening."
  }
]
```

Then use the `store_trade_setup` MCP tool to load each entry, or create a simple script to batch import them.

---

## File Structure

### Logs & Decisions

```
activity_logs/
├── activity_2025-11-17.json
├── activity_2025-11-18.json
└── ...

decisive_actions/
├── 2025-12-19T16-50-25-067Z_SELL_SPY251219C00681000.json
├── 2025-12-19T16-44-32-129Z_BUY_SPY251219C00681000.json
└── ...
```

### Database

```
data/prophet_trader.db
├── db_orders          # Order history
├── db_bars            # Price data cache
├── db_positions       # Position snapshots
├── db_managed_positions # Managed position state
├── trade_embeddings   # Trade metadata
└── trade_vectors      # 384-dim embeddings
```

---

## Daily Trading Workflow

### Pre-Market (Before 9:30 AM ET)
1. `get_account` - Check portfolio status
2. `get_options_positions` - Review open positions
3. `get_quick_market_intelligence` - Get market news
4. `analyze_stocks` - Technical analysis on watchlist
5. Consult CEO agent for risk assessment (optional)

### Market Hours (9:30 AM - 4:00 PM ET)
1. Monitor for setups (breakouts, pullbacks, momentum)
2. Execute trades with limit orders: `place_options_order`
3. Log decisions: `log_decision`
4. Manage positions: Use `wait` tool to check in time blocks
5. Cut losers at -15%, take profits at +25-50%

### End of Day (3:50 PM ET)
1. `get_options_positions` - Review all positions
2. Close <3 DTE scalps before close
3. Decide: Hold overnight or close swings?
4. `log_activity` - Log daily activity

### Weekly Review
1. Review all trades from the week
2. Calculate win rate, profit factor
3. Identify patterns and mistakes
4. Update rules based on results
5. Consult CEO + Consultant agents

---

## Key Functions (High Complexity)

| Function | Complexity | Purpose |
|----------|------------|---------|
| `CloseManagedPosition` | 15 | Close position with cleanup |
| `LogPositionClosed` | 8 | Log trade with P&L calculation |
| `AnalyzeStock` | 8 | Full technical + news analysis |
| `GetOptionsChain` | 7 | Fetch and filter options |
| `CleanNewsForTrading` | 7 | AI-powered news summarization |

---

## Development

### Adding New MCP Tools

1. Add endpoint in Go backend (`controllers/`)
2. Add route in `setupRouter()` (`cmd/bot/main.go`)
3. Add tool handler in `mcp-server.js`
4. Test with Claude Code

---

## Production Considerations

### Risk Management
- Always use **limit orders** (never market orders on options)
- Start with **paper trading** (`ALPACA_PAPER=true`)
- Test rules thoroughly before live capital
- Monitor **transaction costs** (<5% of gross profit)

### Monitoring
- Use the `wait` tool to let the AI check positions in time blocks
- Review `decisive_actions/` daily
- Weekly review `activity_logs/`
- Track win rate and profit factor monthly

### Governance
- Rules are guidelines, not hard constraints
- Update rules based on results, not aspirations
- Agents are advisory, not required
- Success = profitable trading

---

## Disclaimer

THIS SOFTWARE IS PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND. The author strongly recommends against using this system with real money. Options trading carries substantial risk of loss. Past performance (including any example trades in this repository) does not guarantee future results. You are solely responsible for your own trading decisions and any resulting gains or losses.

---

## License

MIT License - Free to use, modify, and distribute with attribution.

See [LICENSE](LICENSE) for details.

---

Built by [cartogopher.com](https://cartogopher.com) code intelligence tool.

**Happy Trading!**
