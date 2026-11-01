# Provenance

This package is independently written Buoy code. No historical implementation or tests are copied.

The legacy record layout is based on Charles Yeomans, [The TextStyleData Format](https://web.archive.org/web/20160630232339/https://www.declaresub.com/articles/2007/02/16/the-textstyledata-format), February 16, 2007. The article describes classic Mac TextEdit Style Scrap Records: a two-byte count followed by twenty-byte records containing start, height, ascent, font ID, face/filler, size, and three 16-bit color components. Its Carbon font-ID lookup is not used. The package's independent hand-authored fixture exercises that layout.

The portable model and BSTX binary format are new APIs; neither promises full Xojo StyledText behavior nor native attributed-text editing.
