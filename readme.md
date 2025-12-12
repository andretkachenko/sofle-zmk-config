# ZMK config for Sofle
This is my personal config for my Sofle keyboard.  
It's a 34-key layout with 8 layers (4 layers for daily use and 4 more for configuration and rare cases that conflict with the 34 layout, optimized for typing).  

This config is heavily inspired by [urob's config](https://github.com/urob/zmk-config). However, I didn't want to use his helpers, so I used ideas I liked but recreated them using standard Devicetree syntax. This makes my config easier to replicate and modify to fit your needs, providing a lesser barrier to entry.  

Compiling this config was quite a journey, which also required a lot of additional investigation and learning. I decided to make this readme as a possible guide for newcomers who might stumble upon it. If you're one of them, I hope it helps you achieve the result faster than I did.  

This readme will contain three separate sections:  
- Information about the config itself;  
- Information that I feel might help people, who are considering getting the split keyboard or has just got one;  
- Information for those, who try to conjure their own config;  

Please refer to the table of contents below to go to the desired part.  

# Table of Contents
- [About config](#about-config)  
  - [Layers](#layers)  
  - [Combos](#combos)  
  - [Smart layers](#smart-layers)  
  - [Mouse emulation](#mouse-emulation)  
- [Useful info for new to split keyboards](#useful-info-for-new-to-split-keyboards)  
  - [DANGER ZONE](#danger-zone)  
  - [Why 34 key layout, when you have 58?](#why-34-key-layout-when-you-have-58)  
  - [What are home row mods](#what-are-home-row-mods)  
  - [Key positions](#key-positions)  
  - [Bluetooth connectivity](#bluetooth-connectivity)  
  - [Issues when using with RDP/Gnome](#issues-when-using-with-rdpgnome)  
- [Technical info for those, who want to make their own config](#technical-info-for-those-who-want-to-make-their-own-config)  
  - [Splitting keymap to multiple files](#splitting-keymap-to-multiple-files)  
    - [File types](#file-types)  
    - [Importing files into the main keymap](#importing-files-into-the-main-keymap)  
  - [Multiple layouts for one keyboard](#multiple-layouts-for-one-keyboard)  
  - [Logging](#logging)  

# About config
## Layers
Main 4 layers:  
- `base` - main layer. Contains letters, modifiers via [home row mods](#what-are-home-row-mods) and essentials on thumbs - `space`, `tab`, `enter`, `backspace`;  
- `fn` - a combination of symbols on the right side and F1-F12 on the left side. They were on 2 separate layers at the first stages of config development, before I realized that they don't overlap, and I could reduce the number of layers. [HRMs](#what-are-home-row-mods) on the left side, regular modifiers on the right side;  
- `num` - home row becomes numbers, modifiers on centerward columns, top and bottom rows. Can be activated by holding either `b` or `n` (to allow crosshand usage - holding `b` while typing numbers 6-0, holding `n` while typing numbers 1-5) from the `base` layer, or by the [`numword` combo](config/custom/combos.dtsi)) on any layer;  
- `nav` - navigation cluster and arrows from the full-sized keyboard on the left side; media controls and modifiers on the right side; arrows are also duplicated on the bottom row of the right side, mainly to control YouTube videos with one hand. Can be activated by holding `h` from the `base` layer, or by the [`navword` combo ](config/custom/combos.dtsi)) on any layer;  
  
While the previous 4 are used all the time, these 4 are niche and are utilized less frequently. For that reason, they have less accessible ways to activate:  
- `hub` - allows you to activate any of 7 other layers; activated using [`hub layer` combo](config/custom/combos.dtsi))  from any layer, making it also a great bail-out if you accidentally activated something else - go to `hub`, then `base` and try again;  
- `mouse` - mouse emulation; allows you to move cursor, use left, right and middle click, MB4 and MB5 and vertical and horizontal scrolls; activated from `hub` layer or [`mouse layer` combo](config/custom/combos.dtsi);  
- `sys` - configurational layer; allows to control RGB and Bluetooth; only activated from `hub` layer;  
- `uni` - "gaming" layer; regular 65% keyboard, no HRMs, standard layout (except caps lock); arrows on thumb cluster of the right-side part; only activated from `hub` layer;  

## Timeless home row mods
This config uses [urob's implementation of the home row mods](https://github.com/urob/zmk-config/tree/main?tab=readme-ov-file#timeless-homerow-mods), which eliminates all the issues people face when trying to use HRMs.  
You can see the behaviors in [config/custom/hrm.dtsi](custom/hrm.dtsi).  
Usage example: `&hmlf LSHFT F` or look at the [config/layers/sofle/base.dtsi](config/layers/sofle/base.dtsi).  

If you don't know what home row mods are, read the details in [the dedicated section below](#what-are-home-row-mods).  

## Combos
Once again, heavily inspired by [urob](https://github.com/urob/zmk-config?tab=readme-ov-file#using-combos-instead-of-a-symbol-layer), but less extreme and made without his helpers. You can see them in [config/custom/combos.dtsi](https://github.com/andretkachenko/sofle-zmk-config/blob/master/config/custom/combos.dtsi).  
All I need to do in the `sofle.keymap` to make them work - add this line:  
``` c
#include "custom/combos.dtsi"
```
All combos are separated into **fast** and **slow**. All combos that use two neighbouring (horizontally) keys are considered fast - they provide a smaller window between the first and the second keypress, with a longer idle period, ensuring they are not accidentally invoked by a key rollover. I still accidentally hit some of them, but it only happens because I hit 2 buttons with the same finger, and that's not a ZMK issue.  

Every other combo - two buttons on different halves, diagonal combo, vertical combo - is considered slow. They have bigger leniency (slightly bigger time window, slightly smaller prior idle time window). As these combos are harder to click faster, I often couldn't reliably invoke the combo.  

**urob** also uses combos instead of symbol layer, but I decided against this idea due to the issues when using from the RDP (see [section below](#issues-when-using-with-rdpgnome)).  

## Smart layers
These aren't just inspired by **urob** - they are his implementation.  
`numword` enables the `num` layer and keeps it active until the `&trans` keypress - which means, this key hasn't been bound in this layer. After that, the `num` layer is disabled in favor of the `base` layer.  
`smart mouse` acts the same way, but for the `mouse` layer.  
[Read more details from urob](https://github.com/urob/zmk-config?tab=readme-ov-file#smart-layers-and-other-gimmicks).  

## Mouse emulation
Mouse emulation has been finished and merged into the **main** branch ([PR](https://github.com/zmkfirmware/zmk/pull/2477)).  

It works perfectly, but you might need to tweak speed configurations a bit. I had some issues while tinkering with more advanced configurations, and one of the moderators in the Discord server gave me his configuration as an example. I quite liked it, so I incorporated it into my config.  

I also used **urob**'s [zmk-auto-layer](https://github.com/urob/zmk-auto-layer) to make my mouse layer into an auto layer, and also to add numword. Can't say that this is a game changer, and I might even revert it back to keep my config simpler and without dependencies, but it's neat and might be up someone's alley.  

# Useful info for new to split keyboards
I had to spend a lot of time to find out these bits of knowledge. I hope this helps someone to decrease the steep entry barrier.  

## DANGER ZONE
**These are 2 things each owner of split keyboards should know from the start.**  
That's why I put it at the top of this section - this information isn't common knowledge, yet it may cost you a keyboard. I had to solder a new controller because I fried the first one (I knew these dangers already at that time, but didn't follow strictly enough to avoid problems).  
- **DO NOT plug/unplug the TRRS cable when the USB cable is connected.** *This can short circuit your controller*, requiring quite pricey repair. In most scenarios, you don't have to unplug it at all - even when reflashing the firmware, you can keep this cable as-is. So I suggest you plug it once, and stop touching it; treat it as a single unit instead of two halves. If you need to pack it for travel - first unplug USB, then TRRS. When connecting to the computer - first plug in TRRS, then USB.  
- If your keyboard supports both wired and wireless connection, make sure to **not use both at the same time** - *you can destroy batteries*. If you want to use it wirelessly - unplug USB, unplug TRRS, switch power to wireless mode on both halves and then use it. If your keyboard doesn't have a power switch - either unplug the batteries and use the keyboard in a wired mode, or avoid using a wired connection at all.  

## Why 34 key layout, when you have 58?
There's an idea that when we have to move our entire hand to reach any key, we are putting strain on it which it isn't supposed to do, damaging it.  
With a 34-key layout, you have only those keys, which are only 1 unit apart from resting positions for your fingers.  
- Number row forces you to move your wrist to reach it, so it's removed;  
- The outer column, which you're supposed to click using your pinkies, is removed to reduce strain on your weakest fingers;  

This layout is impossible without the extensive use of layers. When we removed the outer column, we were left without modifiers - `Shift`, `Ctrl`, `Win`, `Alt` - which are essential when you do any kind of work on your computer. There are two ways to tackle it:  
- Home row mods - when a button from the home row is clicked, it behaves as a letter, like it is on a regular keyboard. However, when you hold it, instead of typing the letter until you let it go, the keyboard instead sends the modifier assigned to the key. For example, the `f` letter acts as `Left Shift` when held. It takes some time to get used to, but for me now it feels like second nature, and I feel discomfort when I have to use a keyboard without this feature. But, there is a possible issue when you receive misfires - `fj` instead of `J`, or triggering Start Menu with `Win` when you want to type `a`, etc. You can read how to solve it in [the home row mods section below](#what-are-home-row-mods);  
- Move modifiers to the combos and thumb cluster - I've seen some layouts do this, and people like them. In theory, that could also work for me well, but I discovered **urob**'s timeless home row mods early and liked them too much to consider this option. Just know, that there are small layouts without `HRMs`, and if you are interested, you just need to do some digging for inspiration;  

I was interested in trying 34 key keyboards even before I decided to spend my money to try custom keyboards. But I was concerned that I may not like it, or that it won't be enough for me (I'm gaming as well, and this is one of the scenarios, where you need more dedicated keys instead of less). So I decided to go with a Sofle as my first split keyboard. After that, I changed my layout iteratively, trying out different ideas and seeing if it gave me better comfort or not.  

> Small keyboard layouts turned out to be a detriment in any non-casual games. For this reason, I created a dedicated `uni` layer, which operated like a more common nowadays 65% keyboard, just split into two.  

## What are home row mods?
> You can read more detailed explanation [here](https://precondition.github.io/home-row-mods)  

The idea is simple - when you tap a button, you receive its standard key press; when you hold it, you receive a modifier.  
At first glance, it sounds like an ideal solution - the letter button doesn't need the `hold` behavior (when typing), modifier button doesn't need the `tap` behavior. You can combine them without an overlap. Great, right?  

Yes, but there's a catch. You need to properly define the timings between the button being `pressed` and `held`. Too little - and you will receive modifiers when typing instead of your letter. Too big - you will have to wait for ages with a pressed button to receive the modifier.  
This is the reason why a lot of people come to the conclusion that this is an idea good in theory, but bad in practice.  

Lo and behold, urob's solution for this conundrum - [timeless home row mods](https://github.com/urob/zmk-config/tree/main?tab=readme-ov-file#timeless-homerow-mods). You can read the details in his readme, but the gist is - same-hand key presses are considered taps when pressed within a certain threshold (`require-prior-idle-ms`). For same-hand operations, you just need to hold it longer. He also provides ways to tweak the configuration if you still receive any kind of misfires. I needed some tweaking, but after that - it feels like the best feature of this keyboard: the first driving point for me to try an unorthodox keyboard was a pain in my left pinkie for holding `Shift` and `Ctrl`. Now it's just a distant memory for me.  

**urob**'s config extensively uses his homemade helpers. I didn't want to have additional dependency, so I recreated them using simple C macro in the config. 
You can see my configuration in [`config/custom/hrm.dtsi`](config/custom/hrm.dtsi) and the usage in [`config/layers/sofle/base.dtsi`](config/layers//sofle/base.dtsi).  

## Key positions
The official explanation for the key positions used in the ZMK explains indexing using only 0 and 1 indexes. The question remains after this example of how it works for a split keyboard - does it have indexes going left-to-right as if it's a single piece, or have separate indexes for each half with some kind of prefix explaining which half is it, etc.  

Turns out that it works as if it's a single-piece keyboard.  
So, for my Sofle keyboard, that means the first row has indexes from 0 to 11, the second from 12 to 23, etc.  

The tricky part is that encoders are also clickable, so they should be counted as well:  
```
0  1  2  3  4  5        6  7  8  9  10 11  
12 13 14 15 16 17       18 19 20 21 22 23  
24 25 26 27 28 29       30 31 32 33 34 35  
36 37 38 39 40 41 42 43 44 45 46 47 48 49  
      50 51 52 53 54 55 56 57 58 59  
```
I was constantly forgetting about the encoders, so I was getting a different combo than I wanted and because of that thought that combo didn't work at all.  
Using [Nick Coutsos' Keyboard Editor](https://nickcoutsos.github.io/keymap-editor/) helped me to see visually my mistake.  

## Bluetooth connectivity
Bluetooth connectivity is finicky for me. This might be an issue with the Bluetooth adapter built into the motherboard of my PC though, so you would have to try.  

I have no problems connecting via Bluetooth to the work laptop. However, my main PC didn't even see the keyboard at first. Adding `CONFIG_BT_CTLR_TX_PWR_PLUS_8=y` helped me to see it available, but it doesn't connect. All other configurations only made it worse - I lost the ability to connect the keyboard to the laptop at some point.  

At the moment my bigger goal was to finish tinkering with the keymap (layout), so I didn't put much time into troubleshooting. Need to go back to the questions once I have enough uninterrupted time to tinker with it.  

## Issues when using with RDP/Gnome
It happened to me when I was using RDP, but people say they face the same issue on Gnome.  

What happens - when you use some of the ZMK keys (in my case, `(` and `)`), there is a 50/50 chance of getting either the bracket or a number. It seems that while you expect it to be a symbol you type, it is actually interpreted by the ZMK to be `Shift+9` and `Shift+0` respectively. And while in the RDP/Gnome environment, sometimes these modifiers are "lost" in the process.  

There's [an issue for that](https://github.com/zmkfirmware/zmk/issues/759) in the ZMK repo.  

# Technical info for those, who want to make their own config
## Splitting keymap to multiple files
Not many resources provide explicit information on how it is supposed to work.  
Some nuggets of information are scattered here and there. But most parts I had to deduce through trial and error. This is a log of my findings, shared in this readme as-is.  

### File types
For Devicetree (which is the name of the C-preprocessor syntax used in `*.keymap` files), there are several used file extensions:  
- `*.overlay`  
- `*.dts`  
- `*.dtsi`  
- `*.h`  

They are all still expected to contain Devicetree instructions, so you can use any at your discretion. `*.h` files are used in C and C++ as headers - declaration of an interface for the `*.cpp` code file. In here, the convention is to keep macros (`#DEFINE`) only. I use them to keep layer names, timings, etc.  

For the rest, I use `*.dtsi` file. These files are meant to be only used as a component to be included in a main file. In our case - from `*.keymap`.   

### Importing files into the main keymap
You add all the logic that is stored in those files, regardless of the extension you use, like this:  
``` c
#include "path/to/the/file.dtsi"
```
The path starts from the file location, where you add your include (most likely - your `*.keymap` file). If you need to go up in the folder tree, use `../` to go 1 level up.  

Please note that those includes should be placed outside of root brackets (`\ {` and `};`) of your main file, otherwise it would cause compiling issues to occur.  

For the header (`*.h`) files, you only move your `#DEFINE`-s there, add `#include` instruction, and that's it.  
For the include (`*.dtsi`) files, check where the part that you want to move to a separate file goes. If it's placed inside root brackets (`\ {` and `};`) - you should add them into their own root brackets in your new file:  
``` c
\ {
 behaviors {     
 rem: rem { // tap: backspace | shift + tap: delete
 compatible = "zmk,behavior-mod-morph";
 #binding-cells = <0>;
 bindings = <&kp BSPC>, <&kp DEL>;
 mods = <(MOD_LSFT|MOD_RSFT)>;
 };
 };
};
```

## Multiple layouts for one keyboard
I wanted to try different layouts for the keyboard - full-size 60 key, Corne 42 key, Chocofi 36 key, Miryoku 34 key, etc.  
I was keeping them in separate branches, which also meant that I had to copy some parts that I liked from one branch to the other (as it's all stored in a single keymap, something like cherry-pick wouldn't work properly). That meant every other layout except the one I'm currently changing was outdated.  
As I wanted to merge all layouts together into a single build, I found out that the `shield` argument can't be changed, as it has a finite set of allowed values.  

`Saixos`, one of the contributors, pointed me in the ZMK Discord server towards the [similar GitHub issue](https://github.com/zmkfirmware/zmk/issues/1931), with the solution that fits like a glove.  
You need to utilise the `cmake-args` property in the `build.yaml`:  
``` yaml
include:

  # 36 key layout
 - board: nice_nano_v2
    shield: sofle_left
    cmake-args: -DKEYMAP_FILE=../../config/sofle_36.keymap
    artifact-name: sofle_36_left-nice_nano_v2-zmk
 - board: nice_nano_v2
    shield: sofle_right
    cmake-args: -DKEYMAP_FILE=../../config/sofle_36.keymap
    artifact-name: sofle_36_right-nice_nano_v2-zmk
```
Important:  
- The `shield` property is validated against the set of supported shields in the ZMK. Changing it to any custom one will lead to an error at the early stages of the build;  
- The `-DKEYMAP_FILE` flag should start from `../../` due to the folder hierarchy created during the build process. In my scenario, `build.yaml` was in the root of the repo, and the keymap file was in the `config` folder;  
- `artifact-name` controls the name of the `.uf2` file generated in the build process. I tried to make it similar to the standard, with the addition of a key amount after the name of the keyboard;  

Having your keymap parts [separated](zmk_sofle.md#Splitting%20keymap%20to%20multiple%20files) helps reusing the behaviors and combos instead of copy-pasting them each time.  
If you need to have different `.conf` files as well - you can also specify it:  
``` yaml
cmake-args: -DKEYMAP_FILE=../../config/sofle_36.keymap -DEXTRA_CONF_FILE=../../config/sofle_36.conf
```
Unless the `-DEXTRA_CONF_FILE` flag is specified, the build will look for the `<shield>.conf` file.  

## Logging
It's an unnecessary thing most of the time, but came in handy when I fried my right-hand side nice!nano during one of the flashes. It allows you to check whether your keystrokes are registered, and what issues happen.  
It's documented in [the official ZMK docs](https://zmk.dev/docs/development/usb-logging). In case of the nice!nano board, all you need, is to create a firmware with `snippet: zmk-usb-logging`.  
I changed my build to always have it as a separate layout available just in case:  
``` yaml 
  # debug layout
 - board: nice_nano_v2
    shield: sofle_left
    cmake-args: -DKEYMAP_FILE=../../config/sofle_36.keymap
    artifact-name: sofle_debug_left-nice_nano_v2-zmk
    snippet: zmk-usb-logging
 - board: nice_nano_v2
    shield: sofle_right
    cmake-args: -DKEYMAP_FILE=../../config/sofle_36.keymap
    artifact-name: sofle_debug_right-nice_nano_v2-zmk
    snippet: zmk-usb-logging
```
It's kind of bloat, but I don't want to haphazardly create a new firmware with logging enabled when something goes wrong again.  
Please note that the ZMK development team is warning about `a non-trivial impact to your time on battery` of logging. So it's recommended you only use the layout with logging enabled in troubleshooting cases only, changing it to the one without logging once it's no longer needed.  
