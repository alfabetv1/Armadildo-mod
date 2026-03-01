# AutoRepair Mod — Fabric 1.21.1 (Robiony w 100% z AI, claude ai i cursor)

A client-side Fabric mod for Minecraft 1.21.1 that automatically right-clicks a configurable number of times, then sends the `/repair` command — all from a clean in-game GUI.

---

## Features

| Feature | Details |
|---|---|
| Auto right-click | Clicks N times per cycle (configurable) |
| Click speed | Fixed ms delay between clicks |
| Random delay | Per-click random delay in a min–max range |
| Repair command | Sends `/repair` after clicks with a configurable delay |
| In-game GUI | Open with **[G]** key, all settings editable in-game |
| Quick toggle | Enable/disable with **[H]** key (no GUI needed) |
| HUD overlay | Shows current status and click progress |
| Client-side only | No server-side mod required |

---

## Default Keybindings

| Key | Action |
|---|---|
| **G** | Open AutoRepair configuration GUI |
| **H** | Toggle auto-clicker on/off |

Both keybindings can be rebound in **Options → Controls → AutoRepair**.

---

## GUI Settings

| Field | Description | Default |
|---|---|---|
| Click Count | Number of right-clicks per cycle | 10 |
| Click Speed (ms) | Milliseconds between clicks (fixed mode) | 50 |
| Repair Delay (ms) | Wait time (ms) after last click before /repair | 500 |
| Enable Random Delay | Use random per-click delays instead of fixed | Off |
| Min Delay (ms) | Lower bound of random delay range | 6 |
| Max Delay (ms) | Upper bound of random delay range | 10 |

---

## Project Structure

```
autorepair-mod/
├── build.gradle                        # Gradle build script (Fabric Loom)
├── gradle.properties                   # Minecraft version, mod version, mappings
├── settings.gradle                     # Project name + Fabric plugin repos
├── gradle/wrapper/
│   └── gradle-wrapper.properties       # Gradle 8.8 wrapper config
│
└── src/main/
    ├── java/com/autorepair/
    │   ├── AutoRepairMod.java           # Client entrypoint — keybinds, HUD, tick events
    │   ├── AutoClickerConfig.java       # Singleton config (click count, delays, etc.)
    │   ├── AutoClickerManager.java      # Click loop thread, /repair command sender
    │   ├── AutoRepairScreen.java        # In-game GUI (Screen + widgets)
    │   ├── HudRenderer.java             # HUD overlay (status + click progress)
    │   └── mixin/
    │       └── InGameHudMixin.java      # Mixin placeholder (InGameHud target)
    │
    └── resources/
        ├── fabric.mod.json              # Mod manifest
        ├── autorepair.mixins.json       # Mixin config
        └── assets/autorepair/lang/
            └── en_us.json              # Translations
```

---

## How to Build

### Prerequisites

- **JDK 21** (exact version — Minecraft 1.21+ requires Java 21)
  - Download: https://adoptium.net/en-GB/temurin/releases/?version=21
- **Git** (to run `./gradlew` for the first time, which downloads Gradle automatically)
- Internet connection (for first-time dependency download only)

---

### Step-by-step Build Instructions

#### 1. Clone / set up the project

```bash
# If you have Git:
git init
# Or just unzip the project folder, then cd into it:
cd autorepair-mod
```

#### 2. Make the Gradle wrapper executable (Linux/macOS only)

```bash
chmod +x gradlew
```

#### 3. Download dependencies and generate IDE sources (optional but recommended)

```bash
./gradlew genSources
```

This downloads Minecraft, Yarn mappings, Fabric API, and decompiles Minecraft for IDE navigation.

#### 4. Build the mod JAR

```bash
./gradlew build
```

On **Windows**, use:

```bat
gradlew.bat build
```

#### 5. Find your built JAR

After a successful build, the mod JAR is located at:

```
build/libs/autorepair-1.0.0.jar
```

(The `-dev` and `-sources` JARs in the same folder are not needed for installation.)

---

### Installing the Mod

1. Install **Fabric Loader 0.15.11+** for Minecraft 1.21.1:
   - Download the installer from https://fabricmc.net/use/installer/
   - Run it and select Minecraft version **1.21.1**

2. Install **Fabric API 0.102.0+1.21.1**:
   - Download from https://modrinth.com/mod/fabric-api
   - Place the `.jar` in your `mods/` folder

3. Copy `autorepair-1.0.0.jar` into your Minecraft `mods/` folder:
   - Windows: `%APPDATA%\.minecraft\mods\`
   - macOS:   `~/Library/Application Support/minecraft/mods/`
   - Linux:   `~/.minecraft/mods/`

4. Launch Minecraft with the Fabric profile.

---

### IDE Setup (IntelliJ IDEA)

```bash
./gradlew idea
```

Then open the project folder in IntelliJ. The `.iml` and `.ipr` files will be generated automatically.

### IDE Setup (Eclipse)

```bash
./gradlew eclipse
```

---

## Extending the Mod

### Persist config to disk

Add Gson to your `build.gradle`:

```gradle
implementation 'com.google.code.gson:gson:2.10.1'
```

Then in `AutoClickerConfig`, add `save()` / `load()` methods that serialize the fields to `config/autorepair.json`.

### Additional click types

To also simulate **left-clicks**, use:

```java
client.interactionManager.attackBlock(/* BlockPos */, Direction.UP);
// or for entity attacks:
client.interactionManager.attackEntity(client.player, entity);
```

### Loop control

The manager currently loops indefinitely until `stop()` is called. To run only once, change `runLoop()` in `AutoClickerManager` to call `runOneCycle()` once and then set `active.set(false)`.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| `./gradlew: Permission denied` | Run `chmod +x gradlew` |
| `JAVA_HOME is set to an invalid directory` | Set `JAVA_HOME` to your JDK 21 install path |
| Build fails with `Could not resolve fabric-api` | Check internet connection; Fabric maven may be temporarily down |
| Mod loads but right-clicks don't work | Make sure you're in a world and not in a menu screen |
| `/repair` command not working | This requires a server plugin (EssentialsX, CMI, etc.) that provides `/repair` |

---

## License

MIT — free to use, modify, and distribute.
