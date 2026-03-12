# SocialCredit

SocialCredit is a Minecraft Paper plugin that adds a playful “social credit” system to a server.

Players can gain or lose points depending on how they behave in the world, which creates a light-hearted layer of rewards, penalties, and general chaos on top of normal gameplay. The plugin is designed to be fun first, while still being structured enough to act as a real framework for future add-ons.

## Current release

**Version: v1.0**

This is the first stable release of the SocialCredit core framework. It has been built, tested in a live server environment, and is the v1.0 release. We reccomend updating your plugin when new updates are pushed.

## What v1.0 includes

- Social credit scoring system
- Behaviour-based rewards and penalties
- Wanted system and bounty handling
- Jail system
- State Resource Department terminal with deposit / withdraw functionality
- Discord webhook notifications
- Debug and diagnostics reporting
- SQLite persistence
- Tab-list score display
- Framework direction for future companion plugins

## Project direction

SocialCredit is intended to be the **core framework plugin**.

Future feature packs should be built as separate companion plugins, for example:

- SocialCreditFarming
- SocialCreditMining
- SocialCreditCivilOrder

This keeps the core stable while allowing the wider system to grow through modular additions.

## Installation

1. Place the compiled jar into your server's `plugins` folder.
2. Start or restart the server.
3. Let the plugin generate its config files.
4. Configure any optional Discord webhook settings if you want external notifications.

## Maintainer

SocialCredit is maintained by **ZeeRaider**.

This project is developed as an independent hobby project within the Minecraft server community. Development and distribution are handled under this online pseudonym.

## Community Use

Server owners are free to use the plugin on their servers under the terms of the MIT licence. Feedback and suggestions from the community are always welcome.

## Licence

This project is released under the **MIT License**.

That means you are free to use it, modify it, and distribute it under the terms of that licence.
