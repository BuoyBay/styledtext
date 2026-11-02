# Portable styled Unicode text + .styl

`Import styledtext` provides a portable Unicode text model with normalized style runs, editing, exact binary save/load, and a bounded importer for classic Mac `styl`/REALbasic `TextStyleData` data. It is an independent implementation, tested with a freshly sealed consumer.

The model supports font name, fractional point size, bold, italic, underline, and RGB16 foreground color. It does not change core `TextArea`, render text, implement selection/typing UI, parse RTF, or provide paragraph layout. For native editing, [StyledTextUI](docs/API.md) embeds GTK/AppKit/Rich Edit controls with selection formatting, typing styles and undo/redo. The separate [richtext](docs/API.md) package handles macOS RTF conversion; its plain-text decoder discards formatting.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add styledtext --git https://github.com/BuoyBay/styledtext.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## Example

```basic
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

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer styledtext
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```

## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
