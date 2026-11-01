# StyledText · 0.1.0

`Import styledtext` provides a portable Unicode text model with normalized style runs, editing, exact binary save/load, and a bounded importer for classic Mac `styl`/REALbasic `TextStyleData` data. It is an independent implementation, tested with the real Buoy compiler and a freshly sealed consumer.

The model supports font name, fractional point size, bold, italic, underline, and RGB16 foreground color. It does not change core `TextArea`, render text, implement selection/typing UI, parse RTF, or provide paragraph layout. For native editing, [StyledTextUI](https://github.com/BuoyBay/styledtextui/blob/main/docs/API.md) embeds GTK/AppKit/Rich Edit controls with selection formatting, typing styles and undo/redo. The separate [richtext](https://github.com/BuoyBay/richtext/blob/main/docs/API.md) package handles macOS RTF conversion; its plain-text decoder discards formatting.

## Editing contract

Positions and lengths are zero-based Unicode **scalar** counts, matching Buoy 0.72's `String.Len` and `String.Substring`, verified by execution. UTF-8 must be valid and must not contain NUL (Buoy 0.72 string construction can discard U+0000 before the library sees it; binary input rejects NUL explicitly). Combining marks and emoji sequences may span multiple scalars: these are not grapheme or UTF-16 positions. A default style belongs to the document independently of its runs. Empty content has zero runs. Every nonempty scalar belongs to exactly one positive-length run; adjacent equal styles merge.

`SetStyle` replaces all attributes in its range. Plain `Insert` inherits the left scalar's style, the right scalar at position zero, or the document default when empty. The explicit style overload also accepts Nil to request inheritance. `Delete` preserves surviving styles, including across merged boundaries. Deleting everything retains the original document default. `Slice` rebases runs and retains that default, even for an empty slice. Zero-length edits are no-ops after argument validation. `StyleAt` requires an actual scalar, so the end position is invalid. Invalid ranges throw `InvalidArgumentException` without mutating the document.

`TextStyle` is mutable for convenient construction, but every input, getter, factory, and returned run is copied deeply. `Equals` compares supported attributes, including the exact case-sensitive font name. Empty font name and size zero mean unspecified/platform default. Colors are integers 0–65535; sizes must be finite and in 0–1,000,000 points. Font names must be valid UTF-8 and at most 4096 bytes. Documents allow at most 1,000,000 scalars and 100,000 runs. Interchange is limited to 16,000,000 bytes. Valid styles are checked on input; internal copies retain validation and remain detached from callers. These fixed limits bound work and allocation. Editing scans content and runs, including font-name comparison costs, so this is not a large-document rope editor. The wire limit is independent of the model limits: a valid document with many long font names can exceed 16 MB and fail to encode. Encode before committing a save and handle that exception; no model is changed on failure.

## Legacy import

`ImportStyl(content, data, mapOffset, resolveFont, maxRecords = 10000)` takes already decoded, valid Unicode content. The owned `StylOffsetMapper` closure maps each original start to a scalar boundary; `StylFontResolver` maps signed font IDs to names, returning an empty name for unresolved IDs. There is no automatic byte/UTF-16/codepoint assumption or Carbon font lookup. The caller must ensure the mapping corresponds to the separately decoded text. Callbacks must not be Nil; exceptions propagate. The input bytes are snapshotted before any callback.

The importer requires a big-endian signed nonnegative Int16 count and exactly `2 + 20 * count` bytes, first start zero, strictly increasing signed nonnegative Int32 starts, and nonnegative signed Int16 font sizes. Mapped starts must begin at zero, strictly increase, and be inside the content. Zero records are valid only for empty content. `maxRecords` is 0–32767. This intentionally strict subset rejects duplicate or terminal zero-length runs.

`StylImport.Document` contains the editable normalized model. `Records` separately retains every raw field, mapped offset, and resolved name, even where normalization merges visual styles. Warnings identify unresolved fonts, unsupported face bits (outline/shadow/condense/extend and unknown bits), and nonzero filler. Height and ascent are retained as historical metrics, not applied as layout. The high byte is the face byte; the low byte is preserved filler. Colors retain all 16 bits. Editing or serializing the document does not carry raw legacy metadata; keep the import result or original blob if needed. There is no legacy exporter.

## Save/load and verification

`EncodeStyledText` / `DecodeStyledText` preserve text, defaults, run boundaries after normalization, and all supported attributes. This is a new versioned binary format, **not** RTF or `styl`. See [the wire specification](../resources/buoy-docs/api.md#binary-format-v1). Decoders reject malformed UTF-8, NUL, unknown versions/flags, invalid coverage, adjacent equal runs, truncation, and trailing bytes.

Maintainers run the private validation suite before release. See the repository README for public build instructions.
