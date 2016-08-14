# kulr
Manage multiple color configurations (e.g. vim, Xresources) in one place.

A `kulrrc` file is used to store shared settings (e.g. color values), and target scripts are used to replace old configuration settings with new ones.

# Usage

```
kulr [options] [target ...]
```

# Options

```
-l         List available target scripts
-b         Back up source config file before making modifications
-c <file>  Kulr config file (if not specified ~/.config/kulr/kulrrc will be used)
-v         Verbose output
-y         Don't prompt for confirmation before running target scripts
-h         Print this help message and exit
```

# Quickstart

1. Clone the repo
2. Add kulr to your path
3. Copy the example files to ~/.config/kulr

# Examples

List available target scripts

``` sh
$ kulr -l
vim
xresources
```

Run all available target scripts and prompt before making changes

``` sh
$ kulr
vim
xresources
Are you sure? [Y/n] y
```

Run the vim target script, output verbose messages, back up source config files, and do not prompt before making changes

``` sh
$ kulr -vby vim
vim - backing up /home/user/.config/nvim/colors/kulr.vim to /home/user/.config/nvim/colors/kulr.vim.bak
vim - modifying /home/user/.config/nvim/colors/kulr.vim
```

# Config

## Example

``` sh
# Colors based on Tomorrow Night theme

# Black
KULR_0="303030"
KULR_8="969896"

# Red
KULR_1="cc6666"
KULR_9="cc6666"

# Green
KULR_2="b5bd68"
KULR_10="b5bd68"

# Yellow
KULR_3="de935f"
KULR_11="f0c674"

# Blue
KULR_4="81a2be"
KULR_12="81a2be"

# Magenta
KULR_5="b294bb"
KULR_13="b294bb"

# Cyan
KULR_6="8abeb7"
KULR_14="8abeb7"

# White
KULR_7="c5c8c6"
KULR_15="ffffff"

KULR_SEL="585858"
KULR_LINE="3a3a3a"
KULR_WIN="5e5e5e"
KULR_BG="262626"
KULR_COM="${KULR_8}"
KULR_FG="${KULR_7}"
KULR_HI="${KULR_4}"

# Specify target scripts to run (separated by spaces)
targets="vim xresources"

# Confirm target selection before running
confirm=true

# Output verbose messages
verbose=true

# Back up source config files before modifying them
backup=true
```

## Themes

Kulr can be used to manage multiple themes by storing each theme in its own kulr config file. To load a theme called 'my-theme', for example, you might run:

``` sh
$ kulr -c ~/.config/kulr/themes/my-theme
```

# Target scripts

Target scripts hold the commands necessary to update a single config file.

## Example

The `init_source` function registers the config source file and optionally backs it up. The rest of the commands are used to update lines of the file, replacing the current color with the new one. Here, we are also running `xrdb` to reload the updated config.

``` sh
# .config/kulr/targets/xresources
init_source "${HOME%/}/.Xresources"

sed -i 's/\(^\s*\*\.foreground:\s*#\).*$/\1'"${KULR_FG}"'/'     "${source_path}"
sed -i 's/\(^\s*\*\.background:\s*#\).*$/\1'"${KULR_BG}"'/'     "${source_path}"
sed -i 's/\(^\s*\*\.cursorColor:\s*#\).*$/\1'"${KULR_FG}"'/'    "${source_path}"

for (( i = 0; i <= 15; i++ )); do
  color="KULR_${i}"
  sed -i 's/\(^\s*\*\.color'"${i}"':\s*#\).*$/\1'"${!color}"'/' "${source_path}"
done

# Reload config
xrdb "${HOME%/}/.Xresources"
```
