# PiTubeControl: A(nother) control utility for PiTubeDirect

[PiTubeDirect](https://github.com/hoglet67/PiTubeDirect) allows a Raspberry Pi
to interface to the Tube interface on a BBC Model B, Master, or Electron, and
emulate a range of architectures as a Tube-attached second processor.

The host computer is able to select different emulations by writing to specific
locations across the Tube. This can be done with `*FX` commands (e.g. `*FX
151,230,12` to emulate an ARM2), but since it is a bit hard to remember a bunch
of numeric arguments and what all of them do, I wrote a `*PiTube` command to
provide an easier-to-remember way to control PiTubeDirect.

`*PiTube` always runs on the IO processor, so it can be used with Second
Processor emulations of any instruction set, not just the 6502.

## Usage

`*PiTube`
Shows an overview of valid commands.

`*PiTube <processor> [option] [option]...`
Selects a Second Processor with a given set of options.

Processor names and options are case-insensitive.

Processor selection will only take effect after resetting the system with
Control-BREAK. Note that this selection will not persist after the power has
been turned off - on initial boot, PiTubeDirect will always start with the
configuration defined on its SD card in `cmdline.txt`.

## Valid processor types and options

| Processor | Aliases | Options         | Comments                         |
|-----------|---------|-----------------|----------------------------------|
| 65C02     | 6502    | `FAST`, `L6502` | Standard 6502 Second Processor   |
| 65C102    | 65102   | `FAST`          | Master Turbo Second Processor    |
| Z80       |         |                 |                                  |
| 80286     | 286     |                 | Master 512 Second Processor      |
| 6809      |         |                 |                                  |
| PDP11     |         |                 |                                  |
| ARM2      | ARM     |                 | Acorn ARM Evaluation System      |
| 32016     |         |                 |                                  |
| NATIVE    | PI      |                 | Native Raspberry PI ARM6 or ARM7 |
| OFF       |         |                 | Disable PiTubeDirect             |

See the [PiTubeDirect project page](https://github.com/hoglet67/PiTubeDirect)
for further documentation about these processors.

**NOTE:** After selecting `*PiTube OFF` (or the equivalent `*FX 151,230,16`),
PiTubeDirect stops listening to the Tube, and another Second Processor cannot be
selected until the system has been power-cycled.

An less drastic alternative is available on BBC Master series machines:
`*CONFIGURE NoTube` (followed by Control-BREAK) disables the Tube interface in
software. The Tube interface can be re-enabled without power cycling with
`*CONFIGURE Tube`.

### Options

#### `FAST`

By default, the 65C02 and 65C102 processor emulations are calibrated to run at
approximately the same speed as the original hardware. The `FAST` option allows
the emulation to free-run at the highest possible speed, increasing performance
at the cost of potential compatibility issues.

#### `L6502`

The 65C02 processor emulation can use either the default 65Tube implementation,
or the lib6502 emulator core. Supply the `L6502` option to select the lib6502
core.

## Building

`*PiTube` is written in BeebASM assembler; the distribution disc image can be
built with `beebasm -i pitube.6502 -do PiTube.ssd`.

The source is designed to be easy to add new processor types and options to,
without having to modify the core logic. Adding a new processor should just be a
matter of adding a table entry for it (and any desired aliases), and adding a
function to parse and act upon any options that it may require.

## See Also

* PiTubeDirect on GitHub: https://github.com/hoglet67/PiTubeDirect
* Similar tool, but menu-based: https://stardot.org.uk/forums/viewtopic.php?f=2&t=19647
