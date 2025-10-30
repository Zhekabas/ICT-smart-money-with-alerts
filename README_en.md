# ICT Concepts — Annotated with MSS Alerts (Pine v5)

**Derivative & educational build.**  
Original indicator by **LuxAlgo** (CC BY-NC-SA 4.0).  
This repo contains my annotated version with sane defaults, MSS alert presets, and usage notes.

## Features
- **Market Structure**: real-time MSS/BOS with bullish/bearish triggers
- **Order Blocks**: detection + breaker state
- **FVG / IFVG & BPR**: auto-drawing with live right-edge updates
- **Liquidity**: buy-/sell-side zones with break state
- **Volume Imbalance (VI)** markers
- **Killzones** backgrounds (NY, London Open/Close, Asia)
- **Fibonacci overlay** across selected objects (FVG/BPR/OB/LIQ/VI/NWOG)

## Alerts
- `MSS Bullish` — early bullish momentum context  
- `MSS Bearish` — potential bearish shift  
**Tip:** use “Once per bar close” to avoid intra-bar noise.

## Quick Start
1. Paste code into TradingView **Pine Editor** → **Add to chart**.  
2. Enable modules you need (OB / FVG / BPR / Liquidity / VI).  
3. Configure **Killzones** sessions if desired (the script handles timezones).  
4. Create alerts on **MSS Bullish/Bearish**.

## Suggested Presets
- **15m**: MSS ON, OB ON, FVG ON (3–4), Liquidity 2, VI 3, Killzones ON.  
- **1h**: MSS ON, OB ON (lookback 10–12), FVG 2–3, BPR ON, Liquidity 2.  
- **4h**: MSS ON, OB ON (lookback 12–14), FVG 2, BPR ON, Liquidity 1–2, VI OFF.

## Screenshots
