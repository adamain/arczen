<div align="center">

# ArcZen

**Arc's ergonomics for Zen Browser: a blended sidebar, a command bar, tab tidying, Little Arc and a polished Picture-in-Picture, while staying lightweight.**

![Zen](https://img.shields.io/badge/Zen-1.24.1b-black) ![Sine](https://img.shields.io/badge/Sine-mod-blue) ![License](https://img.shields.io/badge/license-MIT-green)

</div>

> [!NOTE]
> ArcZen is an independent project. It is not affiliated with or endorsed by The Browser Company. "Arc" is named only to describe the inspiration.

---

## Contents

- [What ArcZen does](#what-arczen-does)
- [Installation](#installation)
- [Settings](#settings)
- [Tidy with Claude (optional)](#tidy-with-claude-optional)
- [Privacy and network](#privacy-and-network)
- [Compatibility](#compatibility)
- [Uninstalling](#uninstalling)
- [Reporting a problem](#reporting-a-problem)
- [License and credits](#license-and-credits)

## What ArcZen does

ArcZen starts by tuning Zen's native variables. It only rewrites the interface where that is not enough.

| Area | What changes |
|---|---|
| **Appearance** | Rounded shapes, a sidebar blended into the window, the page laid out as a card. Adjustable scale for the sidebar, the command bar and the site menu. Adjustable border thickness around the page. |
| **Command bar** | A centered panel, blurred or opaque. Adjustable opacity and blur strength. Zoom and translation can be shown. |
| **Site menu** | A configurable 4th button: copy link, reader mode, Picture-in-Picture, tab sound or split view. |
| **Extensions** | Pinned extensions appear as tiles under the address bar. |
| **Tabs** | **Tidy** sorts tabs into sections: by topic locally, by site, or by intent with Claude ([see below](#tidy-with-claude-optional)). |
| **Sidebar** | A discreet scrollbar, cleaner titles, a preview of a collapsed folder on hover, a preview of unread Gmail messages on hover. |
| **Links and downloads** | Link preview before opening. **Little Arc** opens links coming from other apps in a small window (<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>N</kbd>). Downloads with unreadable names are renamed, and the renaming can be undone. |
| **Picture-in-Picture** | Opens automatically when you leave the tab of a playing video. Animated entrance. The window can be thrown toward a corner. Firefox's full player remains available as an option. |

**Design principles:**
- no universal selector;
- no bundled or downloaded font;
- no `backdrop-filter`: the only blur is the command bar's, and it can be turned off;
- animations run on `transform` and `opacity`, with a few short exceptions (a colour fade, one margin slide);
- when the mod is disabled, the script removes everything it added to the interface. A few Firefox preferences set by ArcZen's options stay: see [Uninstalling](#uninstalling).

## Installation

### Requirements

- [Zen Browser](https://zen-browser.app) 1.24.1b, the version ArcZen is tested on, or later.
- [Sine](https://github.com/CosmoCreeper/Sine), the mod manager that loads both ArcZen's CSS **and** its script.

> [!IMPORTANT]
> ArcZen is only tested on **Windows 11**. Some options, such as the acrylic material or the window buttons, are Windows-specific. It can be installed on macOS and Linux, but without any guarantee.

### Install from this repository

ArcZen is not listed in a store. Sine installs it from this repository's address.

1. In `about:config`, set `sine.allow-unsafe-js` to `true`. Sine only loads the script of a mod installed from outside its store when this setting is on. Without it, only ArcZen's CSS and settings are installed.
2. Open `about:preferences`, then the **Sine** section.
3. Paste the mod's address in the install field and confirm: `https://github.com/adamain/arczen`.
4. Restart Zen if Sine asks you to.

> [!WARNING]
> `sine.allow-unsafe-js` lets **every** mod installed from outside the store run code. Only turn it on for repositories you have read.

Sine checks this repository for updates and installs them like any other mod update.

## Settings

All settings are in `about:preferences`, section **Sine**, then **ArcZen**. They apply without a restart, except *Matière des panneaux* (panel material), which says so.

> [!NOTE]
> ArcZen's interface and settings are in **French** for now. This page quotes the labels as they appear, with a translation.

> [!TIP]
> Some mods change the same areas as ArcZen and give unpredictable results. This is the case for full "wireframe" or "transparent" themes, such as *Wireframe* or *Transparent Zen*. Disable them before installing ArcZen.

## Tidy with Claude (optional)

By default, Tidy sorts **locally**, with no network access. The *Par intention* (by intent) mode asks Claude Haiku to group tabs by topic.

- **Cost:** it uses Anthropic's API with **your** key, and each tidy is billed to your API account. For a few dozen tabs, the cost is a fraction of a cent. Pricing: <https://www.anthropic.com/pricing>.
- **API key:** it is never written to preferences.
  1. Create a key at <https://console.anthropic.com>.
  2. Open `about:logins` and add a login:
     - **Website:** `https://api.anthropic.com`
     - **Username:** anything
     - **Password:** your key
  3. In ArcZen's settings, set *Méthode de tri* (sorting method) to *Par intention : Claude Haiku*.
- **Data sent:** the **title** of each tab, filtered as described below (150 characters at most), its **site name** when the site is public, the **names of the sections** you already have (so that tabs can join them), and your instructions (see below). Never the tab's address, the page content or cookies. Sorting with Claude is disabled in private browsing.
- **Site name:** it is sent only when it ends with a suffix of the Public Suffix List that ships with Firefox (`.com`, `.co.uk`…). An IP address, `localhost`, a name such as `nas.local` or a name that holds more than 8 digits is never sent. An internal site under a public domain, such as `intranet.example.com`, counts as public.
- **Filtering:** a title is free text chosen by the page, and a page without a title shows its address instead. ArcZen reads each title word by word and keeps only:
  - plain words: letters and digits, with an apostrophe, a hyphen or `&` inside and ordinary punctuation around them;
  - short numbers, times, dates, version numbers and fractions. A word never holds more than 8 digits in total, decimals included, whatever the script the digits are written in;
  - dotted names that end with a public suffix, such as `github.com`. A file name such as `main.py` has the same shape and is kept too.

  Every other word is replaced by a placeholder: file paths, `file:`, `data:`, `blob:` and any other `word:something` form, IP and MAC addresses, `localhost`, dotted names without a public suffix (`nas.local`, `notes.txt`), email addresses, query strings, numbers and words with more than 8 digits (`0612345678`, `0612-345678`, `3,14159265`), long codes that mix letters and digits, and any word that contains `/`, `\`, `_`, `=` or a similar sign. An address with a path is reduced to the name of its public site, or replaced when the site is not public. After such an address, a `word:something` form, a path that starts with `/`, `~/`, `./` or a drive letter, or a word that contains `\`, the rest of the title is dropped, because these can contain spaces. A section name that the filter would change is not sent at all.

  The filter does not understand meaning. Plain words are sent as they are, whatever they say: a person's name, a host name written as a single word, or the plain words next to a removed path (`Projects/Top Secret Plan/budget.xlsx` becomes `[…] Secret […]`). Keep the local sorting methods if your tab titles are too sensitive for that.
- **Instructions:** the *Consignes pour Claude* (instructions for Claude) field lets you state your preferences, for example "keep work and personal apart". This text is sent as you wrote it, without filtering.

## Privacy and network

ArcZen has **no telemetry and no server**. It makes only three kinds of network access, each tied to an option:

| Option | Access |
|---|---|
| Tidy *Par intention* (off by default) | `api.anthropic.com`, with your key (see above) |
| Gmail preview on hover | Reads the unread-mail feed of the Gmail account you already have open. The data stays between Zen and Google. |
| Section names from Firefox's local model (off by default) | Firefox downloads the model once, on first use. Classification then runs on your machine. |

## Compatibility

| | |
|---|---|
| Zen | 1.24.1b (tested) |
| System | Windows 11 (tested); macOS and Linux (untested) |
| Mod manager | Sine 2.3.0.3 (tested) |

The official **Zen Mods** marketplace only accepts CSS. ArcZen includes a script, so it needs Sine and is installed from this repository.

> [!NOTE]
> Picture-in-Picture: with several displays at different scaling factors, automatic placement can be imprecise.

## Uninstalling

1. Four settings write a Firefox preference that stays once ArcZen is removed. Set them back to their default value **before** removing the mod; ArcZen then clears the preference itself:

   | Setting | Default | Firefox preference |
   |---|---|---|
   | *Matière des panneaux* (panel material) | *Réglage de Zen* | `widget.windows.mica.popups` |
   | *Aperçu de lien avant ouverture* (link preview) | *Désactivé* | `browser.ml.linkPreview.enabled`, `browser.ml.linkPreview.shift` |
   | *Automatique en quittant l'onglet d'une vidéo en lecture* (automatic Picture-in-Picture) | off | `media.videocontrols.picture-in-picture.enable-when-switching-tabs.enabled` |
   | *Noms par le modèle local de Firefox* (section names from Firefox's local model) | off | `browser.ml.enable`, only if ArcZen turned it on |

   If the mod is already removed, reset these preferences from `about:config`.
2. Open `about:preferences`, then **Sine** and **ArcZen**, and click **Remove**. The script unloads without a restart.
3. The `arczen.*` preferences can then be cleared from `about:config`.

## Reporting a problem

Open an [issue](https://github.com/adamain/arczen/issues) and include:
- your Zen version and operating system;
- the other active mods;
- a screenshot;
- the browser console messages (<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>J</kbd>) that start with "ArcZen".

## License and credits

- Code released under the MIT license: see [LICENSE](LICENSE).
- Inspired by the ergonomics of Arc, by The Browser Company. No graphic asset from Arc is reused.
- Thanks to the [Zen Browser](https://github.com/zen-browser/desktop) and [Sine](https://github.com/CosmoCreeper/Sine) teams.
