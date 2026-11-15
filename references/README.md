# Reference material for the stickman strategy game

Public repositories downloaded into this folder for review. These are reference projects, not merged into a game build. Check each license before copying code or assets into a release.

| Folder | Source | Useful parts | Reuse notes |
|---|---|---|---|
| `madeindjs-stickman` | https://github.com/madeindjs/stickman | Browser SVG editor for prototyping stickman poses and simple animations. | No LICENSE file present; use as a reference until permission/license is confirmed. |
| `Yakir1512-StickMan` | https://github.com/Yakir1512/StickMan | Python skeleton/joint/bone model; useful for planning a stickman rig and poses. | No LICENSE file present; reference only pending permission/license. |
| `natethedrummer-stickman` | https://github.com/natethedrummer/stickman | Closest gameplay example: side-view bases, gold, unit spawning, enemy AI, multiple unit types, ages, and local two-player mode. | No LICENSE file present; study mechanics, do not copy code/art/audio into a release without permission. |
| `lampe-games-godot-open-rts` | https://github.com/lampe-games/godot-open-rts | Full Godot RTS architecture, units, resources, fog of war, minimap, movement, AI, UI, and VFX. | MIT code. Review asset/logo notices separately; this is a 3D space RTS, so its art does not match the stickman game directly. |
| `Lacaedemon-sparta` | https://github.com/Lacaedemon/sparta | Godot tactical combat systems: formations, morale, unit targeting, terrain, replay and deterministic battle simulation. | MIT code. Individual bundled assets have their own CC0/CC-BY notices; retain required credits. Download is a sparse checkout of the useful main-branch source/docs/assets; large generated media branches are excluded. |
| `habi-sticklebacks-manuscript` | https://github.com/habi/sticklebacks-manuscript | CT visualizations and bone morphology that could inspire an aquatic/armored faction. | README states CC BY 4.0 by default and CC0 1.0 for specified file types. Attribute CC BY material; MorphoSource scans have separate access terms. |
| `foresle-matrix-stickers-manager` | https://github.com/foresle/matrix_stickers_manager | Utility to upload/export image folders as Matrix sticker packs for game community promotion. | No LICENSE file present; it contains no game art or gameplay code. |

## Suggested use for this game

1. Use `madeindjs-stickman` and `Yakir1512-StickMan` as visual/rigging references for original troop designs.
2. Use `natethedrummer-stickman` to study the lane-battle loop and unit economy, subject to its missing license.
3. Use `Lacaedemon-sparta` for tactical battle ideas such as formations and morale; follow its MIT and per-asset notices.
4. Use `lampe-games-godot-open-rts` for general RTS architecture if the project uses Godot.
5. Make original troop art, weapons, team colors, and backgrounds for the final game.
