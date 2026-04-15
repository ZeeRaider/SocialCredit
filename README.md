# SocialCredit

SocialCredit is a **Minecraft Paper plugin framework** that introduces a structured, event-driven “social credit” system to your server.

Players gain or lose Social Credit based on behaviour, creating a layer of:
- rewards
- penalties
- surveillance
- and controlled chaos

While the concept is intentionally satirical, the system itself is designed to be **modular, extensible, and developer-friendly**.

---

## Current Release

**Version:** 1.6.4

This release represents a significant evolution from the original 1.0 system.

SocialCredit is now:
- a **stable core framework**
- with a **public API**
- designed for **addon-based expansion**

---

## Core Features

### Social Credit System
- Behaviour-driven score system
- Positive and negative adjustments
- Fully configurable thresholds and effects

### Wanted & Jail Systems
- Automatic wanted-state escalation
- Jail/detention mechanics
- Integration with score and heat

### Heat System
- Tracks player “risk level”
- Configurable thresholds
- Drives inspections, raids, and escalation behaviour

### State Resource Department
- Deposit / withdraw system
- State-controlled economy layer
- Terminal-based interaction model

### Discord Integration
- Webhook support built into core
- No need for external Discord bots

### Debug & Diagnostics
- Full debug report generation
- Designed for support and troubleshooting

### Persistence
- SQLite-backed storage
- Stable and lightweight

---

## API & Addon System (NEW)

SocialCredit is no longer just a plugin — it is a **platform**.

A full public API is now available for addon developers.

### Key API Features

- Score access and mutation
- State credits integration
- Heat system integration
- Terminal discovery
- Webhook reuse
- Event-driven architecture

---

### New in 1.6.3 / 1.6.4

#### Pre-Change Score Interception

**`SocialCreditPreChangeEvent`**

A new event that fires **before a score change is applied**.

This allows addons to:
- inspect score changes
- modify the delta
- modify the reason
- cancel the change entirely

If cancelled:
- no score is applied
- no database write occurs
- no side effects are triggered

This is the **primary extension point** for gameplay modification.

---

### Event Model

SocialCredit now uses a clear two-stage event system:

| Stage | Event | Purpose |
|------|------|--------|
| Pre-change | `SocialCreditPreChangeEvent` | Intercept / modify / cancel |
| Post-change | `SocialCreditChangeEvent` | Observe committed changes |

This ensures:
- full backwards compatibility
- safe extension for addons

---

## Project Direction

SocialCredit is designed as a **core engine**, not a monolithic plugin.

Future features should be built as **separate companion plugins**, for example:

- SocialCreditFarming
- SocialCreditMining
- SocialCreditCivilOrder
- SocialCreditBlackMarket

### Design Philosophy

- Core remains **stable**
- Features expand via **addons**
- API remains **authoritative integration layer**

---

## Installation

1. Place the compiled `.jar` into your `plugins` folder
2. Start or restart the server
3. Allow config files to generate
4. Configure as required

Optional:
- Configure Discord webhook for external logging

---

## Developer Integration

### Getting the API

```java
SocialCreditAPI api = SocialCreditProvider.get();

## Maintainer

SocialCredit is maintained by **ZeeRaider**.

This project is developed as an independent hobby project within the Minecraft server community. Development and distribution are handled under this online pseudonym.

## Community Use

Server owners are free to use the plugin on their servers under the terms of the MIT licence. Feedback and suggestions from the community are always welcome.

## Licence

This project is released under the **MIT License**.

That means you are free to use it, modify it, and distribute it under the terms of that licence.
