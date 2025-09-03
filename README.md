# Ansible Role: i3 Window Manager

[![CI](https://github.com/pluggero/ansible-role-i3wm/actions/workflows/ci.yml/badge.svg)](https://github.com/pluggero/ansible-role-i3wm/actions/workflows/ci.yml) [![Ansible Galaxy downloads](https://img.shields.io/ansible/role/d/pluggero/i3wm?label=Galaxy%20downloads&logo=ansible&color=%23096598)](https://galaxy.ansible.com/ui/standalone/roles/pluggero/i3wm)

An Ansible Role that installs and configures i3 Window Manager with highly customizable keybindings, themes, and layouts.

## Table of Contents

- [Requirements](#requirements)
- [Role Variables](#role-variables)
  - [Installation Settings](#installation-settings)
  - [General Settings](#general-settings)
  - [Visual & Layout Settings](#visual--layout-settings)
  - [Color Themes](#color-themes)
  - [Keybinding Configuration](#keybinding-configuration)
  - [Workspace Configuration](#workspace-configuration)
  - [Monitor & Application Configuration](#monitor--application-configuration)
  - [i3blocks Configuration](#i3blocks-configuration)
- [Dependencies](#dependencies)
- [Example Playbook](#example-playbook)
- [License](#license)
- [Author Information](#author-information)

## Requirements

None.

## Role Variables

All variables are optional and have sensible defaults defined in `defaults/main.yml`. The role is designed to be highly modifiable, particularly for keybinding customization.

### Installation Settings

| Variable | Default | Description |
|----------|---------|-------------|
| `i3wm_install_method` | `"package"` | Installation method for i3wm |
| `i3wm_config_dir` | `"{{ ansible_env.HOME }}/.config/i3"` | Directory for i3 configuration files |
| `i3wm_config_file` | `"{{ i3wm_config_dir }}/config"` | Path to main i3 config file |

### General Settings

| Variable | Default | Description |
|----------|---------|-------------|
| `i3wm_modifier` | `"Mod4"` | Main modifier key (Mod4 = Super/Windows key) |
| `i3wm_enable_media_keys` | `true` | Enable media key bindings |
| `i3wm_volume_step` | `5` | Volume adjustment step percentage |
| `i3wm_brightness_step` | `5` | Brightness adjustment step percentage |
| `i3wm_disable_beep` | `true` | Disable system beep sound |

### Visual & Layout Settings

| Variable | Default | Description |
|----------|---------|-------------|
| `i3wm_font.family` | `"Hack Nerd Font"` | Font family for i3wm |
| `i3wm_font.size` | `10` | Font size for i3wm |
| `i3wm_focus_follows_mouse` | `true` | Whether focus follows mouse cursor |
| `i3wm_gaps.inner` | `6` | Inner gap size between windows |
| `i3wm_gaps.outer` | `0` | Outer gap size around screen edges |

### Color Themes

The role includes Gruvbox-inspired color themes that can be fully customized:

```yaml
# Window colors - each state has border, background, text, indicator, and child_border
i3wm_colors:
  focused:
    border: "#83a598"
    background: "#282828"
    text: "#d5c4a1"
    indicator: "#b8bb26"
    child_border: "#83a598"
  # ... (other window states)

# Status bar colors
i3wm_bar_colors:
  background: "#282828"
  statusline: "#d5c4a1"
  separator: "#665c54"
  # ... (workspace states)
```

### Keybinding Configuration

This role provides extensive keybinding customization through several variable groups:

#### Application Execution Keybindings

Control which applications can be launched with keyboard shortcuts:

```yaml
i3wm_keybindings_exec: []
# Example:
# i3wm_keybindings_exec:
#   - key: $mod+Return
#     exec: "i3-sensible-terminal"
#   - key: $mod+d
#     exec: "rofi -show drun"
```

#### Window Focus Navigation

Default vim-style navigation keybindings (fully customizable):

```yaml
i3wm_keybindings_focus:
  - key: $mod+h
    action: "focus left"
  - key: $mod+j
    action: "focus down"
  - key: $mod+k
    action: "focus up"
  - key: $mod+l
    action: "focus right"
  - key: $mod+space
    action: "floating toggle"
  - key: $mod+f
    action: "fullscreen"
  # ... (additional focus keybindings)
```

#### Window Movement

Move windows and containers with customizable keybindings:

```yaml
i3wm_keybindings_move:
  - key: $mod+Shift+h
    action: "move left"
  - key: $mod+Shift+j
    action: "move down"
  - key: $mod+Shift+k
    action: "move up"
  - key: $mod+Shift+l
    action: "move right"
  # ... (additional movement keybindings)
```

#### Layout Control

Control window layouts and container behavior:

```yaml
i3wm_keybindings_layout:
  - key: $mod+m
    action: "split h"
  - key: $mod+n
    action: "split v"
  - key: $mod+s
    action: "layout stacking"
  - key: $mod+t
    action: "layout tabbed"
  - key: $mod+w
    action: "layout toggle split"
```

#### System Control

Essential system keybindings for i3wm control:

```yaml
i3wm_keybindings_system:
  - key: $mod+Shift+q
    action: "kill"
  - key: $mod+Shift+r
    action: "restart"
  - key: $mod+Shift+e
    action: 'exec --no-startup-id i3-nagbar -t warning -m "You pressed the exit shortcut. Do you really want to exit i3? This will end your X session." -B "Yes, exit i3" "i3-msg exit"'
```

#### Resize Mode Configuration

Configure resize mode with entry key and resize keybindings:

```yaml
i3wm_keybindings_resize:
  enter_key: $mod+r
  bindings:
    - key: h
      action: "resize shrink width 10 px or 10 ppt"
    - key: j
      action: "resize grow height 10 px or 10 ppt"
    - key: k
      action: "resize shrink height 10 px or 10 ppt"
    - key: l
      action: "resize grow width 10 px or 10 ppt"
    - key: Escape
      action: "mode default"
    - key: Return
      action: "mode default"
```

#### Quick Resize (Outside Resize Mode)

Direct resize keybindings without entering resize mode:

```yaml
i3wm_keybindings_quick_resize:
  - key: $mod+Control+Up
    action: "resize shrink height 10"
  - key: $mod+Control+Down
    action: "resize grow height 10"
  - key: $mod+Control+Left
    action: "resize shrink width 10"
  - key: $mod+Control+Right
    action: "resize grow width 10"
```

#### Media/Hardware Control

Hardware function key mappings (controlled by `i3wm_enable_media_keys`):

```yaml
i3wm_keybindings_media:
  volume_up:
    key: XF86AudioRaiseVolume
    action: "exec --no-startup-id pactl set-sink-volume @DEFAULT_SINK@ +{{ i3wm_volume_step }}%"
  volume_down:
    key: XF86AudioLowerVolume
    action: "exec --no-startup-id pactl set-sink-volume @DEFAULT_SINK@ -{{ i3wm_volume_step }}%"
  volume_mute:
    key: XF86AudioMute
    action: "exec --no-startup-id pactl set-sink-mute @DEFAULT_SINK@ toggle"
  # ... (additional media keys)
```

### Workspace Configuration

Define custom workspaces:

```yaml
i3wm_workspaces:
  - workspace:
    id: 1
    name: "1"
  - workspace:
    id: 2
    name: "2: Web"
  # ... (customize as needed)
```

### Monitor & Application Configuration

Configure multi-monitor setups and application assignments:

```yaml
# Monitor configuration
i3wm_monitors: []
# Example:
# i3wm_monitors:
#   - monitor:
#     id: 1
#     output: "DP-1"
#     mode: "1920x1080"
#     pos: "0x0"
#     rotate: "normal"
#     workspaces: [1,2,3,4,5]

# Application workspace assignments
i3wm_applications: []
# Example:
# i3wm_applications:
#   - application:
#     class: "Firefox"
#     name: "firefox"
#     workspace: 1
#     on_startup: false
```

### i3blocks Configuration

Customize status bar appearance:

```yaml
i3wm_i3blocks_colors:
  primary_bg: "#3a4553"
  secondary_bg: "#282828"
  accent_bg: "#2e3440"
  text_color: "#ebdbb2"
  separator_color: "#2e3440"
```

## Dependencies

None.

## Example Playbook

### Basic Usage

```yaml
- hosts: all
  roles:
    - pluggero.i3wm
```

### Advanced Configuration

```yaml
- hosts: all
  roles:
    - pluggero.i3wm
  vars:
    # Custom modifier key (Alt instead of Super)
    i3wm_modifier: "Mod1"
    
    # Add application launchers
    i3wm_keybindings_exec:
      - key: $mod+Return
        exec: "alacritty"
      - key: $mod+d
        exec: "rofi -show drun"
      - key: $mod+shift+f
        exec: "firefox"
    
    # Custom focus keybindings (arrow keys only)
    i3wm_keybindings_focus:
      - key: $mod+Left
        action: "focus left"
      - key: $mod+Down
        action: "focus down"
      - key: $mod+Up
        action: "focus up"
      - key: $mod+Right
        action: "focus right"
      - key: $mod+space
        action: "floating toggle"
    
    # Custom workspace names
    i3wm_workspaces:
      - workspace:
        id: 1
        name: "1: Terminal"
      - workspace:
        id: 2
        name: "2: Web"
      - workspace:
        id: 3
        name: "3: Code"
    
    # Application assignments
    i3wm_applications:
      - application:
        class: "Firefox"
        name: "firefox"
        workspace: 2
        on_startup: true
```

## License

MIT / BSD

## Author Information

This role was created in 2025 by Robin Plugge.
