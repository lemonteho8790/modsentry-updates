# ModSentry

Support: [Discord](https://discord.gg/C45A3Dv6t8)

Detects hacked clients, cheat mods and client spoofers on Paper and Folia servers, and punishes
them the way you choose. It is an identity filter, not a movement or combat anticheat: run it next
to your usual anticheat.

- **No other plugins needed.** One jar. ViaVersion, Floodgate, Geyser and PlaceholderAPI are
  used when present.
- **Paper or Folia 1.20.1 and newer**, Java 17+.

## What it catches

- **Cheat clients and mods** (Meteor, Wurst, LiquidBounce, Aristois, BleachHack, Freecam, X-ray
  mods, auto clickers, auto attack, AutoTotem, Baritone, world downloaders and more): over 85 mods
  and clients built in, updated with every release. Every mod published on Modrinth was checked
  against its own released files, old and new versions, so its detection matches the mod. Add your
  own detections in `config.yml`.
- **See and change every detection**: `config.yml` lists every built-in detection by name under
  `punishments.mods`, each with its own punishment: `ban`, `kick`, `none` (alert only), `ignore`
  (no alert at all) or your own commands.
- **Clients that lie about what they are**: modded clients claiming to be vanilla (even when they
  hide their plugin channels), fake Lunar Client brands, LiquidBounce's client spoofer, and clients
  that hide every mod from the server.
- **Bots and unusual clients**: clients that send what no vanilla client sends.
- **Ban evaders**: accounts joining from the same address as a player ModSentry banned.
- **Strict servers**: an optional channel allowlist flags any client that registers a channel you
  did not list, and hostname rules refuse players who join through an address you do not allow.

A full scan takes about a second, runs once the player starts walking, and is invisible to them.

## Punishments

- Cheat clients are banned on an escalating ladder: 1m, 5m, 10m, 30m, then 1h for every ban
  after that. The ladder starts over 30 days after a player's last ban.
- Mods many servers allow (minimaps, replay mods, inventory sorting, schematics) only alert staff.
- Every detection's punishment can be changed: `ban`, `kick`, `none`, or your own list of console
  commands. Set it per mod under `punishments.mods`, or for a whole confidence level.
- Players see exactly which mods to remove, on the kick or ban screen and through placeholders.
- Detections from one scan are punished together, so a player is told every mod at once.
- **Test mode** (`punishments.enabled: false`) shows staff what each player would get without
  punishing anyone. ModSentry starts in test mode: check the alerts for a few days, then set
  `punishments.enabled: true`.
- Works with your ban plugin: set `punishments.ban.command`, for example
  `tempban %player% %duration% %mods%`.

## Verification servers

- **Freeze** (`verification.freeze`): players are held still from joining until their check is
  done, so nobody plays a second before being checked.
- **On-pass commands** (`verification.on-pass`): run console commands once a player passes, for
  example to give a rank or send them on to your main server.

## Keeping legit players safe

- Bedrock players (Floodgate or Geyser) are never checked.
- Players whose game is not in English and who use a mod translation resource pack are not
  punished automatically; staff get an alert to check by hand.
- Laggy players get retries before anything happens.
- Privacy mods that hide translations are kicked with a message, not banned.
- Alt linking ignores shared networks and stores addresses only as salted hashes.

## Commands

All commands are `/modsentry` (alias `/msentry`) and need `modsentry.command`.

| Command | What it does |
|---|---|
| `/msentry` | Help |
| `/msentry info <player>` | Brand, language, join address, channels, mod loader, detections, bans and alts. Works for offline players |
| `/msentry scan <player>` | Check the player again (alerts only, never punishes) |
| `/msentry online` | Everyone online and what they were caught with |
| `/msentry search <mod>` | Online players caught with a mod |
| `/msentry stats` | Scans, bans, kicks and the most detected mods so far |
| `/msentry alts <player>` | Accounts that joined from the same address |
| `/msentry reset <player>` | Start their ban ladder over and lift a ModSentry ban |
| `/msentry alerts` | Turn your own alerts off or back on |
| `/msentry reload` | Reload `config.yml` and report any problems in it |
| `/msentry testwebhook` | Send a test message to your Discord webhook |

## Permissions

| Permission | Default | |
|---|---|---|
| `modsentry.command` | op | Use `/msentry` |
| `modsentry.alerts` | op | See alerts in chat |
| `modsentry.bypass` | nobody | Skip all checks. Ops do **not** have it unless you give it |

## Placeholders (PlaceholderAPI)

| Placeholder | |
|---|---|
| `%modsentry_mods%` | Mods named in the player's last punishment |
| `%modsentry_punishment%` | `ban` or `kick` |
| `%modsentry_duration%` | How long the last ban lasts |
| `%modsentry_bans%` | Bans on record toward the ladder |
| `%modsentry_next_ban%` | How long the next ban would last |
| `%modsentry_alts%` | Accounts linked by address |
| `%modsentry_brand%` | Client brand (online players) |
| `%modsentry_loader%` | Mod loader found by the deep checks (online players) |
| `%modsentry_detections%` | What the player was caught with this session (online players) |

The first five work for offline players too, so a ban plugin's ban screen can use them.

## Alerts and logs

- Chat alerts for staff, with the player's name clickable to open `/msentry info` and the mod
  showing why it was flagged on hover. The console gets the same line.
- Discord webhook with colored embeds and the player's head (or plain messages).
- `plugins/ModSentry/detections.log` keeps every detection and punishment.
- All messages are MiniMessage and can be changed in `config.yml`.

## Updating

Replace the jar and restart. New settings are added to your existing `config.yml` with their
comments; your own values are never changed. The built-in detections come with the jar, so every
update brings the newest ones without touching your config.

ModSentry checks for a newer version at startup and every 12 hours, and tells the console and
staff with `modsentry.command` when one is out. Only the version number is downloaded; nothing
about your server is sent. Turn it off with `update-check.enabled: false`.

## What it cannot do

Minecraft trusts the client. A hacked client built to behave exactly like vanilla, sending the
same packets and hiding everything, cannot be detected by any server plugin, including this one.
ModSentry catches the clients and mods that leave a trace, which is most of them. Cheat
developers update their clients, and detections are updated to match.

It also does not check movement or combat; keep your behavioral anticheat.

## Compatibility notes

- **Proxies:** with BungeeCord or Velocity, turn on IP forwarding, or alt linking is disabled
  (it warns once in the console). Keep `hostnames.mode` off behind a proxy.
- **Login plugins:** while `verification.freeze` holds a player, commands such as `/login` wait
  until the check is done (a few seconds).
- **Plugins that kick for any failed resource pack:** turn off `checks.resource-pack-probe`.
- **Packet-limiting plugins:** the deep checks open and close a sign editor in one tick. If a
  packet limiter kicks players for sign edits, exempt that packet or raise its limit.

## License

ModSentry is a paid plugin, licensed per buyer for the servers they own or manage. Sharing,
reselling, reuploading and decompiling are not allowed; the full terms come with the download
(`LICENSE.txt`).
