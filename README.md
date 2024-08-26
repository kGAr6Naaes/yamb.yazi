# Yet another bookmarks

A [Yazi](https://github.com/sxyazi/yazi) plugin for bookmark management, supporting the following features

- Persistent bookmarks. No bookmarks are lost after you close yazi.
- Quickly jump, delete, and rename a bookmark by keymap.
- Support fuzzy search through [fzf](https://github.com/junegunn/fzf).
- Configure your bookmarks using Lua language.

## Installation

> [!NOTE]
> Yazi >= 0.25.

```sh
# Linux/macOS
git clone https://github.com/h-hg/yamb.yazi.git ~/.config/yazi/plugins/yamb.yazi

# Windows
git clone https://github.com/h-hg/yamb.yazi.git $env:APPDATA\yazi\config\plugins\yamb.yazi
```

## Usage

Add this to your `yazi/init.lua` (Linux: `~/.config/yazi/init.lua`)

Note: If you're new to Lua, whenever you see `..` that means concatenate.

```lua
-- Create a table to programmatically add bookmarks
local bookmarks = {}

-- This variable equals to a separator character based on your platform
-- Windows gets \
-- Linux gets /
local path_sep = package.config:sub(1, 1)

-- Get the home directory based on platform
local home_path = ya.target_family() == "windows" and os.getenv("USERPROFILE") or os.getenv("HOME")

-- Add bookmarks programmatically
-- Example 1: Add windows path pointing to the scoop directory
if ya.target_family() == "windows" then
  table.insert(bookmarks, {
    tag = "Scoop Local",
    
    path = (os.getenv("SCOOP") or home_path .. "\\scoop") .. "\\",
    key = "p"
  })
  table.insert(bookmarks, {
    tag = "Scoop Global",
    path = (os.getenv("SCOOP_GLOBAL") or "C:\\ProgramData\\scoop") .. "\\",
    key = "P"
  })
end

-- Example 2: Add a bookmark pointing to Desktop
table.insert(bookmarks, {
  tag = "Desktop",
  path = home_path .. path_sep .. "Desktop" .. path_sep,
  key = "d"
})

require("yamb"):setup {
  -- Add the table we declared at the beginning to have bookmarks declared added to our list
  bookmarks = bookmarks,
  -- Optional, the cli of fzf.
  cli = "fzf",
  -- Optional, a string used for randomly generating keys, where the preceding characters have higher priority.
  keys = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ",
  -- Where is the bookmark file saved?
  -- Check if we're on Win or Linux, assemble the correct path and assign it to variable path
  path = (ya.target_family() == "windows" and os.getenv("APPDATA") .. "\\yazi\\config\\bookmark") or
        (os.getenv("HOME") .. "/.config/yazi/bookmark"),
}
```

Add this to your `keymap.toml`:

```toml
[[manager.prepend_keymap]]
on = [ "u", "a" ]
run = "plugin yamb --args=save"
desc = "Add bookmark"

[[manager.prepend_keymap]]
on = [ "u", "g" ]
run = "plugin yamb --args=jump_by_key"
desc = "Jump bookmark by key"

[[manager.prepend_keymap]]
on = [ "u", "G" ]
run = "plugin yamb --args=jump_by_fzf"
desc = "Jump bookmark by fzf"

[[manager.prepend_keymap]]
on = [ "u", "d" ]
run = "plugin yamb --args=delete_by_key"
desc = "Delete bookmark by key"

[[manager.prepend_keymap]]
on = [ "u", "D" ]
run = "plugin yamb --args=delete_by_fzf"
desc = "Delete bookmark by fzf"

[[manager.prepend_keymap]]
on = [ "u", "A" ]
run = "plugin yamb --args=delete_all"
desc = "Delete all bookmarks"

[[manager.prepend_keymap]]
on = [ "u", "r" ]
run = "plugin yamb --args=rename_by_key"
desc = "Rename bookmark by key"

[[manager.prepend_keymap]]
on = [ "u", "R" ]
run = "plugin yamb --args=rename_by_fzf"
desc = "Rename bookmark by fzf"
```
