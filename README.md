# MagicDesk-like External ROM cartridge for C128

This is a clone of [Magic Desk 512k](https://github.com/msolajic/c64-magic-desk-512k) by Marko Šolajić
converted to KiCad 6 format and modified to work as an External ROM cartridge for C128 and map 32K of ROM at a time.

<img src="media/c128-magicdesk.jpg" alt="C128 External Function ROM cartridge" width=640>

There are two projects [Magic Desk 128](https://github.com/RetroNynjah/Magic-Desk-128) and [Gmod2 (128)](https://www.freepascal.org/~daniel/gmod2/)
with a similar goal, but both map only 16K at a time. This makes it impossible e.g. to put The Servant on such cartridge.

## Why

It just seems much more useful in C128 mode to have whole 32K available - just like Internal Function ROM, but easily switched.
Since MMU can bank in/out both 16K halves ($8000-$BFFF) and ($C000-$FFFF) separately it seems that e.g. GEOS 128 could run directly from ROM bypassing any boot or loading parts.

## Hardware

The [schematic is here](kicad/plots/c128-magicdesk.pdf).

The main difference to C64 version is that this cartridge is meant for C128, so `/EXROM` signal is no longer connected to prevent from entering C64 mode automatically.

Two diodes and a pullup resistor perform AND function on `/ROML` and `/ROMH` to map 32K at a time ($8000-$FFFF).

Because the bank size is now 32K instead of 8K the lowest two bits of control register are not connected.

[kicad](kicad/) folder contains all the KiCad 6.0 project files and [kicad/plots](kicad/plots) all the Gerber files needed to produce PCB.

Please read the notes from the [original project](https://github.com/msolajic/c64-magic-desk-512k). The jumper configuration for various EPROMS/EEPROMS/FLASH chips is exactly the same, as well as possible expansion to 1MB.

## Programming

Data from ROM will appear as External Function ROM mapped in $8000-$FFFF area (32K).

ROM banks can be changed from BASIC.

```
BANK15:POKEDEC("DE00"),4*<rom bank>
```

There are 16 banks in a 512K EEPROM and 32 banks if you piggy-back two of them and connect `/CE2` signal as described for C64 Magic Desk 512K.

RESET will always enable bank 0.

It's easy to check the ROM data from C128 monitor, just use `8` as the bank number:

```
M 88000 880FF
```

Reading from $DE00 register will switch bank to a random value (what happened to be on the data bus), don't do that.

## Cartrige ROM generator

This forked and modified version of the [Magic cartridge generator](https://bitbucket.org/ytmytm/magic-cartridge-generator/src/feature-c128-extfunctionrom-32k/) supports 32K banks, use `-832` option instead of `-8` when building ROM image.

A simple code snippet for a true C128 cartridge (internal/external function ROM) code to load and start BASIC program can be found on [World of Jani page](http://blog.worldofjani.com/?p=1600).

[Gmod2 Commodore 128 technical manual](https://www.freepascal.org/~daniel/gmod2/gmod2-c128.pdf) is also a useful resource.

You can also put The Servant on it

## Emulator Support

None yet :)
