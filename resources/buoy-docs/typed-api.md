---
title: "styledtext typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["StylFontResolver", "StylImport", "StylOffsetMapper", "StylRecord", "StyleRun", "StyledText", "TextStyle"]
dependencies: []
symbols: ["StylFontResolver", "StylImport", "StylOffsetMapper", "StylRecord", "StyleRun", "StyledText", "TextStyle"]
related: ["api.md", "getting-started.md", "index.md"]
---

# styledtext typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## TextStyle

`Public Class TextStyle`

Portable attributes. API boundaries copy these mutable values.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `FontName` | `String` | No | No | `""` | — |
| `FontSize` | `Double` | No | No | `0.0` | — |
| `Bold` | `Boolean` | No | No | `False` | — |
| `Italic` | `Boolean` | No | No | `False` | — |
| `Underline` | `Boolean` | No | No | `False` | — |
| `Red` | `Integer` | No | No | `0` | — |
| `Green` | `Integer` | No | No | `0` | — |
| `Blue` | `Integer` | No | No | `0` | — |

### Equals

`Public Function Equals(other As TextStyle) As Boolean`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `other` | `TextStyle` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Boolean` |

### Copy

`Public Function Copy() As TextStyle`

No parameters.

#### Returns

| Type |
| --- |
| `TextStyle` |

## StyleRun

`Public Class StyleRun`

Snapshot of a positive-length run at a zero-based Unicode scalar offset.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Start` | `Integer` | No | No | `0` | — |
| `Length` | `Integer` | No | No | `0` | — |
| `Style` | `TextStyle` | No | No | — | — |

## StyledText

`Public Class StyledText`

Editing uses Unicode scalar positions, never UTF-8 bytes or grapheme clusters.

### Constructor

`Public Sub Constructor(content As String = "")`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `content` | `String` | `ByVal` | `""` |

Returns: no value.

### Constructor (overload 2)

`Public Sub Constructor(content As String, style As TextStyle)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `content` | `String` | `ByVal` | Required |
| `style` | `TextStyle` | `ByVal` | Required |

Returns: no value.

### FromRuns

`Public Shared Function FromRuns(content As String, runs As Array Of StyleRun, defaultStyle As TextStyle) As StyledText`

Build from exact coverage; adjacent equal styles are coalesced.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `content` | `String` | `ByVal` | Required |
| `runs` | `Array Of StyleRun` | `ByVal` | Required |
| `defaultStyle` | `TextStyle` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `StyledText` |

### PlainText

`Public Function PlainText() As String`

No parameters.

#### Returns

| Type |
| --- |
| `String` |

### Length

`Public Function Length() As Integer`

No parameters.

#### Returns

| Type |
| --- |
| `Integer` |

### DefaultStyle

`Public Function DefaultStyle() As TextStyle`

No parameters.

#### Returns

| Type |
| --- |
| `TextStyle` |

### Runs

`Public Function Runs() As Array Of StyleRun`

No parameters.

#### Returns

| Type |
| --- |
| `Array Of StyleRun` |

### StyleAt

`Public Function StyleAt(position As Integer) As TextStyle`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `position` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `TextStyle` |

### SetStyle

`Public Sub SetStyle(start As Integer, length As Integer, style As TextStyle)`

Replace all attributes in the selected range; a zero length is a no-op.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `start` | `Integer` | `ByVal` | Required |
| `length` | `Integer` | `ByVal` | Required |
| `style` | `TextStyle` | `ByVal` | Required |

Returns: no value.

### Insert

`Public Sub Insert(position As Integer, content As String)`

Plain insertion inherits the left character, or the right at zero, or default when empty.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `position` | `Integer` | `ByVal` | Required |
| `content` | `String` | `ByVal` | Required |

Returns: no value.

### Insert (overload 2)

`Public Sub Insert(position As Integer, content As String, style As TextStyle)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `position` | `Integer` | `ByVal` | Required |
| `content` | `String` | `ByVal` | Required |
| `style` | `TextStyle` | `ByVal` | Required |

Returns: no value.

### Delete

`Public Sub Delete(start As Integer, length As Integer)`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `start` | `Integer` | `ByVal` | Required |
| `length` | `Integer` | `ByVal` | Required |

Returns: no value.

### Slice

`Public Function Slice(start As Integer, length As Integer) As StyledText`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `start` | `Integer` | `ByVal` | Required |
| `length` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `StyledText` |

## StyledText functions

### EncodeStyledText

`Public Function EncodeStyledText(document As StyledText) As Bytes`

Versioned binary interchange; preserves content, defaults and all supported attributes.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `document` | `StyledText` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Bytes` |

### DecodeStyledText

`Public Function DecodeStyledText(data As Bytes) As StyledText`

Strict bounded decoder: exact coverage, canonical runs, no trailing bytes.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `data` | `Bytes` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `StyledText` |

### StylOffsetMapper

`Public Closure Function StylOffsetMapper(legacyOffset As Integer) As Integer`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `legacyOffset` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Integer` |

### StylFontResolver

`Public Closure Function StylFontResolver(fontID As Integer) As String`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `fontID` | `Integer` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `String` |

### ImportStyl

`Public Function ImportStyl(content As String, data As Bytes, mapOffset As StylOffsetMapper, resolveFont As StylFontResolver, maxRecords As Integer = 10000) As StylImport`

Classic big-endian Mac styl data, with explicit caller-owned offset and font mapping.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `content` | `String` | `ByVal` | Required |
| `data` | `Bytes` | `ByVal` | Required |
| `mapOffset` | `StylOffsetMapper` | `ByVal` | Required |
| `resolveFont` | `StylFontResolver` | `ByVal` | Required |
| `maxRecords` | `Integer` | `ByVal` | `10000` |

#### Returns

| Type |
| --- |
| `StylImport` |

## StylRecord

`Public Class StylRecord`

Original record retained independently of normalized, editable text.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Start` | `Integer` | No | No | `0` | — |
| `Height` | `Integer` | No | No | `0` | — |
| `Ascent` | `Integer` | No | No | `0` | — |
| `FontID` | `Integer` | No | No | `0` | — |
| `Face` | `Integer` | No | No | `0` | — |
| `Filler` | `Integer` | No | No | `0` | — |
| `Size` | `Integer` | No | No | `0` | — |
| `Red` | `Integer` | No | No | `0` | — |
| `Green` | `Integer` | No | No | `0` | — |
| `Blue` | `Integer` | No | No | `0` | — |
| `MappedStart` | `Integer` | No | No | `0` | — |
| `ResolvedFontName` | `String` | No | No | `""` | — |

## StylImport

`Public Class StylImport`

A detached import result; edits to Document do not rewrite the original records.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `Document` | `StyledText` | No | No | — | — |
| `Records` | `Array Of StylRecord` | No | No | `[]` | — |
| `Warnings` | `Array Of String` | No | No | `[]` | — |
