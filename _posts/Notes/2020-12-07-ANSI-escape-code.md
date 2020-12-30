---
title: ANSI Escape Code
---

ref: https://en.wikipedia.org/wiki/ANSI_escape_code

**ANSI escape sequences** are a standard for [in-band signaling](https://en.wikipedia.org/wiki/In-band_signaling) to control cursor location, color, font styling, and other options on video [text terminals](https://en.wikipedia.org/wiki/Text_terminal) and [terminal emulators](https://en.wikipedia.org/wiki/Terminal_emulator). 

Certain sequences of [bytes](https://en.wikipedia.org/wiki/Byte), most starting with an [ASCII Escape](https://en.wikipedia.org/wiki/Escape_character#ASCII_escape_character) and [bracket](https://en.wikipedia.org/wiki/Bracket) character followed by parameters, are embedded into text. The terminal interprets these sequences as commands, rather than text to display verbatim.

ANSI sequences were introduced in the 1970s to replace vendor-specific sequences and became widespread in the computer equipment market by the early 1980s. They are used in development, scientific, commercial text-based applications as well as [bulletin board systems](https://en.wikipedia.org/wiki/Bulletin_board_system) to offer standardized functionality.

Although hardware text terminals have become increasingly rare in the 21st century, the relevance of the ANSI standard persists because a great majority of terminal emulators and command consoles interpret at least a portion of the ANSI standard.

## ASCII Escape Character

- `ESC`, `\e`
- `0x1B`, `\u001b` (hexadecimal)
- `\033` (octal)

## Escape Sequence

- Escape sequences vary in length. 

- The general format for an ANSI-compliant escape sequence is defined by [ANSI X3.41](https://en.wikipedia.org/wiki/ANSI_X3.41) (equivalent to **ECMA-35** or **ISO/IEC 2022**). 

  - The **[ESC](https://en.wikipedia.org/wiki/C0_and_C1_control_codes#ESC)** (27 / [hex](https://en.wikipedia.org/wiki/Hexadecimal) 0x1B / [oct](https://en.wikipedia.org/wiki/Octal) 033) is followed by 

    - zero or more intermediate "I" bytes between hex 0x20 and 0x2F inclusive followed by 
    - a final "F" byte between 0x30 and 0x7E inclusive.

  - **ANSI X3.41** / **ECMA-35** divides escape sequences into several broad categories:

    - | type           | desc                                                         | note                                                         |
      | -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
      | 1. type `Fe`   | no I bytes, and a F byte between 0x40 and 0x5F inclusive (`@`, `A-Z`, `[\]^_`) | delegated to the applicable [C1 control code](https://en.wikipedia.org/wiki/C0_and_C1_control_codes) (ASCII 0x80-0x9F) standard (Accordingly all escape sequences corresponding to C1 control codes from **ANSI X3.64 / ECMA-48** follow this format) (*e* for *equivalent*) |
      | 2. type `Fs`   | no I bytes, and a F byte between 0x60 and 0x7E inclusive (`` ` `` , ` a-z`, `{|}~`) | used for control functions individually registered with the [ISO-IR](https://en.wikipedia.org/wiki/ISO-IR) registry (defined in [ANSI X3.41](https://en.wikipedia.org/wiki/ANSI_X3.41), equivalent to **ECMA-35** or **ISO/IEC 2022**)  see https://en.wikipedia.org/wiki/ISO/IEC_2022#Other_control_functions |
      | 3. type `Fp`   | no I bytes, and a F byte between 0x30 and 0x3F inclusive (`0-9`, `:;<=>?`) | set apart for **private-use** control functions              |
      | 4. type `nF`   | one or more I bytes (n = the low four bits of the first I byte) |                                                              |
      | 4.1 type `nFp` | the F byte is in the private use range from 0x30 and 0x3F inclusive |                                                              |
      | 4.2 type `nFt` | the F byte is *not* in the private use range from 0x30 and 0x3F inclusive |                                                              |

      | example in 4.  type `nF` | desc                                                         | note                                                         |
      | ------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
      | type `2F`                | the first intermediate byte is `0x22`                        | mostly used for ANSI/ISO code-switching mechanisms such as those used by [ISO-2022-JP](https://en.wikipedia.org/wiki/ISO-2022-JP) |
      | type `3F`                | the first intermediate byte is `0x23`                        | used for individual control functions                        |
      | type `3Ft`               | the first intermediate byte is `0x23` and the F byte is *not* in the private use range | reserved for additional ISO-IR registered individual control functions |
      | type `3Fp`               | the first intermediate byte is `0x23` and the F byte is in the private use range | private-use control functions                                |

      ### type `Fe`

      The standard says that, in 8-bit environments, the control functions corresponding to type `Fe` escape sequences (those from the set of [C1 control codes](https://en.wikipedia.org/wiki/C1_control_codes)) can be represented as single bytes in the 0x80–0x9F range.[[5\]](https://en.wikipedia.org/wiki/ANSI_escape_code#cite_note-ECMA-48-5):5.3.b 

      However, on modern devices those codes are often used for other purposes, such as parts of [UTF-8](https://en.wikipedia.org/wiki/UTF-8) or for [CP-1252](https://en.wikipedia.org/wiki/CP-1252) characters, so only the 2-byte sequence is typically used. (In the case of UTF-8 and other Unicode encodings, C1 can be encoded as their Unicode codepoints [e.g. `\xC2\x8E` for `U+008E`], but no space is saved this way.)

      Other C0 codes besides ESC — commonly BEL, BS, CR, LF, FF, TAB, VT, SO, and SI — produce similar or identical effects to some control sequences when output.

## Escape Sequence Examples

| type  |          | Intermediate Byte  | Final Byte |  C1  |     Short      |                             Name                             |                                                              |
| ----- | :------: | ------------------ | ---------- | :--: | :------------: | :----------------------------------------------------------: | ------------------------------------------------------------ |
| `Fe`  |  ESC N   | -                  | 0x4E       | 0x8E |      SS2       |                       Single Shift Two                       |                                                              |
| `Fe`  |  ESC O   | -                  | 0x4F       | 0x8F |      SS3       |                      Single Shift Three                      |                                                              |
| `Fe`  |  ESC P   | -                  | 0x50       | 0x90 |      DCS       |                    Device Control String                     |                                                              |
| `Fe`  |  ESC [   | -                  | 0x5B       | 0x9B |      CSI       |                 Control Sequence Introducer                  |                                                              |
| `Fe`  |  ESC \   | -                  | 0x5C       | 0x9C |       ST       |                      String Terminator                       |                                                              |
| `Fe`  |  ESC ]   | -                  | 0x5D       | 0x9D |      OSC       |                   Operating System Command                   |                                                              |
| `Fe`  |  ESC X   | -                  | 0x58       | 0x98 |      SOS       |                       Start of String                        |                                                              |
| `Fe`  |  ESC ^   | -                  | 0x5E       | 0x9E |       PM       |                       Privacy Message                        |                                                              |
| `Fe`  |  ESC _   | -                  | 0x5F       | 0x9F |      APC       |                 Application Program Command                  |                                                              |
| `Fs`  |  ESC c   | -                  | 0x63       |  -   |      RIS       |                    Reset to Initial State                    | (independent function) ANSI escape sequences recognised by terminals |
| `0Ft` | ESC SP F | SP (0x53 0x5**0**) | 0x46       |  -   | ACS6<br/>S7C1T | Announce Code Structure 6<br/>Send 7-bit C1 Control Character to the Host | (announcement) ANSI escape sequences recognised by terminals |
| `0Ft` | ESC SP G | SP (0x53 0x5**0**) | 0x47       |  -   | ACS6<br/>S7C1T | Announce Code Structure 6<br/>Send 7-bit C1 Control Character to the Host | (announcement) ANSI escape sequences recognised by terminals |
| `Fp`  |  ESC 7   | -                  | 0x37       |  -   |     DECSC      |                       DEC Save Cursor                        | (private-use) escape sequences recognised by the [VT100](https://en.wikipedia.org/wiki/VT100), its successors, and/or terminal emulators such as [xterm](https://en.wikipedia.org/wiki/Xterm) |
| `Fp`  |  ESC 8   | -                  | 0x38       |  -   |     DECRC      |                      DEC Restore Cursor                      | (private-use) escape sequences recognised by the [VT100](https://en.wikipedia.org/wiki/VT100), its successors, and/or terminal emulators such as [xterm](https://en.wikipedia.org/wiki/Xterm) |
| 3Fp   | ESC # 3  | 0x2**3**           | 0x33       |  -   |     DECDHL     |             DEC Double-Height Letters, Top Half              | (private-use) escape sequences recognised by the [VT100](https://en.wikipedia.org/wiki/VT100), its successors, and/or terminal emulators such as [xterm](https://en.wikipedia.org/wiki/Xterm) |
| 3Fp   | ESC # 4  | 0x2**3**           | 0x34       |  -   |     DECDHL     |            DEC Double-Height Letters, BottomHalf             | (private-use) escape sequences recognised by the [VT100](https://en.wikipedia.org/wiki/VT100), its successors, and/or terminal emulators such as xterm |
| 3Fp   | ESC # 5  | 0x2**3**           | 0x35       |  -   |     DECSWL     |                    DEC Single-Width Line                     | (private-use) escape sequences recognised by the [VT100](https://en.wikipedia.org/wiki/VT100), its successors, and/or terminal emulators such as [xterm](https://en.wikipedia.org/wiki/Xterm) |
| 3Fp   | ESC # 6  | 0x2**3**           | 0x36       |  -   |     DECDWL     |                    DEC Double-Width Line                     | (private-use) escape sequences recognised by the [VT100](https://en.wikipedia.org/wiki/VT100), its successors, and/or terminal emulators such as [xterm](https://en.wikipedia.org/wiki/Xterm) |

## Control String

the ESC sequences for [DCS](https://en.wikipedia.org/wiki/ANSI_escape_code#DCS), [SOS](https://en.wikipedia.org/wiki/ANSI_escape_code#SOS), [OSC](https://en.wikipedia.org/wiki/ANSI_escape_code#OSC), [PM](https://en.wikipedia.org/wiki/ANSI_escape_code#PM) and [APC](https://en.wikipedia.org/wiki/ANSI_escape_code#APC) are 

- followed by a variable-length sequence of parameter data 
- terminated by [ST](https://en.wikipedia.org/wiki/ANSI_escape_code#ST) (String Terminator `ESC \`)

## Control Sequence Introducer 

is dubbed a "control sequence" by **ECMA-48** (**ANSI X3.64** / **ISO 6429**)

For CSI, or "Control Sequence Introducer" commands, the `ESC [` is followed by 

1. any number (including none) of **parameter bytes** in the range 0x30–0x3F (ASCII `0–9:;<=>?`), 
2. then by any number of **intermediate bytes** in the range 0x20–0x2F (ASCII space and `!"#$%&'()*+,-./`), 
3. then finally by *a single* **final byte** in the range 0x40–0x7E (ASCII `@A–Z[\]^_`a–z{|}~`)



| CSI (`ESC [`) | desc                                    | any # of parameter bytes | any # of intermediate bytes | a single final byte |
| ------------- | --------------------------------------- | ------------------------ | --------------------------- | ------------------- |
| `CSI <n> m`   | red foreground; blue background **SGR** | 31;44                    |                             | m                   |
|               |                                         |                          |                             |                     |
|               |                                         |                          |                             |                     |

|    Code     | a single final byte | Short |             Name             |
| :---------: | ------------------- | :---: | :--------------------------: |
|   CSI n A   | A                   |  CUU  |          Cursor Up           |
|   CSI n B   | B                   |  CUD  |         Cursor Down          |
|   CSI n C   | C                   |  CUF  |        Cursor Forward        |
|   CSI n D   | D                   |  CUB  |         Cursor Back          |
|   CSI n E   | E                   |  CNL  |       Cursor Next Line       |
|   CSI n F   | F                   |  CPL  |     Cursor Previous Line     |
|   CSI n G   | G                   |  CHA  |  Cursor Horizontal Absolute  |
| CSI n ; m H | H                   |  CUP  |       Cursor Position        |
|   CSI n J   | J                   |  ED   |       Erase in Display       |
|   CSI n K   | K                   |  EL   |        Erase in Line         |
|   CSI n S   | S                   |  SU   |          Scroll Up           |
|   CSI n T   | T                   |  SD   |         Scroll Down          |
| CSI n ; m f | f                   |  HVP  | Horizontal Vertical Position |
|   CSI n m   | m                   |  SGR  |   Select Graphic Rendition   |
|   CSI 5i    |                     |       |         AUX Port On          |
|   CSI 4i    |                     |       |         AUX Port Off         |
|   CSI 6n    |                     |  DSR  |     Device Status Report     |

## Select Graphic Rendition (SGR)

 - `CSI <n> m`
 - SGR (Select Graphic Rendition) sets display attributes. 
 - Several attributes can be set in the same sequence, separated by semicolons.[35] 
 - Each display attribute remains in effect until a following occurrence of SGR resets it.[5] 
 - If no codes are given, `CSI m` is treated as `CSI 0 m` (reset / normal).

## Tip
To know the code of a key, execute `cat`, press enter, press the key, then Ctrl+C.

## Terminal input sequences

ref: https://en.wikipedia.org/wiki/ANSI_escape_code

https://docstore.mik.ua/orelly/unix/upt/ch05_08.htm

> Most terminals use special character strings called escape sequences to control their operation. These strings begin with the escape character (ASCII character 033) ( 51.3 ) . 
>
> This character can be generated alone by the ESC key found on most keyboards, or by typing the left bracket character while holding down the CONTROL key (often shown as ^[ ). But it's also generated by many of the special keys on your keyboard. For example, an UP ARROW key might generate an escape sequence like ^[OA . When the terminal sees this sequence of characters, it knows to move the cursor up one line. 
>
> The special escape sequences used by your terminal are stored in the terminal's termcap or terminfo entry ( 41.11 , 5.2 ) , which allows programs to respond appropriately to all of the special keys on the keyboard. Programs themselves issue escape sequences to do such things as move around the screen, highlight text, and so on. docstore.mik.ua/orelly/unix/upt/ch05_08.htm

When typing input on a terminal keypresses outside the normal main alphanumeric keyboard area can be sent to the host as ANSI sequences. For keys that have an equivalent output function, such as the cursor keys, these often mirror the output sequences. However, for most keypresses there isn't an equivalent output sequence to use.

There are several encoding schemes, and unfortunately most terminals mix sequences from different schemes, so host software has to be able to deal with input sequences using any scheme. To complicate the matter, the VT terminals themselves have two schemes of input, *normal mode* and *application mode* that can be switched by the application.

```
<char>                                -> char
<esc> <nochar>                        -> esc
<esc> <esc>                           -> esc
<esc> <char>                          -> Alt-keypress or keycode sequence
<esc> '[' <nochar>                    -> Alt-[
<esc> '[' (<num>) (';'<num>) '~'      -> keycode sequence, <num> defaults to 1
```

- If the terminating character is '~', 
  - the first number must be present and is a keycode number, 
  - the second number is an optional modifier value. 
- If the terminating character is a letter, 
  - the letter is the keycode value
  - and the optional number is the modifier value.

The modifier value defaults to 1, and after subtracting 1 is a bitmap of modifier keys being pressed: Meta-Ctrl-Alt-Shift. So, for example, <esc>[4;2~ is Shift-End, <esc>[20~ is function key 9, <esc>[5C is Ctrl-Right[

```
vt sequences:
<esc>[1~    - Home        <esc>[16~   -             <esc>[31~   - F17
<esc>[2~    - Insert      <esc>[17~   - F6          <esc>[32~   - F18
<esc>[3~    - Delete      <esc>[18~   - F7          <esc>[33~   - F19
<esc>[4~    - End         <esc>[19~   - F8          <esc>[34~   - F20
<esc>[5~    - PgUp        <esc>[20~   - F9          <esc>[35~   - 
<esc>[6~    - PgDn        <esc>[21~   - F10         
<esc>[7~    - Home        <esc>[22~   -             
<esc>[8~    - End         <esc>[23~   - F11         
<esc>[9~    -             <esc>[24~   - F12         
<esc>[10~   - F0          <esc>[25~   - F13         
<esc>[11~   - F1          <esc>[26~   - F14         
<esc>[12~   - F2          <esc>[27~   -             
<esc>[13~   - F3          <esc>[28~   - F15         
<esc>[14~   - F4          <esc>[29~   - F16         
<esc>[15~   - F5          <esc>[30~   -             

xterm sequences:
<esc>[A     - Up          <esc>[K     -             <esc>[U     -
<esc>[B     - Down        <esc>[L     -             <esc>[V     -
<esc>[C     - Right       <esc>[M     -             <esc>[W     -
<esc>[D     - Left        <esc>[N     -             <esc>[X     -
<esc>[E     -             <esc>[O     -             <esc>[Y     -
<esc>[F     - End         <esc>[1P    - F1          <esc>[Z     -
<esc>[G     - Keypad 5    <esc>[1Q    - F2       
<esc>[H     - Home        <esc>[1R    - F3       
<esc>[I     -             <esc>[1S    - F4       
<esc>[J     -             <esc>[T     - 
```