<!--
SPDX-FileCopyrightText: 2026 Dee0n
SPDX-License-Identifier: GPL-2.0-or-later

The front page of github.com/Dee0n/shadPS4-SecondSon-Conduit (branch conduit).
-->

<div align="center">

# shadPS4 · Second Son · Conduit

**Patches for [axylpup's inFAMOUS Second Son build](https://github.com/axylpup/shadPS4) of the shadPS4 emulator.**<br>
Sharp HUD at any render size · fights that cost the GPU 5 ms less · GoldHEN cheats · menus that do not freeze the game.

[![Sharp HUD](https://img.shields.io/github/pulls/detail/state/axylpup/shadPS4/75?label=%2375%20sharp%20HUD&style=for-the-badge)](https://github.com/axylpup/shadPS4/pull/75)
[![Faster fights](https://img.shields.io/github/pulls/detail/state/axylpup/shadPS4/76?label=%2376%20faster%20fights&style=for-the-badge)](https://github.com/axylpup/shadPS4/pull/76)
[![Cheats](https://img.shields.io/github/pulls/detail/state/axylpup/shadPS4/77?label=%2377%20cheats&style=for-the-badge)](https://github.com/axylpup/shadPS4/pull/77)
[![Panels](https://img.shields.io/github/pulls/detail/state/axylpup/shadPS4/78?label=%2378%20panels&style=for-the-badge)](https://github.com/axylpup/shadPS4/pull/78)

**[What is added](#what-is-added) · [Numbers](#numbers) · [Use it](#use-it) · [Cheats](#cheats) · [Limits](#limits) · [По-русски](#по-русски)**

</div>

> [!NOTE]
> This is not a new port. All the heavy lifting - the frame rate, DLSS, mouse aiming, the whole build - is
> [axylpup's work](https://github.com/axylpup/shadPS4) on top of [shadPS4](https://github.com/shadps4-emu/shadPS4).
> This branch only carries a few fixes on top of his v1.1.1, each one sent to him as a pull request. Setup, controls,
> DLSS files and the FAQ are in **[his README](https://github.com/axylpup/shadPS4#readme)**. You need your own copy
> of the game; no game files are here.

## What is added

| | What | How to switch it on | Upstream |
|---|---|---|---|
| 🔤 | **Sharp HUD below 100% render size.** The game draws its HUD at the render size, so at 67% text and the minimap got jagged. The HUD layer is now drawn at the screen's size. | `SHADPS4_HUD_NATIVE=1` | [#75](https://github.com/axylpup/shadPS4/pull/75) |
| ⚡ | **Fights and the chain cost about 5 ms less GPU time per frame.** The game hides dead debris and particles with a NaN position; their vertex shaders then walked the whole light-probe mesh (15 000 steps) for vertices that are never drawn. | `SHADPS4_PROBE_WALK_NAN_EXIT=1` | [#76](https://github.com/axylpup/shadPS4/pull/76) |
| 🎮 | **Cheats panel.** GoldHEN cheat files are listed in a panel and switched live. Every cheat is checked against the game's code first; console code caves are moved into the emulator's memory. | **F6**, files in `user\cheats` | [#77](https://github.com/axylpup/shadPS4/pull/77) |
| 🧊 | **Open menus no longer drop the game to 8 FPS.** With the frame-rate cap above the game's rate (cap 75, game at 60) any open panel made the present thread redraw old frames and starve the new ones. | always on | [#78](https://github.com/axylpup/shadPS4/pull/78) |
| 🚪 | **Quit game button in the F3 menu.** Asks the same question as the quit hotkey; handy on a streaming client or a controller. | F3 → Quit game | [#79](https://github.com/axylpup/shadPS4/pull/79) |

Lines for `shadps4-flags-user.txt` (next to `shadPS4.exe`):

```
SHADPS4_HUD_NATIVE=1
SHADPS4_PROBE_WALK_NAN_EXIT=1
```

Both are off unless you add them, so the build behaves like axylpup's without them.

## Numbers

Measured on one PC: Ryzen 7 7700, RTX 5070 Ti, EU dump CUSA00004 with update 1.07, DLSS 4.5.

**Fights** - a minute of chain hits on street props, 60 FPS, GPU time from timestamp queries:

| | before | after |
|---|---|---|
| debris draws, per frame | 2.4 - 2.6 ms | 0.010 ms |
| particle draws, per frame | 3.2 - 3.4 ms | 0.25 ms |
| frame time spent in render passes | 61 - 62% | 10% |

**Menus** - frame-rate cap 75, the game at 60 FPS:

| open | before | after |
|---|---|---|
| nothing | 60 FPS | 60 FPS |
| cheats panel / DLSS panel | 8 - 9 FPS | 60 FPS |

## Use it

There is no download here yet: build the branch and put the exe over axylpup's folder.

1. Install **[axylpup's v1.1.1](https://github.com/axylpup/shadPS4/releases/latest)** and get the game running with it.
2. Build this branch (`conduit`) the way shadPS4 is built on Windows: Visual Studio 2022 with clang-cl, then
   ```
   cmake --preset x64-Clang-RelWithDebInfo
   cmake --build Build/x64-Clang-RelWithDebInfo --target shadps4
   ```
3. Keep a copy of the original `shadPS4.exe`, then replace it with `Build\x64-Clang-RelWithDebInfo\shadps4.exe`.
4. Add the two lines above to `shadps4-flags-user.txt`.

Your saves, settings and DLSS files stay as they are.

## Cheats

1. Get the file for your game from the `json` folder of the
   [GoldHEN Cheat Repository](https://github.com/GoldHEN/GoldHEN_Cheat_Repository), for example `CUSA00004_01.07.json`.
2. Put it into `user\cheats` next to `shadPS4.exe` (create the folder).
3. In the game press **F6**. Tick what you want; **F6** again closes the panel.

What the panel does for you:

- **Checks before it writes.** A cheat is only offered when the game's code at its address is exactly what the file
  expects. A file for another version shows its cheats greyed out instead of breaking the game.
- **Other regions work** when the code is the same: the EU file on the US game, the Asian file on the EU game.
- **Console-only cheats work.** Cheats that park their code in a console's system memory are moved behind the game's
  image and their jumps are rewritten.
- **No two cheats on one instruction.** Switching on a cheat that changes code another cheat already changed is
  refused, with the other cheat's name.
- **Remembers.** Ticked cheats are switched on again at the next start (`user\cheats\enabled.txt`).

The key can be changed in `user\input_config\global.ini` (`hotkey_cheats_panel = f6`).

## Limits

- Everything was run on one PC with an NVIDIA card. The fight fix no longer depends on the exact shader code a GPU
  gets, but AMD and Intel cards were not tried.
- The effect of each cheat in play was not checked one by one.
- The fight fix was timed with the chain on props, not in a D.U.P. fight with concrete (the same shaders draw both).
- No pixel-by-pixel comparison of the picture with the fight fix on and off.
- These are pull requests waiting for review. If axylpup takes them, use his build and forget this branch.

## Credits and licence

- **[axylpup](https://github.com/axylpup/shadPS4)** - the inFAMOUS Second Son build this sits on.
- **[shadPS4](https://github.com/shadps4-emu/shadPS4)** - the emulator.
- **[GoldHEN Cheat Repository](https://github.com/GoldHEN/GoldHEN_Cheat_Repository)** and its authors - the cheat files.
- The patches here were written with [Claude Code](https://claude.com/claude-code).

GPL-2.0-or-later, like shadPS4. Not affiliated with Sony Interactive Entertainment, Sucker Punch Productions,
the shadPS4 team or GoldHEN. inFAMOUS is a trademark of Sony Interactive Entertainment.

## По-русски

Это набор правок поверх сборки [axylpup](https://github.com/axylpup/shadPS4) для inFAMOUS Second Son на ПК. Сама
сборка - его работа; здесь только то, что добавлено сверху и отправлено ему как pull request:

- **Чёткий интерфейс** при рендере ниже 100% (`SHADPS4_HUD_NATIVE=1`).
- **Драки и удары цепью** отнимают у видеокарты примерно на 5 мс меньше на кадр (`SHADPS4_PROBE_WALK_NAN_EXIT=1`).
- **Панель читов GoldHEN** по **F6**: файлы `.json` кладутся в `user\cheats`, каждый чит перед включением сверяется
  с кодом игры.
- **Открытые меню больше не роняют игру до 8 FPS**, когда лимит кадров выше реального FPS.
- **Кнопка Quit game** в меню F3.

Готового архива здесь пока нет: ветку `conduit` нужно собрать и заменить `shadPS4.exe` в папке сборки axylpup.
Установка игры, управление и DLSS описаны в [его README](https://github.com/axylpup/shadPS4#readme).
