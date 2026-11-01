---
title: "StyledText API"
summary: "Editing contracts, import callbacks, validation limits, and the BSTX v1 wire format."
category: "Reference"
order: 20
keywords: ["styledtext", "styl", "binary", "serialization", "unicode"]
symbols: ["TextStyle", "StyleRun", "StyledText", "EncodeStyledText", "DecodeStyledText", "StylOffsetMapper", "StylFontResolver", "StylRecord", "StylImport", "ImportStyl"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# API

## Portable model

| API | Contract |
| --- | --- |
| `TextStyle` | Mutable FontName String; FontSize Double points; Bold/Italic/Underline Boolean; Red/Green/Blue Integer RGB16. Empty name/zero size mean unspecified. |
| `TextStyle.Copy()` | Validated independent value; rejects invalid UTF-8, NUL, names over 4096 bytes, nonfinite/out-of-range sizes, colors outside 0–65535. |
| `TextStyle.Equals(other)` | Exact supported-attribute equality; Nil compares false. |
| `StyleRun` | Mutable snapshot: Start/Length Integer, Style TextStyle. |
| `StyledText(content = "")` | Default attributes, zero-based scalar offsets; rejects malformed UTF-8, NUL, or over 1,000,000 scalars. |
| `StyledText(content, style)` | Copy explicit default style; Nil rejected. |
| `StyledText.FromRuns(content, runs, defaultStyle)` | Copy exact contiguous positive-length coverage; reject gaps/overlaps/Nil; merge equal neighbours. At most 100,000 input runs. |
| `PlainText()`, `Length()`, `DefaultStyle()`, `Runs()` | Content, scalar count, copied default, deep run snapshots. |
| `StyleAt(position)` | Copied attributes of an existing scalar; end position invalid. |
| `SetStyle(start, length, style)` | Full attribute replacement; zero length no-op after validation. |
| `Insert(position, content[, style])` | Explicit style or inherit left/right/default; Nil requests inheritance. |
| `Delete(start, length)` | Remove range and coalesce survivors; document default persists. |
| `Slice(start, length)` | Independent document with rebased runs and original default. |
| `EncodeStyledText(document)` | New Bytes, exact supported-attribute serialization, Nil rejected. |
| `DecodeStyledText(data)` | Strict validated new document, input snapshot, at most 16 MB. |

Invalid arguments throw `InvalidArgumentException`; callback exceptions propagate unchanged. Documents have at most 100,000 runs and 1,000,000 scalars; name limits are bytes. Invalid edits leave the document intact. Valid in-memory documents can exceed the separate 16 MB wire cap (for example, many runs with long names); EncodeStyledText then throws without modifying the model. Empty content has no runs. Offsets are neither UTF-16 units nor grapheme clusters.

## Legacy importer

`StylOffsetMapper(legacyOffset As Integer) As Integer` and `StylFontResolver(fontID As Integer) As String` are nominal owned closure types. Pass typed closures to `ImportStyl(content, data, mapOffset, resolveFont, maxRecords = 10000) As StylImport`. Nil callbacks are rejected. The importer never decodes the source text. The caller supplies correct mapping from historical starts into the separately decoded scalar string. Empty font names produce warnings.

`StylImport` owns mutable `Document`, `Records`, and `Warnings` properties. `StylRecord` retains Start, Height, Ascent, FontID, Face, Filler, Size, Red, Green, Blue, MappedStart, and ResolvedFontName. Raw metadata is separate from document attributes and is not encoded by EncodeStyledText. All original fields are preserved for accepted records, including unsupported face/filler values. The first byte of the two-byte StyleField is Face; the second is Filler.

The parser reads big-endian count and records. Count must be signed nonnegative Int16 and within maxRecords (0–32767); exact length is 2+20*count. Starts are nonnegative signed Int32, begin at zero, and strictly increase. Size/metrics/font ID are signed Int16; negative size is rejected. RGB channels are UInt16. Mapped starts begin at zero, increase, and stay strictly inside content. Zero records are accepted only for empty content. Raw validation completes before callbacks. No duplicate-start or terminal empty-run normalization is attempted.

## Binary format v1

All integers use unsigned **big-endian** byte order. UTF-8 strings must be valid and NUL-free. They are prefixed by a four-byte byte count; no BOM or terminator. Limits: 16,000,000 total bytes, 4,000,000 content bytes, 4096 bytes per font name, 1,000,000 content scalars, 100,000 runs.

Document: four ASCII bytes `BSTX`, one version byte `01`, content string, default style, four-byte run count, then each run as four-byte scalar length followed by style. Starts are implicit cumulative scalar lengths. Coverage must exactly match content. Runs must be positive and adjacent styles different. Empty content has zero runs. Trailing bytes, unknown versions, and noncanonical forms are rejected.

Style: font-name string, font-size encoding (nine bytes), one-byte flags (bold=1, italic=2, underline=4), then red/green/blue as two bytes each. Other flag bits are invalid. Font sizes must be finite in 0–1,000,000 points.

Font size uses a two-byte biased exponent `b` followed by a seven-byte significand `m`. Zero is exclusively b=0,m=0. Otherwise value is `(m / 2^52) * 2^(b-1074)`, with 2^52 ≤ m < 2^53 and 0 ≤ b ≤ 1093. This encodes every allowed binary64 size exactly without native byte order or decimal conversion. A decoder must reject pairs that do not reproduce the same pair after rounding to binary64 and re-encoding (including underflowed noncanonical subnormal encodings). Negative zero encodes as zero; this format preserves numeric attribute equality, not the sign bit of zero.

See [overview](index.md) and [getting started](getting-started.md).


[Full typed API: properties, parameters, defaults, and returns](typed-api.md)
