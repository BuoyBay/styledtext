---
title: "Using StyledText"
summary: "Construct styled content, edit scalar ranges, and save it without losing formatting."
category: "Guide"
order: 10
keywords: ["styledtext", "editing", "serialization"]
symbols: ["EncodeStyledText", "DecodeStyledText"]
related: ["index.md", "api.md", "typed-api.md"]
---

# Getting started

Install the sealed styledtext package in your project's packages directory. The runnable example is `examples/styledtext/main.bui` in this repository. It creates a Unicode document, applies a bold range, inserts content, and round-trips the result through the binary codec.

Create `New StyledText(content)` for defaults or `New StyledText(content, style)` for an explicit default. Prepare a `TextStyle` and call `SetStyle(start, length, style)` to replace range attributes. `Insert(position, content)` inherits nearby attributes; the three-argument overload uses the supplied style. `Delete(start, length)` and `Slice(start, length)` operate on scalars. Combining marks are separate scalars. `StyleAt` and `Runs` return defensive snapshots.

Encode using `EncodeStyledText(document)` and decode using `DecodeStyledText(data)`. Keep the returned Bytes binary; always specify an encoding when decoding any bytes into a String. The codec format is not RTF.

## Recovering from a batch edit

Each rejected edit leaves its receiver unchanged, but earlier successful edits in a batch remain applied. To publish a batch only after every edit and serialization succeeds, work on `document.Slice(0, document.Length())`. This independent document preserves the text, styles, and document default, including for empty content. Assigning `candidate = document` instead would share the same mutable object.

```buoy
Dim document As New StyledText("A😀é")
Try
    Dim candidate As StyledText = document.Slice(0, document.Length())
    candidate.Delete(1, 1) ' Remove the emoji: positions count Unicode scalars.
    candidate.Insert(1, "B")
    Dim saved As Bytes = EncodeStyledText(candidate)
    document = candidate
Catch e As InvalidArgumentException
    Print "Batch rejected: " + e.Message
End Try
```

Keep the assignment after encoding: a valid model can still exceed the codec's separate byte limit. On rejection, discard the candidate; the original document remains available. This is an in-memory publication pattern, not a disk-save transaction. References already held by other consumers still refer to the original document after reassignment. A slice copies the editable model only; retain `StylImport.Records` or the original legacy blob separately if historical metadata matters.

For legacy imports provide typed owned `StylOffsetMapper` and `StylFontResolver` closures. Decode the original text separately with its known encoding, and map historical offsets explicitly. Keep the returned `StylImport.Records` if original metrics, font IDs or unsupported face bits matter.

See [API](api.md) for bounds and wire format, or [overview](index.md).
