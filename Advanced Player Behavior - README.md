# Advanced Player Behavior

Version `d2026.11.10.4`

Advanced Player Behavior (APB) is a set of player-control systems for GAMMA:
tactical stances on the Crouch and Jump keys, a weapon that lowers itself in
peace and raises itself for danger, automatic visor wiping, a context-aware
Headlamp key, remembered Free Zoom in iron-sight ADS, an eight-slot Suit Setups
wheel and manager, and a reload workflow for GAMMA Mags Reloaded with an
ammunition wheel and Animated Magazine Loading field loading. It adds no
animations of its own and no new keys apart from the optional Suit Setups
wheel key. Stances, weapon readiness, visor wiping, contextual equipment, Free
Zoom and Reload-hold field loading can each be turned off in MCM. Suit Setups
and the Reload-tap ammunition wheel, with its `g_auto_reload` override, have no
off switch and stay active while APB is installed.

## What it does

### Stances (Crouch and Jump)

| Starting stance | Crouch tap | Crouch hold | Jump tap | Jump hold | Sprint |
| --- | --- | --- | --- | --- | --- |
| Standing | Crouch | Crouch while held | Normal jump | Normal jump or ledge grab | Normal |
| Crouching | Prone | Prone while held | Jump, land crouched | Stand up | Stand, then sprint |
| Prone | Nothing | Nothing | Jump, land prone | Crouch | Stand, then sprint |

"Prone" is Anomaly's own lowest stance (Crouch + Walk), not a new belly-down
animation. The tap/hold boundary is 250 ms by default. A stance change that is
blocked (for example standing up under a low ceiling) is cancelled, not
queued, and Walk cannot change your stance by accident while you are crouched
or prone.

While stance controls are on, APB holds the game's `g_crouch_toggle` and
`g_walk_toggle` options off, because the stance logic needs hold behaviour. It
remembers your own values the first time it does this, keeps that copy in
`axr_options.ltx` so it survives restarts, and puts them back when stance
controls are off: at once when you turn them off in a game, or when the next
save or new game loads if you turned them off at the main menu.

### Weapon readiness

- Supported weapons lower at the first safe moment in peace, including after a
  weapon switch.
- They raise at once for Aim or Fire, human combat, mutants that are attacking
  you, a hit from a hostile, or confirmed weapon fire. When the last of those
  clears, the weapon stays up for the calm delay (two seconds by default) and
  then lowers.
- Safemode (B) in peace raises the weapon; pressing it again lowers it. In
  combat, attempts to lower are ignored.
- Empty hands stay empty and unsupported items behave normally. APB never
  delays, replays or invents an Aim or Fire press.
- A lowered weapon is still a drawn weapon, so GAMMA's disguise scoring treats
  it the same as a raised one.

APB also makes every weapon move into and out of the lowered position about
twice as fast. It does this by changing `weapon_lower_speed` in
`[default_weapon_params]` from GAMMA's 0.3 to 0.15. This change is global: it
also affects GAMMA's own Safemode lowering, and it stays in effect when
adaptive readiness is turned off. Weapons whose own config sets
`weapon_lower_speed` keep their own value.

### Visor wiping

With a mask that supports wiping, APB starts the game's own wipe once the visor
is wet enough, and counts the wipe as done only when the wipe animation has
played and the wetness has actually dropped. Combat and sprinting do not block
a wipe the game would otherwise allow. Defaults: wipe at 30% wetness, re-arm
at 10%, at most one automatic wipe every 30 seconds.

### Headlamp key (contextual equipment)

The Headlamp key does different things depending on what you are doing when
you press it:

| When you press it | Tap | Hold |
| --- | --- | --- |
| Not aiming, night vision off | Headlamp on/off (animated) | Night vision on (animated) |
| Not aiming, night vision on | Night vision brightness (Beef's NVG) | Night vision off (animated) |
| Aiming through the main sight | Next weapon function mode | Next 3DSS reticle (Mark Switch), if the sight has more than one |
| Aiming through the alternative sight, laser shown | Back to the main sight | Laser colour: red, green, blue |
| Aiming through the alternative sight, laser hidden | Back to the main sight | Nothing |

The game's own Night Vision, Weapon Function, Mark Switch and magnifier keys
keep working.

### Free Zoom in iron-sight ADS

The mouse wheel sets Free Zoom to normal or maximum while aiming without a
magnified optic (iron sights, red dots, non-optical alternative sights). APB
remembers your last choice in the save, instead of every aim starting at
maximum. Optical and dynamic scopes, and Magnifier Switch's wheel control, keep
priority over it.

### Suit Setups

- **Manager:** right-click any outfit you own and open the setup manager. It
  lists eight slots, each showing the saved suit's icon and the setup name,
  coloured by status: blue equipped, yellow partial, red unavailable, plain
  ready. Empty slots are blank. Save stores what you are wearing in the
  selected slot, asking first if it already holds a setup; Apply puts the
  selected setup on; Delete removes it. Click the selected setup's name to
  rename it.
- **Wheel:** hold the Suit Setup wheel key (unbound by default) to open the
  eight-slot wheel. Names are coloured by status as in the manager, and the
  aimed setup's name and status show in the centre.
  Left-click a ready or partial setup to apply it. The cursor starts in the
  centre, where nothing is aimed; releasing the key without clicking closes
  the wheel and changes nothing. Slow motion while the wheel is open follows Haru's Quick Action
  Wheel settings.
- Applying a setup swaps the exact items you saved, using FDDA Redone's
  equipment animations, and undoes the swap if a step fails.
- Suit Setups replaces G_FLAT's Remember Belt Items, which GAMMA ships: the
  automatic per-outfit belt memory and its MCM page are gone while APB is
  installed. Belt memory already in your save is left untouched.

### Reloading with GAMMA Mags Reloaded

- **Tap Reload** opens the ammunition wheel. A single real alternative is
  shown, not chosen for you. The ammunition you already have loaded stays
  marked, and you can pick it to top up while the weapon is not full. It is
  greyed out only when picking it would add nothing: a full tube or internal
  magazine, or, on a magazine weapon, no carried magazine of that type with
  more rounds than the one inserted. On break-action shotguns (BM-16, TOZ-34)
  a type is greyed while you carry only one shell of it, because the game's
  reload takes two and would load another type instead. On a weapon without a
  detachable magazine, if you carry no other ammunition it accepts, the tap
  reloads straight away.
- Magazine weapons reload the exact magazine you picked; GAMMA Mags Reloaded
  and Dynamic Reload Speeds still own the swap itself and its timing. Internal,
  tube-fed, bolt-action and magazine-less weapons use their normal reloads.
- **Hold Reload** (with Animated Magazine Loading installed) runs a field-loading
  batch over the magazines in your loadout, even with empty hands. Each loadout
  magazine can be assigned an ammunition type from its right-click menu; field
  loading never substitutes another type.
- Weapon Parts Overhaul jams still clear on a Reload tap.
- The game's own automatic reload (`g_auto_reload`) is switched off and kept
  off while the reload system is active, because it would bypass the
  ammunition choice. See Uninstall for turning it back on.

What APB deliberately leaves alone: disguise calculations, the game's own
weapon, reload, unjam, visor, headlamp, night vision, scope, laser, FDDA and
magazine animations, and Aim and Fire input. It ships no textures, sounds or
models.

## Requirements

- S.T.A.L.K.E.R. Anomaly 1.5.3.
- GAMMA, modpack definition 920 (built and checked against it; only briefly
  tested in game, see Known issues), with the Modded Exes that GAMMA installs.
  APB uses the Modded Exes input and weapon callbacks that GAMMA already
  depends on.
- Part of GAMMA and required: MCM, Haru's Quick Action Wheel, FDDA Redone
  (with LAM2), Weapon Parts Overhaul.
- **Not part of GAMMA and required:** GAMMA Mags Reloaded 1.05, by Priler,
  released by andtheherois:
  https://github.com/andtheherois/GAMMA-Mags-Reloaded/releases/tag/1.05
  (file `GAMMA.Mags.Reloaded-1.05.zip`). It is an updated fork of Anomaly
  Magazines Redux ("Mags Redux") by Arti and RavenAscendant. GAMMA's own mod
  list carries Mags Redux as an optional mod that is off by default (in MO2,
  `207- Anomaly Magazines (Disable G.A.M.M.A. Unjam Reload) - RavenAscendant`;
  older installs may also have
  `207- Mags Redux (Disable G.A.M.M.A. Unjam Reload) - RavenAscendant`); the
  two ship magazine scripts under the same file names, so enable Mags Reloaded
  and leave Mags Redux off.
- **Must be disabled:** G.A.M.M.A. Unjam Reload on the same key, which GAMMA
  enables by default. See Compatibility and conflicts.
- Not part of GAMMA and optional:
  - Animated Magazine Loading
    (https://www.moddb.com/mods/stalker-anomaly/addons/animated-magazine-loading-beta),
    for Reload-hold field loading. Without it, Reload hold does nothing extra.
    Tested with 0.6.0 Beta and Update 10 (its installer says 0.9 Beta). In
    Update 10's installer pick Animated Magazine Loading, the textures for
    your renderer and **Mags Reloaded**, and leave its Skill Expansion patch
    off: that patch replaces AML's script and has not been tested with APB.
  - Dynamic Reload Speeds 1.0 by Verdatim
    (https://github.com/Verdatim25/Dynamic-Reload-Speeds/releases/tag/Dynamic_reload_speeds_v1.0),
    whose reload timing APB leaves in charge of the magazine swap. Later
    releases have not been tested.
- Part of GAMMA and optional (each missing one turns only its own feature into
  a no-op): Beef's NVG, Boomsticks and Sharpsticks with Laser Settings, Free
  Zoom, 3DSS for GAMMA (Magnifier Switch), Mark Switch, Lower Weapon Sprint
  Optimized, Ledge Grabbing.

## Installation

Install the archive with Mod Organizer 2 and enable it. Disable
G.A.M.M.A. Unjam Reload on the same key and bind Reload back to your key (see
Compatibility and conflicts). Restart the game after installing or updating.

**MO2 priority matters for one file.** APB overwrites two files of
G_FLAT's Remember Belt Items, which GAMMA ships: `remember_outfit_belt.script`
and `remember_outfit_belt_mcm.script`. Advanced Player Behavior must have the
higher priority, which means it must sit **below** G_FLAT's Remember Belt
Items in MO2's left pane. A newly installed mod goes to the bottom, so this is
normally already true. If G_FLAT's copy wins, Suit Setups stays off, the rest
of APB still works, and the game log names the fix. APB overwrites no other
file.

When it loads, the game log (`xray_<user>.log` in Anomaly's `appdata\logs`
folder) contains these lines (the magazine line has more fields after
`build=`):

```
[ADVANCED-PLAYER-BEHAVIOR] installed | build=d2026.11.10.4 | router=deadline/event | input=single-composable | systems=contextual-L,stance,readiness,freezoom,visor,suit-setups | inactive=none | mags=vendored
[ADVANCED-PLAYER-BEHAVIOR:SUIT-SETUPS] installed | build=d2026.11.10.4 | schema=1 | slots=8 | scheduler=shared
[ADVANCED-PLAYER-BEHAVIOR:MAGS-COMPAT] installed | build=d2026.11.10.4 | ...
```

If one system fails to start, APB logs why, lists it after `inactive=`, and
runs the others. If GAMMA Mags Reloaded changes in a way APB does not
recognise, the log has one `Mags API mismatch` line naming each hook that
could not be installed.

## Compatibility and conflicts

- **G.A.M.M.A. Unjam Reload on the same key (on by default in GAMMA): you
  must disable it to use APB's reload controls.** Each time a game starts,
  that mod moves the game's Reload action to F10 and gives your Reload key an
  "Unjam Weapon" action instead. When you press that key, it unjams a jammed
  weapon itself, or otherwise presses Reload for you on F10. APB's reload
  system only reacts to a Reload key you are actually holding, so with that
  mod enabled the Reload-tap ammunition wheel and Reload-hold field loading
  never open. Reload then does GAMMA Mags Reloaded's ordinary reload, which
  picks the magazine for you, and jams are cleared by that mod rather than
  through APB. Nothing is handled twice and nothing breaks; you only lose APB's reload
  controls. Because APB requires GAMMA Mags Reloaded, this applies to every
  supported setup; GAMMA's own mod list likewise names Mags Redux "Disable
  G.A.M.M.A. Unjam Reload". To switch over:
  1. Disable G.A.M.M.A. Unjam Reload on the same key in MO2.
  2. Start the game, open the controls options and bind **Reload** to your
     key again. The game keeps the F10 binding that mod wrote after it is
     disabled.

  That mod ships its own copy of Weapon Parts Overhaul's jamming script, so
  disabling it returns jamming to Weapon Parts Overhaul's own version. That is
  the setup APB is tested with.
- **G_FLAT's Remember Belt Items:** replaced, as described under Installation.
  Its automatic belt memory and MCM page are unavailable while APB is
  installed.
- **Boomsticks and Sharpsticks laser control (Laser Settings):** while
  Contextual Equipment is on, APB takes over the Headlamp key from BAS's laser
  script. It switches off three of BAS's own callbacks: the hold-L laser
  toggle, the 250 ms loop that reapplies your saved lasers, and the loop that
  hides the laser in ADS. APB then decides when the laser is visible and sets
  its colour itself. Your saved BAS laser preferences are not rewritten, and
  BAS's `no_laser_alt_aim` option is respected. Turning Contextual Equipment
  off gives the three callbacks back to BAS (they then run after other mods'
  callbacks of the same kind).
- **Headlamp key:** APB wraps GAMMA's headlamp toggle so a tap or hold on the
  Headlamp key does only what the table above says.
- **Ledge Grabbing:** a Jump press from crouch or prone is offered to Ledge
  Grabbing first. While APB sends its own jump input, it briefly sets Ledge
  Grabbing's key to none so that input cannot start a climb, then puts your
  key back. The copy of Ledge Grabbing in Jack Harris's Anti Lag, which GAMMA's
  development branch enables, looks for a ledge only while it sees Jump held,
  so APB also tells it when that Jump press starts and when you let go.
- **Walk key while prone:** the game has no way to set a stance directly. Its
  lowest stance is the Crouch and Walk actions held together, so APB keeps
  Walk held while you are prone, and the game can only be told that through
  your Walk key (Shift by default). So that other scripts do not read this as
  Shift being held, APB wraps the game's `bind_stalker_ext.on_key_press` and
  `on_key_hold` and hides its own Walk from them unless you really are holding
  the key.
- **Lower Weapon Sprint Optimized:** APB tells it when a weapon was lowered by
  readiness, so the two do not fight over the lowered state.
- **Magnifier Switch:** its wheel handler always runs before APB's Free Zoom
  handler, and a wheel notch it used is never also used for Free Zoom.
- **Animated Magazine Loading Update 10:** each time a load starts, it reads
  a camera setting, `aml_camera_effects`, from Mags Reloaded's MCM page.
  Mags Reloaded 1.05 has no such setting, and its lookup then raises an error
  inside AML's own update. With Mags Reloaded 1.05 it is APB that starts
  AML's loads, so without a guard the first animated load would crash the
  game. APB reads an AML setting that Mags cannot resolve as unset instead,
  so AML leaves its camera movement off. Mags' own settings are untouched.
- **GAMMA Mags Reloaded:** APB's reload system replaces 34 functions of Mags
  Reloaded and its companions, 43 with Animated Magazine Loading installed
  (39 once a Mags release provides its own Animated Magazine Loading
  interface); the full list is below. Most of them
  call the original. A Mags update that renames or removes one of them is
  reported by the `Mags API mismatch` log line rather than failing silently.
- **Engine options:** APB sets `g_crouch_toggle` and `g_walk_toggle` (while
  stance controls are on) and `g_auto_reload` (while the reload system is
  active). Other mods that set the same options will be overruled.
- No other known conflicts. APB's own files are new except for the two
  Remember Belt Items files.

### Functions APB replaces in GAMMA Mags Reloaded and its companions

- `magazines`: `on_key_press`, `set_mag_data`, `room_in_pouch`,
  `loadout_precondition`, `loadout_func`, `retain_magazine`,
  `retool_mag_functor`, `eject_magazine`, `timer_eject_magazine`,
  `delay_load_weapon`, `load_weapon`.
- Only with Animated Magazine Loading installed: `magazines.load_one_round`,
  `unload_one_round`, `do_interrupt` and `on_item_drag_dropped`, and
  `magazines_mcm.get_config` (an `aml_` setting Mags cannot resolve reads as
  unset; every other setting is passed on unchanged). While Mags Reloaded
  lacks its own Animated Magazine Loading interface (true of 1.05), also
  `magazines.ensure_load_holster`, `is_loading_mag`, `do_interrupt_magload`
  and `func_unload_ammo`.
- `magazine_binder`: `set_data`, `toggle_carried_mag`, `validate_loadout`,
  `room_in_pouch`, `update_loadout_slots`.
- `mags_patches`: `set_data`, `toggle_carried_mag`, `room_in_pouch`,
  `is_carried_mag`, `eject`, and the ammunition wheel's
  `UIWheelAmmoWuut.OnAmmo`, `SwitchNextAmmo` and `Close`.
- `magazines_loot.set_data`, `ui_item.item_description`.
- Weapon Parts Overhaul: `zzzz_arti_jamming_repairs.custom_disassembly_weapon`.
- Dynamic Reload Speeds: `ver_dynamic_reload_speeds.change_reload_animation_speed`
  and `remove_sound`.
- Anomaly and GAMMA scripts: `level_input.on_key_press`, `on_key_hold` and
  `on_key_release`, and the globals `alife_clone_weapon` and
  `CHudItem__OnMotionMark`.

## Options

Two MCM pages.

**Advanced Player Behavior**

| Section | Setting | Default |
| --- | --- | --- |
| Stance Controls | Enable stance controls | On |
| Stance Controls | Hold threshold | 250 ms (150-500) |
| Adaptive Weapon Readiness | Enable adaptive readiness | On |
| Adaptive Weapon Readiness | Calm delay | 2000 ms (0-10000) |
| Smart Visor Wiping | Enable smart visor wiping | On |
| Smart Visor Wiping | Wipe trigger | 30% (10-100) |
| Smart Visor Wiping | Re-arm level | 10% (0-40, always at least 5 below the trigger) |
| Smart Visor Wiping | Minimum interval | 30 s (5-120) |
| Contextual Equipment | Enable contextual equipment controls | On |
| Contextual Equipment | Equipment hold threshold | 350 ms (200-600) |
| Unscoped ADS Free Zoom | Enable unscoped ADS Free Zoom | On |
| Suit Setups | Suit Setup Wheel key | Unbound |

**Mags: AML Field Loading**

| Setting | Default |
| --- | --- |
| Enable Reload-hold field loading | On |
| Animate mismatched chamber ejection | On |
| Reload hold threshold | 0.45 s (0.2-1.5) |
| AML loading animation speed | 1.0 (0.5-2.0) |
| AML unloading animation speed | 1.0 (0.5-2.0) |

The page also explains the reload rules in full.

## Adding or removing mid-save

- **Adding:** designed to be safe, not yet tested in game. Suit Setups start empty, Free Zoom starts at maximum
  until you choose otherwise, and belt memory saved by G_FLAT's Remember Belt Items is left in
  the save untouched.
- **Removing:** turn Stance Controls off first (see Uninstall). APB's own save
  data (Suit Setups, the Free Zoom choice) stays in the save and is ignored.
  APB also stores a few extra fields in GAMMA Mags Reloaded's magazine records
  (loadout order, ammunition assignment and weapon slot); Mags Reloaded 1.05
  does not read them, so they are inert. Removing APB mid-save has not yet been
  tested in game; keep a save from before you remove it.

## Uninstall

1. In MCM, open Advanced Player Behavior and turn **Enable stance controls**
   off. This puts your own `g_crouch_toggle` and `g_walk_toggle` values back:
   at once if you are in a game, or as soon as you next load a save or start a
   new game if you are at the main menu, so do that once before quitting. If
   you skip this step, set Crouch and Walk toggle again in the game's control
   options after removing APB.
2. Quit the game and disable or remove Advanced Player Behavior in MO2.
   G_FLAT's Remember Belt Items is active again from the next start.
3. Engine automatic reload stays off after removal. To turn it back on, open
   the console (`~`) and enter `g_auto_reload 1`.

Left behind and harmless: APB's MCM values and, if step 1 was skipped or no
game was loaded after it, its `[advanced_player_behavior_console_snapshot]`
section in `axr_options.ltx`; APB's data in your saves.

## Known issues

- Engine automatic reload is forced off while the reload system is active, and
  stays off after removal until you turn it back on.
- The faster lowering speed applies to every weapon that does not set its own,
  including when adaptive readiness is off.
- While Contextual Equipment is on, BAS's own laser key, laser reapplication
  and ADS laser hiding are off (see Compatibility and conflicts).
- A key rebound in the game's own controls menu, rather than through MCM, is
  picked up by APB at the next save load, level change, death or MCM change.
- The Russian text has not been checked in game.
- This build adds the guard for Animated Magazine Loading Update 10 and has
  had one short scripted run in game with Update 10 installed, on a save that
  already used APB, with no errors from APB in the log. A test script started
  one inventory drag-load, and a real Reload hold field-loaded two loadout
  magazines; AML's animation played, AML read its camera setting once per
  magazine and APB caught the error each time (logged once), and every
  magazine was filled. Update 10's Skill Expansion patch was not
  tried. The build before it, otherwise the same, had one short scripted run
  of its Suit Setups manager: real mouse clicks selected a setup by its name
  and opened the name box with a second click on it, typing and Enter renamed
  it, Esc closed the name box, Save on a saved setup asked before overwriting
  it, and Apply switched to a setup with a suit only. The build before that,
  whose wheel was the same, had one such run in which the wheel was aimed at
  a ready setup, an unavailable one and its centre, and its manager saved and
  renamed a setup with real clicks and typing. The wheel key and the outfit's
  right-click entry were not tried in any of them. The unreleased build
  before that, which still had the old Suit Setups menus, opened the
  Reload-tap ammunition wheel with a real key press. The build before that
  changed how the Free Zoom wheel handler is registered again after an MCM
  change; the step ran without error in a load check, but the mouse wheel was
  not used. Earlier builds, the oldest of them without the
  later ledge-grab and Walk key changes, were tested in game mostly by
  script, with real key presses but a test script placing the character. By
  hand, an earlier build was tried in one session, for a crouched ledge grab,
  and an older copy was played for one session. With the Anti Lag copy of
  Ledge Grabbing swapped into definition 920, a Jump hold climbed from crouch
  and from prone in the scripted tests, and from crouch by hand. GAMMA's
  development branch itself has not been run. A ledge grab from crouch or prone
  has not been confirmed on the standalone Ledge Grabbing that definition 920
  uses.
- While you are crouched or prone, APB takes each press of the Walk key so it
  cannot change your stance by accident. Other scripts and windows do not see
  that press, so with Walk on Shift, shortcuts such as Shift+T and Shift+P
  (take all, put all) in a loot window do nothing until you stand.
- On tube-fed shotguns, if the type you pick runs out before the tube is
  full, the game carries on loading another type you carry. Those last shells
  fire first. GAMMA does the same without APB.

## Changelog

### d2026.11.10.4

- First public release.

## Credits

APB's code is original. It integrates with the following mods through their
public functions and thanks their authors:

- G_FLAT: Remember Belt Items, whose filename APB takes over.
- HarukaSai: Quick Action Wheel. APB's Suit Setups wheel subclasses its
  `haru_arm` classes, and its wheel and manager layouts are derived from
  Haru's layouts.
- Mich and the Boomsticks and Sharpsticks (BAS) team; Borksy, Laser Settings.
- theRealBeef: Beef's NVG.
- SlowVibe: Free Zoom V2; Kute: Free Zoom Rewrite.
- Redotix99, Andtheherois and party50: 3DSS for GAMMA, including Magnifier
  Switch; party_50 and meowie: Mark Switch.
- Spaicrab, Coverdrave and Skieppy: Lower Weapon Sprint Optimized.
- Demonized: Ledge Grabbing.
- Feel_Fried: FDDA; lizzardman: FDDA Redone and LAM2.
- Priler and andtheherois: GAMMA Mags Reloaded, built on Anomaly Magazines
  Redux (Mags Redux) by Arti and RavenAscendant and on wuut's original Anomaly
  Magazines.
- Arti: Weapon Parts Overhaul.
- The authors of Animated Magazine Loading.
- Verdatim: Dynamic Reload Speeds.
- RavenAscendant: MCM.
- The GAMMA team and the Anomaly team.

## Licence

The mod's own code is licensed under CC BY-NC-SA 4.0. See `Advanced Player Behavior - LICENSE.txt`.
