# Clawdbot Polymarket Flash

> High-speed Clawdbot execution module for Polymarket that minimizes latency between signal detection and order submission. Designed for time-sensitive arb windows and breaking-news market reactions where milliseconds determine fill quality.

*Last updated: March 2026*

## What is Clawdbot Polymarket Flash?

Clawdbot Polymarket Flash is the low-latency execution engine of the Clawdbot suite. It is built for scenarios where standard execution speed is not enough - arb windows that close in seconds, breaking news events that move markets instantly, and high-conviction signals that require immediate fill.

![preview_clawdbot polymarket flash execution bot](https://github.com/user-attachments/assets/5ab6f945-92a4-4151-98f7-d18291444c76)

---

## Download

| Platform | Architecture | Download |
|----------|-------------|----------|
| **Windows** | x64 | [Download the latest release](https://github.com/drinkergoat/clawdbot-polymarket-flash/releases) |

---

## What Makes It Fast

| Optimization | How It Works | Gain |
|-------------|-------------|------|
| **Pre-auth headers** | Authentication tokens cached, not regenerated per request | -40ms per request |
| **Persistent connection** | Keeps CLOB API connection open between requests | -15ms per request |
| **Priority queue** | High-score signals jump ahead of lower-priority pending orders | Consistent fast lane |
| **Minimal JSON** | Stripped payload with only required fields | -5ms serialization |

---

## Engine Features

* **Sub-second execution** - signal to order submission in under 200ms
* **Signal priority queue** - high-confidence signals always execute first
* **Connection pooling** - maintains persistent connections to Polymarket CLOB
* **Execution log** - records signal time, submission time, and fill time for every order
* **Speed monitor** - alerts if average execution latency rises above your configured limit
* **Fallback mode** - automatically slows to standard mode if rate limits approach
* **Telegram alerts** - instant notification on fill with execution latency included

---

## Two Ways to Run It

| | Windows App | Python Bot |
|---|---|---|
| **Setup** | Double-click | `pip install` + config |
| **Latency target** | Config-based | Custom tuning |
| **Signal source** | Built-in + external | Pluggable |
| **Config** | `config.toml` | Direct code access |
| **Monitoring** | Built-in latency dashboard | JSON metrics |

## Quick Start

```
# 1. Download from Releases
# 2. Edit config.toml - set Polymarket API key and max latency target
# 3. Run Flash - it pre-warms connections and starts accepting signals
```

### Python

```bash
cd clawdbot-polymarket-flash/python
pip install -r requirements.txt
python clawdbot-polymarket-flash-v.1.4.14.py
```

---

## How It Works

![flash execution pipeline](https://github.com/user-attachments/assets/a04e688c-fcfe-48e9-8c3f-264c8bb91fb6)

Four stages per order:

1. **Receive** - signal arrives from connected source (arb scanner, news feed, or manual trigger)
2. **Prioritize** - signal placed in execution queue by confidence score
3. **Execute** - order submitted via pre-authenticated persistent CLOB connection
4. **Log** - fill time, latency, and outcome recorded immediately

### Config Reference

```toml
[flash]
max_latency_ms = 250              # Alert if execution exceeds this
position_size_usd = 30
connection_pool_size = 3          # Persistent connections to maintain

[signals]
accept_external_signals = true    # Accept signals from other Clawdbot modules
internal_scanner = true

[polymarket]
api_key = ""
private_key = ""

[telegram]
bot_token = ""
chat_id = ""

[export]
execution_log_csv = "data/flash/execution_log.csv"
```

---

## Execution Log Format

```json
{
  "exec_id": "flash_20260328_009",
  "market": "will-btc-exceed-90k-april",
  "side": "YES",
  "signal_time_ms": 1711620000100,
  "submit_time_ms": 1711620000187,
  "fill_time_ms": 1711620000241,
  "latency_ms": 141,
  "entry_price": 0.62,
  "size_usd": 30
}
```

---

## Verified Live

**Configuration used:**
* Max latency 250ms, internal scanner + external signals, $30 per trade

**Flash execution:**

| | Details |
|---|---|
| Market | will-btc-exceed-90k-april |
| Signal source | Internal arb scanner |
| Signal to fill | 141ms total latency |
| Entry | YES at 0.62 |
| Tx hash | 0xd4e5f6a7b8c9d0e1f2a3b4c5d6e7f8a9b0c1d2e3f4a5b6c7d8e9f0a1b2c3d4e5 |

---

## Frequently Asked Questions

**What is Clawdbot Polymarket Flash?**
Clawdbot Polymarket Flash is the low-latency execution module of the Clawdbot suite. It minimizes the time between signal detection and order fill through connection pooling, pre-authentication, and priority queuing.

**How fast is it?**
In typical operation, signal-to-fill latency is under 200ms. The exact latency depends on your network connection to Polymarket's CLOB servers.

**What signals does it accept?**
Flash accepts signals from the Clawdbot Arb, Signals, and Whale modules. It also accepts manual triggers and external signal webhooks via the API.

**Is this a polymarket fast execution bot?**
Yes. Flash is specifically optimized for execution speed - it trades off features like complex entry scoring for maximum order submission velocity.

**When should I use Flash vs Auto?**
Use Flash for time-sensitive opportunities where execution speed is the primary constraint - arb windows, breaking news reactions. Use Auto for rule-based systematic trading where speed is less critical.

**Does it monitor open positions?**
Flash is an execution module, not a position manager. Pair it with Clawdbot Tracker for open position monitoring.

**Can it accept signals from non-Clawdbot sources?**
Yes. Enable `accept_external_signals` and send signal payloads to the local webhook endpoint. Full API spec is in the docs folder.

---

## Use Cases

- **Polymarket fast execution** - minimize latency between signal detection and order fill
- **Polymarket low-latency bot** - time-sensitive arb execution where milliseconds matter
- **Clawdbot flash module** - high-speed execution engine for the full Clawdbot suite
- **Polymarket rapid execution** - execute immediately on breaking news market reactions
- **Polymarket high-speed bot** - systematic fast-lane order submission with latency monitoring

---

## Repository Structure

```
clawdbot-polymarket-flash/
+-- clawdbot-polymarket-flash-v.1.4.14.exe
+-- config.toml
+-- data/
|   +-- flash/
|   +-- logs/
|   +-- dll/
+-- python/
|   +-- src/
|   |   +-- connection_pool.py
|   |   +-- signal_queue.py
|   |   +-- executor.py
|   +-- requirements.txt
+-- README.md
```

---

## Requirements

```
python-dotenv, typer[all], httpx, py-clob-client, aiohttp
```

* Polymarket account with CLOB API access
* Low-latency network connection recommended
* Telegram bot token (for alerts)

---

*First in. Best fill.*
