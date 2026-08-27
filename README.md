# dotfiles

This repository is intended to hold the config files for the Unix utilities used across my various machines.

I'm using [chezmoi](https://chezmoi.io), the dotfile management tool that allows for declaratively defining your configurations across many diverse machines from a single source of truth.

At present, I use two primary x86_64 desktops: One running Fedora 44; the other running Windows with Ubuntu 26.04 on WSL. My chezmoi configuration will largely be designed around these machines but should be generally applicable to other Ubuntu- or Fedora-flavors of Linux, particularly those running GNOME.

Note that I intend for these configs to be limited in scope to a single user's home directory. Besides the system-wide packages that get installed, chezmoi shouldn't touch anything outside of `/home/$USER`. As such, provisioning a fresh machine requires some manual steps in addition to running `chezmoi apply`.

# Setup Instructions (aka: Notes to Future Self)

## 1. Install password manager; import ssh config

Install KeePassXC password manager. On Windows, install natively.

From cloud storage, download the kdbx database(s). Manually import the ssh keys and ssh config file to `~/.ssh`, and set file permissions appropriately:

```sh
mkdir -p ~/.ssh && cd ~/.ssh
keys='id_rsa id_ed25519 ...'
for k in $keys; do
    chmod 600 $k
    ssh-keygen -y -f $k > "$k.pub"
    chmod 644 "$k.pub"
done
chmod 700 ~/.ssh
```

## 2. If running GNOME, install/enable extensions

Ensure the [GNOME shell integration](https://addons.mozilla.org/firefox/addon/gnome-shell-integration) Firefox extension is installed. This should already be installed if you're signed into your Mozilla account.

From `extensions.gnome.org`, install the desired extensions. See `enabled-extensions` in the dconf settings.

## 3. Configure sudo

Run `sudo visudo -f /etc/sudoers.d/$USER` and append a line of the following form to disable the password requirement for sudo commands:

`<user> ALL=(ALL:ALL) NOPASSWD: ALL`

## 4. Install and initialize chezmoi

The following commands should install the chezmoi executable to `~/opt` and create a symbolic link in `~/.local/bin`. Once installed, run the `chezmoi init` command to clone this repo and initialize the local chezmoi installation. Note that this command uses the ssh url, which relies on the configuration added in step 1.

```sh
# Let's create the other home directories while we're here
mkdir -p ~/opt ~/.local/bin ~/dev/{home,work} ~/temp
sh -c "$(curl -fsLS https://get.chezmoi.io)" -- -b ~/opt
ln -sf ~/opt/chezmoi ~/.local/bin
PATH="$HOME/.local/bin:$PATH"
chezmoi init -S ~/dev/home/dotfiles home.github.com:colin360/dotfiles.git
```

## 5. Apply changes with `chezmoi apply`

Use any combination of `chezmoi status`, `chezmoi diff`, or `chezmoi cat [file]` to inspect the changes more closely before applying them.

## 6. Misc. optional setup

Desktop wallpapers:
- [Celestial Antiquity](https://github.com/diinki/wallpapers)
- more to come...

### Linux

Configure the repositories specified in [.chezmoiexternal.toml](.chezmoiexternal.toml).

```sh
# Add tldr remote url for fetching changes from upstream
cd ~/dev/home/tldr
git remote add upstream https://github.com/tldr-pages/tldr.git
git remote set-url --push upstream NONE

# Since this repo isn't under ~/dev we must manually set these
cd ~/.config/nvim
git config --local user.name "Colin Watson"
git config --local user.email "264837520+colin360@users.noreply.github.com"
```

Add user account to `dialout` group for read/write access to serial ports.

```sh
sudo usermod -aG dialout $USER

# Alternatively, log out and back in
newgrp dialout
```

### Windows

At present, I'm using the default Windows Terminal rather than Alacritty. To match my Alacritty config, manually install the appropriate nerd font and theme.

Nerd font:
1) Download an unzip `https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip`.
2) Select all `*.ttf` files, right click, and select `Install`.
3) In terminal settings, select `JetBrainsMono Nerd Font` under `Profiles` > `Ubuntu` > `Appearance`.

Gruvbox Dark theme:
1) Copy the JSON scheme object from `github.com/runxel/gruvbox-iterm`.
2) In terminal settings, click `Open JSON file` to open `settings.json`.
3) Paste the JSON object under `schemes`. Save the file. The theme should then appear as an option under `Profiles` > `Ubuntu` > `Appearance`.

To disable font ligatures, in the same `settings.json` file, add or modify the `font.features` object to set both `liga` and `calt` to zero:

```json
"font": {
    "face": "JetBrainsMono Nerd Font",
    "features": {
        "calt": 0,
        "liga": 0
    }
}
```

Adjust copy/paste keybindings:
- By default, Windows Terminal uses ctrl+v for pasting from the system clipboard. This keybinding conflicts with vim's visual block mode. In `settings.json`, add or modify `keybindings` to match the following:

```json
"keybindings":
[
    {
        "id": "Terminal.CopyToClipboard",
        "keys": "ctrl+shift+c"
    },
    {
        "id": "Terminal.PasteFromClipboard",
        "keys": "ctrl+shift+v"
    }
]
```
