---
title: ASCII and extended ASCII character set (8-bit) (encoding)
---

## ANSI vs ASCII character set

> There is ASCII (7 bit) and there is Extended ASCII (8 bit), sometimes called high-ASCII (above 128 character values). There is Extended ASCII by OEM (IBM PC), and there is Extended ASCII by ANSI (“ANSI”). But nevertheless, it’s Extended ASCII.
>
> The so called “ANSI encoding” means different things in different programs and operating systems (variable code points), but in a world with WinDOS this gets completely screwed up and any logic that had existed in that encoding before gets thrown out the WinDOw(s). In the context of Windows, ANSI is usually used to mean Extended ASCII or high-ASCII, and wrongfully so. Because this is not the Extended ASCII by ANSI (the organization), but Microsoft’s own Extended ASCII which they call ANSI code page.
>
> Read more: [Difference Between ANSI and ASCII | Difference Between](http://www.differencebetween.net/technology/web-applications/difference-between-ansi-and-ascii/#ixzz6fvQvi8L6) http://www.differencebetween.net/technology/web-applications/difference-between-ansi-and-ascii/#ixzz6fvQvi8L6

## Extended ASCII

> **Extended ASCII** (**EASCII** or **high ASCII**) [character encodings](https://en.wikipedia.org/wiki/Character_encoding) are [eight-bit](https://en.wikipedia.org/wiki/Eight-bit) or larger encodings that include the standard seven-[bit](https://en.wikipedia.org/wiki/Bit) [ASCII](https://en.wikipedia.org/wiki/ASCII) characters, plus additional characters. Using the term "extended ASCII" on its own is sometimes criticized,[[1\]](https://en.wikipedia.org/wiki/Extended_ASCII#cite_note-1)[[2\]](https://en.wikipedia.org/wiki/Extended_ASCII#cite_note-2)[[3\]](https://en.wikipedia.org/wiki/Extended_ASCII#cite_note-3) because it can be mistakenly interpreted to mean that the ASCII standard has been updated to include more than 128 characters or that the term unambiguously identifies a single encoding, neither of which is the case.
>
> There are many extended ASCII encodings (more than 220 DOS and Windows codepages). [EBCDIC](https://en.wikipedia.org/wiki/EBCDIC) ("the other" major 8-bit character code) likewise developed many extended variants (more than 186 EBCDIC codepages) over the decades.

## Extended ASCII: ANSI(Windows-1252) vs ISO-8859-1 vs MacRoman vs ISO-8859-15

> Of the three main 8-bit character sets, only ISO-8859-1 is produced by a standards organization. The three sets are identical for the 95 characters from 32 to 126, the ASCII character set. The [ANSI character set](http://www.alanwood.net/demos/ansi.html), also known as Windows-1252, has become a Microsoft proprietary character set; it is a superset of ISO-8859-1 with the addition of 27 characters in locations that ISO designates for control codes. Apple’s proprietary [MacRoman character set](http://www.alanwood.net/demos/macroman.html) contains a similar variety of characters from 128 to 255, but with very few of them assigned the same numbers, and also assigns characters to the control-code positions.
>
> http://www.alanwood.net/demos/charsetdiffs.html

### ISO-8859-1 vs ISO-8859-15

> The ISO-8859-1 character set is full; Every code point is assigned to a character. In 1999, ISO needed to make the Euro currency symbol available. There were also a few other characters that were desired. So ISO created ISO-8859-15, which is identical to ISO-8859-1 except for 8 characters.
>
> ISO-8859-15 is often used on Unix systems in Europe, especially in France.
>
> 

| Code Point | ISO-8859-1 Removed |       ISO-8859-15 Added        |                |                                       |        |
| :--------: | :----------------: | :----------------------------: | :------------: | :-----------------------------------: | ------ |
| Character  |   Character Name   |           Character            | Character Name |          Unicode Code Point           |        |
|     A4     |         ¤          |         Currency Sign          |       €        |               Euro Sign               | U+20AC |
|     A6     |         ¦          |           Broken Bar           |       Š        |   Latin Capital Letter S With Caron   | U+0160 |
|     A8     |         ¨          |           Diaeresis            |       š        |    Latin Small Letter S With Caron    | U+0161 |
|     B4     |         ´          |          Acute Accent          |       Ž        |   Latin Capital Letter Z With Caron   | U+017D |
|     B8     |         ¸          |            Cedilla             |       ž        |    Latin Small Letter Z With Caron    | U+017E |
|     BC     |         ¼          |  Vulgar Fraction One Quarter   |       Œ        |       Latin Capital Ligature OE       | U+0152 |
|     BD     |         ½          |    Vulgar Fraction One Half    |       œ        |        Latin Small Ligature OE        | U+0153 |
|     BE     |         ¾          | Vulgar Fraction Three Quarters |       Ÿ        | Latin Capital Letter Y With Diaeresis | U+0178 |

