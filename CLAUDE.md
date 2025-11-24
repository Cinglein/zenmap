# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a ZMK (Zephyr Mechanical Keyboard) firmware configuration repository for the Corne-ish Zen V2 low-profile wireless mechanical keyboard. The firmware is built remotely using GitHub Actions rather than locally.

## Architecture

### Build System
- **Build process**: Firmware is compiled remotely via GitHub Actions, not locally
- **Build configuration**: `build.yaml` specifies both left and right board targets (`corneish_zen_v2_left`, `corneish_zen_v2_right`)
- **GitHub Actions**: `.github/workflows/build.yml` triggers builds on push/PR/manual dispatch using a reusable workflow from `LOWPROKB/zmk`
- **Firmware outputs**: Builds produce two `.uf2` files (one per keyboard half) available as artifacts in GitHub Actions

### Configuration Files
- **Keymap**: `config/corneish_zen.keymap` - Device tree format defining keyboard layout, layers, behaviors, and macros
- **Settings**: `config/corneish_zen.conf` - Configuration options (sleep timeout, USB logging, etc.)
- **Dependencies**: `config/west.yml` - West manifest that pulls ZMK firmware from `zmkfirmware/zmk`

### Keymap Structure (config/corneish_zen.keymap)
The keymap uses ZMK's device tree syntax and contains:
- **Macros**: Custom key sequences (vim commands, window management, multi-key navigation)
- **Behaviors**: Custom hold-tap behaviors like homerow mods (`hm`)
- **Layers**: 4 layers total
  - Layer 0 (QWERTY): Base layer with homerow mods on home row keys
  - Layer 1 (NUMBER): Numbers and window switching macros (Cmd+1-9)
  - Layer 2 (SYMBOL): Special characters and symbols
  - Layer 3 (NAV): Navigation with arrow keys and 5x multipliers
- **Modifier timing**: `&mt` (mod-tap) uses 180ms tapping-term, `hm` uses 150ms

## Development Workflow

### Making Changes
1. Edit `config/corneish_zen.keymap` for keymap changes (layers, keycodes, macros)
2. Edit `config/corneish_zen.conf` for configuration settings
3. Commit and push to trigger automatic firmware build via GitHub Actions
4. Download firmware from Actions > Build > Artifacts > firmware.zip

### Flashing
- Keymap-only changes: Flash left side only
- Config changes: Flash both sides with their respective `.uf2` files

## ZMK Devicetree Syntax Notes

- File uses C preprocessor includes and devicetree syntax
- Bindings format: `<&behavior PARAM1 PARAM2 ...>`
- Key codes: Use ZMK constants like `&kp A`, `&mt LSHFT A`, `&mo 1`
- Macros: Define custom sequences with `macro_press`, `macro_tap`, `macro_release`
- Layers accessed via momentary (`&mo N`) or layer-tap (`&lt N KEY`) bindings
