# ICT Smart Money Concepts with Alerts 🎯

TradingView indicator based on ICT (Inner Circle Trader) Smart Money Concepts with comprehensive alert system. Built on LuxAlgo's ICT Concepts framework.

## Features

### Market Structure
- **MSS (Market Structure Shift)** — Major trend reversals
- **BOS (Break of Structure)** — Continuation patterns
- Customizable swing length (3-10 bars)
- Bullish/bearish color coding

### Order Blocks
- **Bullish Order Blocks** — Institutional buying zones
- **Bearish Order Blocks** — Institutional selling zones
- Swing lookback customization
- Last N order blocks display
- Candle body/wick options

### Volume Imbalance (FVG)
- **Fair Value Gaps** detection
- Customizable visibility (last N gaps)
- Visual color coding

### Displacement
- High-momentum move detection
- Filters out noise in ranging markets

### Premium/Discount Zones
- **Equilibrium** — 50% retracement level
- **Premium** — Above 50% (resistance zone)
- **Discount** — Below 50% (support zone)

### Sessions
- **New York Open/Close**
- **London Open/Close**
- **Asian Session**
- Customizable time zones

## Alert System

Comprehensive alerts for:
- MSS Bullish/Bearish
- BOS Bullish/Bearish  
- Order Block creation
- FVG detection
- Displacement events
- Premium/Discount zone entries

## Installation

1. Copy code from `src/ict_concepts_alerts.pine`
2. Open TradingView Pine Editor
3. Paste and click "Add to chart"
4. Configure settings and alerts

## Usage

### Day Trading (5m-15m)
- Focus on Order Blocks + FVG
- Use session lines for timing
- MSS for reversal confirmation

### Swing Trading (1H-4H)
- Premium/Discount zones for entries
- MSS for trend direction
- Order Blocks for support/resistance

### Position Trading (Daily)
- Monthly/weekly MSS for major trend
- Order Blocks for entry zones
- Displacement for momentum confirmation

## Settings

### Market Structure
| Parameter | Default | Description |
|-----------|---------|-------------|
| Mode | Present | Present or Historical |
| Length | 5 | Swing lookback (3-10) |
| Show MSS | ✓ | Market Structure Shift |
| Show BOS | ✓ | Break of Structure |

### Order Blocks
| Parameter | Default | Description |
|-----------|---------|-------------|
| Swing Lookback | 10 | Detection sensitivity |
| Last Bullish OB | 1 | How many to display |
| Last Bearish OB | 1 | How many to display |
| Use Candle Body | ✓ | Body vs full range |

### FVG
| Parameter | Default | Description |
|-----------|---------|-------------|
| # Visible VI's | 2 | Last N gaps shown |

## Credits

Based on **LuxAlgo's ICT Concepts** indicator with added alert functionality.

## Resources

- [ICT Official YouTube](https://www.youtube.com/c/InnerCircleTrader)
- [LuxAlgo](https://www.luxalgo.com/)
- [Smart Money Concepts Guide](https://www.tradingview.com/script/YFhTEKwE-ICT-Concepts-LuxAlgo/)

## Technical

- **Pine Script:** v5
- **Type:** Indicator (overlay=true)
- **Max objects:** 500 lines/boxes/labels each

## License

Original LuxAlgo code © LuxAlgo  
Alert modifications © 2025

## Contributing

Bug reports and improvements welcome via issues/PRs.

---

**Status:** Stable  
**Last Updated:** 2026-02-19
