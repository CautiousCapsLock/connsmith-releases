# ConnSmith

Terminal client for network engineers on Windows 11 (64-bit). Tabbed SSH, Telnet and serial consoles, a shared session library, highlighted device output, split panes, a command window that sends to several tabs at once, and built-in TFTP, FTP and SFTP/SCP servers.

<img src="docs/images/hero.png" alt="ConnSmith with four device tabs in a group, the session tree on the left and highlighted CLI output" width="900">

This repository publishes the installers only. Download them from the [Releases page](https://github.com/CautiousCapsLock/connsmith-releases/releases/latest).

**Status:** in testing. Features marked **(0.1.6)** arrived in release 0.1.6; unmarked features were in earlier releases. Everything here is in the [latest release](https://github.com/CautiousCapsLock/connsmith-releases/releases/latest).

## Contents

- [Install](#install) and [Updates](#updates)
- [Getting started](#getting-started)
- Core features: [SSH](#ssh-modern-and-legacy-profiles), [Jump host](#jump-host-next-release), [Serial](#serial-console), [Session tree and Shared sessions](#session-tree-and-shared-sessions), [Tabs, tab groups and split panes](#tabs-tab-groups-and-split-panes), [Command window](#command-window), [Highlight rules](#highlight-rules), [Terminal appearance](#terminal-appearance), [Find](#find), [File servers](#file-servers-tftp-ftp-and-sftpscp), [SecureCRT import](#securecrt-import), [SSH keys](#ssh-keys-including-putty-ppk)

---

## Install

1. Download `ConnSmith-Setup.exe` from the [latest release](https://github.com/CautiousCapsLock/connsmith-releases/releases/latest).
2. Run it. The installer is not code-signed, so Windows SmartScreen can show a warning. Select **More info**, then **Run anyway**.
3. Follow the wizard. The install is per user and needs no administrator rights. The Start menu shortcut is on by default and the desktop shortcut is off.
4. Leave **Launch ConnSmith** ticked on the last page.

The uninstaller asks whether to keep ConnSmith settings. Unticked, it removes `%APPDATA%\ConnSmith`.

### Updates

ConnSmith checks for a new release a few seconds after it starts and every 6 hours. A new release puts a red dot on the Settings cog in the top right corner.

1. Select the cog. It opens **Settings > Updates**.
2. Review **What's new** and select **Download update**.
3. When the download finishes, select **Restart to update**. This closes ConnSmith and every open connection, installs the update and starts the new version.

Nothing downloads or installs without a click. The automatic check can be turned off on the same page. The **Dev builds** switch offers pre-releases for testing and is off by default.

---

## Getting started

### First launch

A welcome window asks for the folder that holds saved sessions (the Personal store). The default is a `ConnSmith` folder in Documents, which OneDrive syncs between a user's own PCs. **Use this folder** accepts it.

<img src="docs/images/first-launch.png" alt="The Welcome window with the Personal store folder and the app password step" width="420">

Two optional settings follow. Both can be changed later in **Settings**.

- **App password** (Settings > Security). Required at every start, before any session loads. It protects passwords saved in sessions and makes them usable on other PCs that share the same app password. Without it, saved passwords are readable on the PC that saved them only.
- **Windows Hello.** With an app password set, **Settings > Security** can turn on unlock with Windows Hello (fingerprint, face or PIN) instead of the typed password. Hello is built into ConnSmith and starts no scripts. The setting is per PC, so a second PC needs the app password typed once before Hello is turned on there. Uninstalling ConnSmith switches Hello off, even if the settings are kept.

### Connect to a device

**Alt+Q** opens Quick Connect. The connect bar at the top accepts `host`, `user@host`, `host:port`, `ssh://host`, `telnet://host` and `COM7`.

<img src="docs/images/quick-connect.png" alt="The Quick Connect window with protocol SSH2, a host, a username and the Legacy algorithm profile" width="420">

1. Select the protocol (SSH2, Telnet or Serial), enter the host and username, and select **Connect**.
2. On the first connection to a device, ConnSmith shows its host key fingerprint. Select **Trust and connect** after checking it.
3. Enter the password. The tab opens.

**Save session** in Quick Connect, or **Save session…** in a tab's right-click menu, stores the session. The save dialog proposes a name read from the device prompt.

### Saved and shared sessions

Saved sessions appear in the session tree on the left. Double-click opens one. Two stores are shown together:

- **Personal:** the user's own sessions.
- **Shared:** a team folder synced with OneDrive, normally a SharePoint library. See [Session tree and Shared sessions](#session-tree-and-shared-sessions).

---

## Core features

### SSH, Modern and Legacy profiles

SSH-2 with password, keyboard-interactive and public key authentication. Each session uses one of two algorithm profiles:

- **Modern** (default): current algorithms only.
- **Legacy**: adds `ssh-rsa`, CBC and 3DES ciphers, SHA-1 MACs and `diffie-hellman-group14-sha1`, which older switches require.

When a Modern connection fails because the device offers only older algorithms, ConnSmith lists what the device offers and shows a yellow **Switch to Legacy algorithms and reconnect** button. The button saves Legacy to the session and reconnects. Enter activates it. **What the device offers** expands the full list.

<img src="docs/images/legacy-prompt.png" alt="The prompt: This device only offers diffie-hellman-group14-sha1 and diffie-hellman-group1-sha1 for key exchange, with a yellow Switch to Legacy algorithms and reconnect button" width="900">

Also included: host key verification against a known-hosts file, keepalives, a **Reconnect** button after a dropped link, and the negotiated algorithms in the connection info panel (top right icon). **(0.1.6)** For a device whose host key is already saved, ConnSmith asks for that key type first, as OpenSSH does, so a device that has since gained a newer key type does not show a first-connection dialog.

### Jump host (0.1.6)

**Edit session > Jump host** lists the saved SSH sessions. When one is chosen, ConnSmith logs in to it first, with its own saved password, key and settings, and opens the connection to the device through it. Use it for devices that are reachable only from a bastion or management host.

<img src="docs/images/jump-host-edit.png" alt="Edit session with the Jump host drop-down set to bastion" width="520">

- The host key and login questions of the jump host are titled **Jump host**. The connection info panel shows it on the **Via** line.
- One hop only.
- A Shared session can use only a Shared session as its jump host, and a session moves to Shared only once its jump host is there.

<img src="docs/images/jump-host-info.png" alt="A connected tab with the connection info panel showing Via bastion" width="900">

**Known limitation:** devices that offer only `diffie-hellman-group1-sha1` (for example older Cisco IOS releases) cannot connect. The tab reports this.

### Serial console

COM ports are scanned every 2 seconds. When a USB console adapter is plugged in, a notification offers to open it at **9600** or **115200** baud.

<img src="docs/images/serial-toast.png" alt="A notification: COM3 - USB Serial Device plugged in, with 9600 and 115200 buttons" width="560">

<img src="docs/images/serial-quick-connect.png" alt="Quick Connect with the Serial protocol, a COM port list and a baud rate" width="420">

- **Send Break** in the tab menu, for ROMMON and password recovery.
- Newline and local echo options per session.
- Serial profiles are stored in the Personal store only, never in Shared.
- A tab reconnects automatically when the same USB adapter is plugged back in.

### Session tree and Shared sessions

A single tree shows the Personal and Shared stores with their folders. The filter box searches name, host and folder. Sessions can be dragged between folders and between stores.

<img src="docs/images/session-tree.png" alt="The session tree: Personal sessions, and Shared sessions grouped by client, optional site and device type, with a green dot on each connected session" width="300">

- **Status dots:** a saved session with an open tab shows a dot at the right of its row: green connected, amber connecting, red disconnected.
- **Multi-connect:** Ctrl+click or Shift+click selects several sessions, and Enter opens each in its own tab. Host key and password prompts appear one after another. **(0.1.6)** With several sessions selected, the right-click menu also offers **Move to Shared…** and **Delete N sessions…**. Delete lists the sessions and asks first.
- **Collapse all (0.1.6):** the right-click menu of every folder, and of the Personal and Shared rows, folds the tree. ConnSmith remembers which folders were open.
- **Shared store:** set the team folder in **Settings > Session stores**. Each user enters the same shared password once on their own PC.
- **Move to Shared:** the right-click menu on a session or folder moves it to the Shared store after the share checks. A session that fails a check stays in Personal and the dialog gives the reason. **Session > Move many to Shared…** moves many sessions in one wizard. **(0.1.6)** The wizard's last step offers to delete the Personal folders the move left empty, with the tick box on.
- **OneDrive:** the tree shows a warning when shared files are online-only placeholders. The folder must be set to **Always keep on this device** in File Explorer.

<img src="docs/images/shared-store.png" alt="Settings, Session stores: the Personal and Shared folders and the Shared password" width="760">

#### Shared folder layout (0.1.6)

Shared sessions follow one layout: **Client**, an optional **Site**, then a device type folder (**Firewalls**, **Switches**, **VoIP** or **Other**), then the sessions.

- A client's right-click menu has **New site folder…** and **New device folder…**. A site's menu has **New device folder…** only. A client and each of its sites can hold the same device types.
- **Move to Shared** asks for client, site and device type. The wizard guesses the site from a folder such as `Acme/London/Switches`.
- Nothing is created ahead of time. Device type folders cannot be renamed.
- Folders and sessions already in Shared that do not fit the layout are flagged with a warning and a **Review** button that lists them. Nothing is moved or hidden.
- Deleting a Shared folder asks twice, because it removes the folder for everyone. The confirmation says how many sessions go with it.

#### Duplicate checks (0.1.6)

The Shared store is edited by several people, so ConnSmith checks for the same thing being added twice. The checks ignore case, spaces and punctuation, so `Core SW1` and `core-sw1` match. A short host name and the same name with a domain, or one IPv6 address written two ways, count as the same device.

- **Folders:** a new or renamed client or site folder that matches one beside it is refused. One that is only close (`Acme` and `Acme Ltd`, or a one-letter typo) asks once more, and the button becomes **Create anyway**.

<img src="docs/images/create-anyway.png" alt="New site folder: a warning that Londen is close to London, with a Create anyway button" width="460">

- **Sessions:** when a session being moved to Shared has the same device as one already there, the dialog lists the existing sessions with **Open** and **Edit** buttons, and the button becomes **Move anyway**. The wizard has a **Move anyway** tick box on each such session. A failed name or host check still blocks the move.

<img src="docs/images/move-anyway.png" alt="Move to Shared: an Already in Shared notice listing two sessions with the same host, and a Move anyway button" width="560">

- **Possible duplicates banner:** when duplicates are already in the store, for example because two people shared the same device at once, the tree shows **N possible duplicates in Shared** with a **Review** button. Nothing is changed automatically: delete or rename one from the tree, or leave both if both are wanted.

<img src="docs/images/shared-duplicates.png" alt="The session tree with a banner at the bottom: 2 possible duplicates in Shared, and a Review link" width="460">

<img src="docs/images/duplicates-review.png" alt="The Possible duplicates in Shared window listing two sessions with the same device and two folders with close names" width="640">

- **Empty folders:** after the last session leaves a Shared device folder (deleted, or moved to Personal or another Shared folder), ConnSmith asks **Delete the empty folder…?**. A client or site folder that the removal would also empty is included. It never deletes on its own, so a colleague who is about to add to the folder can keep it.

<img src="docs/images/delete-empty-folder.png" alt="A confirmation: Delete 2 empty folders, listing Shared, Fabrikam, Leeds, Switches and Shared, Fabrikam, Leeds" width="460">

### Tabs, tab groups and split panes

Each connection opens in a tab. Tabs reorder by drag. **Move tab to new window** detaches a tab together with its connection and scrollback. The tab menu also closes other tabs or the tabs to the right.

**Tab groups** keep the devices for one task together. **Connect as tab group…** in the tree's right-click menu opens the selected sessions as a named, coloured group, shown as a chip before its tabs. Selecting the chip folds the group. **Add to new group…** in a tab's menu groups tabs that are already open.

<img src="docs/images/tabs-groups.png" alt="Four tabs under a blue Northwind group chip" width="900">

<img src="docs/images/tab-group-dialog.png" alt="The Connect as tab group window with a group name and colour choices" width="420">

**Split panes (0.1.6).** **Split pane right** and **Split pane down**, in a tab's right-click menu and in the Tab menu, open a second connection to the same place beside or under the first. Each pane has its own connection and asks for its own login. Drag the bar between the panes to resize them.

- **Close pane** closes one pane. **Move pane to its own tab** undoes the split. Closing the tab closes both panes.
- A tab holds two panes at most. A serial port cannot be split.
- The command window's **Visible panes** option sends to both panes.

<img src="docs/images/split-panes.png" alt="One tab split into two panes, each showing different output, with the command window set to Visible panes (2)" width="900">

### Command window

**View > Command window** opens an input box at the bottom of the window. Its target is one of:

- **Visible panes:** the visible tab, or both panes of a split tab **(0.1.6)**,
- the tab group of the visible tab,
- all active sessions.

The radio buttons name the target and the number of tabs, for example `Tab group: Northwind (4)` or `Visible panes (2)`. Enter sends. Shift+Enter adds a line. Up and Down recall earlier commands. **Wait for the prompt after each line** holds each line until the device prompt returns. Disconnected tabs are skipped and the window reports how many.

<img src="docs/images/command-window.png" alt="The command window sending show clock to the Northwind tab group" width="900">

### Highlight rules

Device output is coloured as it arrives. Rules cover IP addresses, interface up and down states, warnings and errors, ping loss, BGP and OSPF neighbour states, `[confirm]` prompts, and `reload` and `erase`. Rule sets exist for Cisco, Comware, Aruba, Junos and FortiOS, plus a common set that always applies.

The rule set is selected from the device's output. The **Highlighting** menu in the status bar shows the active set and switches it.

**Follows the prompt (0.1.6).** On **Auto**, the rule set follows the prompt after login, not only at login. A session that logs in to a Linux host and hops from there to a FortiGate (or a Junos or Comware device) switches to that device's rules once its prompt appears, and goes back to the common set when a shell prompt returns. A set picked by hand from the Highlighting menu never changes.

<img src="docs/images/highlight-follows-prompt.png" alt="A session that logged in to a bastion and hopped to a FortiGate, with the status bar showing Highlighting: Auto (Fortinet FortiOS)" width="900">

<img src="docs/images/highlight-rules.png" alt="Router output with addresses in blue, up in green, down in red and an Active BGP neighbour in amber" width="900">

### Terminal appearance

Colour scheme, font, font size, bold and cursor style are set per session in **Edit session**, and for new sessions in **Session > Default session…**. A session that does not set a value follows the Default session.

- **Colour schemes:** ConnSmith Dark Blue, Light, Solarized Dark, Classic (white on black), Graphite Orange and Matrix. The highlight colours follow the scheme.
- **Font:** a drop-down of Cascadia Mono, Cascadia Code, Courier, Courier New, Lucida Console and MS Gothic, each shown in its own face. Fonts that are not installed on the PC fall back to Consolas. Cascadia Mono is bundled with ConnSmith.
- **Font size:** the even sizes from 8 to 24. The default is 14.
- **Bold:** a tick box that sets the bold weight for all text.
- The built-in choice in each drop-down is marked **(Default)**.

<img src="docs/images/default-session.png" alt="The Default session window with Colour scheme set to Graphite Orange, and the Font, Font size and Bold options" width="560">

Graphite Orange (orange on grey) and Matrix (green on black):

<img src="docs/images/colour-schemes.png" alt="The same router output in the Graphite Orange scheme and in the Matrix scheme" width="900">

### Find

**Ctrl+Shift+F** searches the scrollback of the current tab, with case-sensitive and regular expression options. Every match is also marked in a strip beside the scrollbar, showing where matches fall in a long output.

<img src="docs/images/find.png" alt="The Find box with matches highlighted in the output and marked beside the scrollbar" width="900">

### File servers: TFTP, FTP and SFTP/SCP

**Tools > File servers** opens a panel with three servers that serve one shared folder (the Downloads folder by default, changed in **Settings > File servers**).

| Server | Port | Typical use |
| --- | --- | --- |
| TFTP | UDP 69 | Firmware and configuration transfer for switches and access points |
| SCP / SFTP | TCP 22 (2222 if 22 is in use) | Devices that use `scp` or `sftp`, such as FortiGate |
| FTP | TCP 21 (passive range 50000-50100) | Devices that support FTP only, such as Cisco `copy ftp:` |

<img src="docs/images/file-servers.png" alt="The File servers panel with TFTP, SCP/SFTP and FTP switched on and a list of transfers" width="900">

- The **File Server Listen IP** in the status bar selects the address the servers bind to, or **All interfaces**.
- The panel shows each server's address and, for SFTP and FTP, the login and a password generated each time the server is switched on.
- The folder is read-only until **Allow uploads** is switched on. The switch resets at every start. Devices cannot delete files or directories.
- Each transfer is listed with device, file, progress and speed. Refused requests are listed with the reason.
- **Allow in Windows Firewall** adds inbound rules for these ports only, after one administrator prompt. Where policy prevents this, the panel shows the equivalent `netsh` command.

### SecureCRT import

**File > Import SecureCRT sessions…** reads the SecureCRT sessions folder and shows its folders and SSH and Telnet sessions as a tree of tick boxes. **Tick all**, **Tick none**, **Collapse all** and **Expand all** act on the whole tree. The ticked sessions are added to the Personal store with the same folder structure. SecureCRT's own files are not modified, and sessions that already exist in ConnSmith are not changed.

<img src="docs/images/securecrt-import.png" alt="The Import SecureCRT sessions window with a tree of folders and sessions with tick boxes" width="460">

The final page offers **Move to Shared…**, which opens the wizard that assigns a client and category to each folder and moves the sessions that pass the share checks.

### SSH keys, including PuTTY .ppk

**Settings > SSH keys > Add key…** accepts OpenSSH and PEM private keys and PuTTY `.ppk` files: version 2 RSA, and version 3 (PuTTY 0.75 and later) RSA, ECDSA and ed25519, with or without a passphrase. No conversion in PuTTYgen is needed.

Keys stay in their original files and are never copied into sessions or the Shared store. A session selects a key by name in **Edit session**. The key is tried first, and the password prompt follows if the device refuses it.

<img src="docs/images/ppk-keys.png" alt="Settings, SSH keys, listing two added keys with their fingerprints" width="760">

---

## Other features

- **Session logging** to a file with a templated path and optional timestamps, started from the tab menu or automatically per session.
- **Paste protection:** pastes are shown for confirmation first and can be sent line by line with per-session line and character delays.
- **Hostname check:** when the device prompt differs from the saved session name, ConnSmith offers to update the session. A switch in **Settings > Terminal** turns the offer off.
- **Command palette** for all menu actions.

## Reporting problems

Send issues to Matt with the ConnSmith version, shown in **Settings > Updates**.
