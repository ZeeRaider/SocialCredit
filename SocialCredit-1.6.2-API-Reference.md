# SocialCredit 1.6.2 Core API Reference

This file is for addon developers building against **SocialCredit 1.6.2 core**.

It documents the **public API surface only**. Addons should use the API/provider/events below and **must not** depend on internal services or touch the plugin database directly.

---

## Compatibility and addon rules

- **Target plugin version:** SocialCredit **1.6.2**
- **Java target:** **21**
- **Paper API target:** **1.20.4-R0.1-SNAPSHOT**
- **Plugin coordinates (from core POM):**
  - `groupId`: `com.example`
  - `artifactId`: `socialcredit`
  - `version`: `1.6.2`
- Addons should depend on the **public API only**:
  - `com.example.socialcredit.api.*`
  - `com.example.socialcredit.api.event.*`
  - `com.example.socialcredit.api.model.*`
  - `com.example.socialcredit.api.result.*`
- Do **not** use internal classes such as services, managers, repositories, listeners, or database code.
- Do **not** read or write SocialCredit's SQLite data directly.
- If your addon introduces a new gameplay/state system that other plugins may need to integrate with, mirror the same rule used by core: expose API support in the same release as the feature.

---

## How to obtain the API

Use the provider class at runtime.

```java
import com.example.socialcredit.api.SocialCreditAPI;
import com.example.socialcredit.api.SocialCreditProvider;

SocialCreditAPI api = SocialCreditProvider.get();
if (api == null) {
    // SocialCredit not loaded / not available
    return;
}
```

Safer form:

```java
if (!SocialCreditProvider.isAvailable()) {
    return;
}
SocialCreditAPI api = SocialCreditProvider.get();
```

### Provider class

`com.example.socialcredit.api.SocialCreditProvider`

Methods:
- `register(SocialCreditAPI instance)`
- `get()`
- `isAvailable()`
- `unregister()`

Addon developers should normally only use:
- `SocialCreditProvider.get()`
- `SocialCreditProvider.isAvailable()`

---

## Public API interface

`com.example.socialcredit.api.SocialCreditAPI`

### Score methods

#### `int getScore(UUID playerId, String playerName)`
Returns the current Social Credit score for the player.

#### `ScoreChangeResult addScore(UUID playerId, String playerName, int amount, String reason)`
Adds or removes score by delta.

Use negative `amount` to reduce score.

#### `ScoreChangeResult setScore(UUID playerId, String playerName, int newScore, String reason)`
Sets the player's score directly.

---

### Wanted / jail state

#### `boolean isWanted(UUID playerId, String playerName)`
Returns whether the player is considered wanted by the core system.

#### `boolean isJailed(UUID playerId, String playerName)`
Returns whether the player is currently jailed/detained.

#### `boolean isPlayerWanted(UUID playerId)`
A UUID-only wanted-state helper.

---

### State Credits API

#### `int getStateCredits(UUID playerId, String playerName)`
Returns the player's State Credits balance.

#### `boolean addStateCredits(UUID playerId, String playerName, int amount, String reason)`
Adds State Credits.

#### `boolean removeStateCredits(UUID playerId, String playerName, int amount, String reason)`
Removes State Credits.

#### `boolean transferStateCredits(UUID fromPlayerId, String fromPlayerName, UUID toPlayerId, String toPlayerName, int amount, String reason)`
Transfers State Credits from one player to another.

---

### State stock / State Department API

#### `int getStateStock(Material material)`
Returns current state stock for a material.

#### `StateTransactionResult depositToState(UUID playerId, String playerName, Material material, int amount)`
Deposits items/material into the state stockpile and returns a transaction result.

#### `StateTransactionResult withdrawFromState(UUID playerId, String playerName, Material material, int amount)`
Withdraws items/material from the state stockpile and returns a transaction result.

#### `List<StateTerminalInfo> getStateTerminals()`
Returns registered state terminal locations.

---

### Heat API

#### `double getHeat(UUID playerId)`
Returns the player's current Heat.

#### `double setHeat(UUID playerId, double value)`
Sets Heat directly and returns the resulting stored Heat value.

#### `double addHeat(UUID playerId, double amount)`
Adds Heat and returns the resulting stored Heat value.

#### `double reduceHeat(UUID playerId, double amount)`
Reduces Heat and returns the resulting stored Heat value.

#### `String getHeatBand(UUID playerId)`
Returns the current Heat band label for the player.

### Heat notes for 1.6.2

In the 1.6.2 core build, Heat is configured with a maximum of **100** in config. Addons should assume Heat is a bounded gameplay value and should not rely on values above the configured cap.

---

### Aggregated player state

#### `PlayerSocialState getPlayerState(UUID playerId, String playerName)`
Returns a summary record containing:
- score
- wanted state
- jailed state
- state credits
- rank
- heat
- heat band
- raid eligibility

Useful when you want one read call instead of multiple separate calls.

---

### Debug / webhook API

#### `DebugReportResult generateDebugReport()`
Generates the plugin debug report and returns metadata about the result.

#### `boolean postDiscordWebhook(String message)`
Posts a message through SocialCredit's configured Discord webhook path.

Use this if your addon wants to reuse the core plugin's webhook instead of shipping a separate Discord transport.

---

## Result records

### `ScoreChangeResult`
Package: `com.example.socialcredit.api.result`

Fields:
- `boolean success`
- `int oldScore`
- `int newScore`
- `int delta`
- `String reason`
- `String message`

Typical use:

```java
ScoreChangeResult result = api.addScore(playerId, playerName, -5, "Contraband handling");
if (result.success()) {
    int updated = result.newScore();
}
```

---

### `StateTransactionResult`
Package: `com.example.socialcredit.api.result`

Fields:
- `boolean success`
- `Material material`
- `int amount`
- `int creditsChanged`
- `int stockAfter`
- `String message`

Typical use:

```java
StateTransactionResult tx = api.depositToState(playerId, playerName, Material.IRON_INGOT, 16);
if (tx.success()) {
    int creditsAwarded = tx.creditsChanged();
}
```

---

### `DebugReportResult`
Package: `com.example.socialcredit.api.result`

Fields:
- `boolean success`
- `String filePath`
- `String summary`

Typical use:

```java
DebugReportResult report = api.generateDebugReport();
if (report.success()) {
    getLogger().info("Debug report: " + report.filePath());
}
```

---

## Model records

### `PlayerSocialState`
Package: `com.example.socialcredit.api.model`

Fields:
- `int score`
- `boolean wanted`
- `boolean jailed`
- `int stateCredits`
- `String rank`
- `double heat`
- `String heatBand`
- `boolean raidEligible`

Typical use:

```java
PlayerSocialState state = api.getPlayerState(playerId, playerName);
if (state.wanted()) {
    // react to wanted state
}
if (state.heat() >= 80.0D) {
    // high-heat behavior
}
```

---

### `StateTerminalInfo`
Package: `com.example.socialcredit.api.model`

Fields:
- `String world`
- `int x`
- `int y`
- `int z`

Typical use:

```java
for (StateTerminalInfo terminal : api.getStateTerminals()) {
    getLogger().info(terminal.world() + " @ " + terminal.x() + "," + terminal.y() + "," + terminal.z());
}
```

---

## Events

These are the safest way for addons to react to SocialCredit state changes without polling.

### `SocialCreditChangeEvent`
Package: `com.example.socialcredit.api.event`

Fires when a player's Social Credit score changes.

Methods:
- `Player getPlayer()`
- `int getOldScore()`
- `int getNewScore()`
- `int getDelta()`
- `String getReason()`

Example listener:

```java
import com.example.socialcredit.api.event.SocialCreditChangeEvent;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;

public class ExampleListener implements Listener {

    @EventHandler
    public void onSocialCreditChange(SocialCreditChangeEvent event) {
        if (event.getDelta() < 0) {
            // react to score loss
        }
    }
}
```

---

### `SocialCreditHeatChangeEvent`
Package: `com.example.socialcredit.api.event`

Fires when Heat changes.

Methods:
- `UUID getPlayerId()`
- `double getOldHeat()`
- `double getNewHeat()`

Example listener:

```java
import com.example.socialcredit.api.event.SocialCreditHeatChangeEvent;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;

public class ExampleHeatListener implements Listener {

    @EventHandler
    public void onHeatChange(SocialCreditHeatChangeEvent event) {
        if (event.getNewHeat() >= 100.0D) {
            // respond to maximum heat
        }
    }
}
```

---

## Recommended addon patterns

### 1) Check API availability on enable

```java
@Override
public void onEnable() {
    if (!SocialCreditProvider.isAvailable()) {
        getLogger().severe("SocialCredit API unavailable; disabling addon.");
        getServer().getPluginManager().disablePlugin(this);
        return;
    }
}
```

### 2) Keep all SocialCredit access behind one bridge class

This makes future version updates easier.

```java
public final class SocialCreditBridge {
    public SocialCreditAPI api() {
        return SocialCreditProvider.get();
    }
}
```

### 3) Use the webhook API instead of shipping a second webhook pipeline

```java
api.postDiscordWebhook("Black market inspection triggered in Sector 7.");
```

### 4) Prefer events over repeated polling

If you need to react to score/heat changes, use:
- `SocialCreditChangeEvent`
- `SocialCreditHeatChangeEvent`

### 5) Never assume internal config structure

The public API is stable integration surface. Internal config/service classes can change between builds.

---

## Example: simple addon usage

```java
package com.example.myaddon;

import com.example.socialcredit.api.SocialCreditAPI;
import com.example.socialcredit.api.SocialCreditProvider;
import com.example.socialcredit.api.result.ScoreChangeResult;
import org.bukkit.entity.Player;

public final class ExampleUsage {

    public static void rewardCivicAction(Player player) {
        if (!SocialCreditProvider.isAvailable()) {
            return;
        }

        SocialCreditAPI api = SocialCreditProvider.get();
        ScoreChangeResult result = api.addScore(
                player.getUniqueId(),
                player.getName(),
                4,
                "Civic cooperation"
        );

        if (result.success()) {
            player.sendMessage("Your conduct has been noted. New score: " + result.newScore());
        }
    }
}
```

---

## Example: heat-aware addon logic

```java
if (SocialCreditProvider.isAvailable()) {
    SocialCreditAPI api = SocialCreditProvider.get();
    double heat = api.getHeat(player.getUniqueId());

    if (heat >= 80.0D) {
        api.postDiscordWebhook("High-heat citizen detected: " + player.getName());
    }
}
```

---

## Example: state terminal integration

```java
StateTransactionResult result = api.withdrawFromState(
        player.getUniqueId(),
        player.getName(),
        Material.BREAD,
        4
);

if (!result.success()) {
    player.sendMessage(result.message());
}
```

---

## Placeholder context for addon developers

The core plugin includes PlaceholderAPI support through `SocialCreditExpansion`.

If your addon wants to expose related placeholders, keep your namespace separate and do not overwrite SocialCredit's placeholders.

---

## Recommended softdepend / harddepend guidance

For addon plugins:
- Use **hard depend** if your plugin cannot function at all without SocialCredit.
- Use **soft depend** only if your plugin has a valid fallback mode.

Typical `plugin.yml` pattern for a true addon:

```yaml
depend: [SocialCredit]
```

---

## Notes for release discipline

This API reference is based on the **1.6.2 core source/jar** currently provided.

When releasing addon updates:
- verify API availability at startup
- test score changes
- test heat changes
- test webhook path if used
- test state credit operations if used
- verify no internal service classes are imported

---

## Public API surface checklist

Classes confirmed for addon use:
- `com.example.socialcredit.api.SocialCreditAPI`
- `com.example.socialcredit.api.SocialCreditProvider`
- `com.example.socialcredit.api.event.SocialCreditChangeEvent`
- `com.example.socialcredit.api.event.SocialCreditHeatChangeEvent`
- `com.example.socialcredit.api.model.PlayerSocialState`
- `com.example.socialcredit.api.model.StateTerminalInfo`
- `com.example.socialcredit.api.result.DebugReportResult`
- `com.example.socialcredit.api.result.ScoreChangeResult`
- `com.example.socialcredit.api.result.StateTransactionResult`

---

## Summary

SocialCredit 1.6.2 exposes a clean public addon surface for:
- score access and mutation
- wanted/jail state reads
- state credits and stockpile transactions
- heat reads and writes
- aggregate player state reads
- terminal discovery
- debug report generation
- Discord webhook reuse
- score change and heat change events

For addon developers, the rule is simple:

**Depend on the public API, not the internals.**
