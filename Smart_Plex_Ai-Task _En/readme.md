# 🎬 PYScript Smart Plex Launcher

**Description:**
This script uses **Ai Task** to automatically scan libraries and their content, manage devices and media players, search for, and play media content in the specific zone/room requested.

The script acts as an intelligent driver: it converts a voice command into a complex JSON request for the Plex API, automatically correcting speech recognition errors (grammar, endings).

**Features:**

* **Playback Commands:** Supports **"Play"** (from the beginning) and **"Resume"** (Smart Resume — from the paused point or the next episode).
* **Smart Search:** Searches by title, season, episode, album, song, playlist, year, genre, artist, actor, director, studio, **music videos**, and even **mood**.
* **Scenarios:** Distinguishes between requests like "Play fresh" (newest items without shuffling) and "Play anything" (shuffle/random).
* **Autonomy:** Automatically determines Plex Library IDs (Auto-Discovery) and caches the database for instant response.
* **Hardware Control:** Turns on TV/Set-top Box (Apple TV, WebOS, Tizen) and launches the Plex app in the required zone.

---

### ⚙️ Requirements

The following components are required for operation:

1. **Plex Media Server:** Installed and configured.
2. **Home Assistant:**
* **Plex Media Server** Integration.
* **Pyscript** Integration.
* Configured **Voice Assistant** (Assist) with a connected AI (Ollama / Google Gemini).



---

### 🚀 Installation and Setup

#### Step 1. Install Pyscript

1. Place the `plex_smart_launch.py` script file into the `/config/pyscript/` folder.
2. Edit the **SETTINGS** section in the file:
* `PLEX_URL`: Server address.
* `PLEX_TOKEN`: Authorization token.


> **How to get the token:** In the Plex Server web interface (not HA), click the three dots on any media item -> **Get Info** -> **View XML**. The token is located at the very end of the URL in the address bar.


* `ZONES`: Define your devices.


3. Reload Pyscript.

#### Step 2. Create the Bridge

To communicate between the script and the Voice Assistant, an intermediate script is needed.
Create a new script in Home Assistant (**Settings -> Automations & Scenes -> Scripts -> Add Script**) and paste this YAML code:

*Do not forget to give the AI access to this script in the Assistant settings!*

```yaml
alias: Smart Plex Bridge
description: Bridge to transfer commands from AI to Pyscript
fields:
  command_text:
    name: Command Text
    example: play movie Avatar
sequence:
  - action: pyscript.plex_smart_launch
    data:
      command_text: "{{ command_text }}"
  - stop: Success
mode: single

```

#### Step 3. Instructions for the Dialogue System (AI Prompt)

Add this text to the system instructions (System Prompt) of your AI (Ollama / Gemini) in Home Assistant:

```text
GENERAL RULES (PLEX):
1. When the user asks to play a movie/series/music, you MUST SILENTLY and IMMEDIATELY call the tool `script.smart_plex_bridge`.
2. Pass the full user phrase into the `command_text` parameter without changes.
3. Wait for the tool response (it will return "Success").
4. ONLY AFTER THAT, output the final phrase: "Sir, searching and launching. I am at your service."
5. NEVER answer anything else. Do not write any text before calling the tool.

```

---

### 🗣 Testing Functionality via Developer Tools:

In Developer Tools, go to the **Actions** tab (in YAML mode or UI mode):

* Find **pyscript.plex_smart_launch** in the Action field.
* Use the following commands in the Data field:

1.) **Simple Movie Launch**

```yaml
command_text: play movie The Gentlemen

```

2.) **Smart Resume (Continue watching)**

```yaml
command_text: resume TV show House M.D.

```

3.) **Precise Episode Launch**

```yaml
command_text: play season 3 episode 5 Rick and Morty

```

4.) **Music Videos**

```yaml
command_text: play clips Rammstein (Videos might not work but songs will play)

```

5.) **Fresh Releases (Sorted by date)**

```yaml
command_text: play fresh sci-fi

```

6.) **Search by Mood**

```yaml
command_text: play happy music

```

7.) **Search by Actor**

```yaml
command_text: play movie with Brad Pitt

```

8.) **Random Choice (Shuffle)**

```yaml
command_text: play any comedy

```

9.) **Search by Year**

```yaml
command_text: play action movie 2024

```

10.) **Music Album**

```yaml
command_text: play Linkin Park album Meteora

```
