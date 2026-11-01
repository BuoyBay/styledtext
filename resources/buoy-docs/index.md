---
title: "StyledText"
summary: "Portable Unicode style runs, editing, exact save/load, and classic Mac styl import."
category: "Overview"
order: 1
keywords: ["styledtext", "style runs", "unicode", "TextStyleData", "styl"]
symbols: ["StyledText", "TextStyle", "StyleRun", "ImportStyl"]
related: ["getting-started.md", "api.md", "typed-api.md"]
---

# StyledText

A portable text model with font name/point size, bold/italic/underline, and RGB16 color. All positions count zero-based Unicode scalars; input UTF-8 must be valid and must not contain NUL (Buoy 0.72 string construction can discard U+0000 before the library sees it; binary input rejects NUL explicitly). Runs cover content exactly and adjacent equal styles merge. APIs copy mutable styles at their boundaries.

Start with [getting started](getting-started.md), then see the [API and binary format](api.md). This package supplies no rendering or native TextArea editor bridge. RTF conversion remains in the separate richtext package.
