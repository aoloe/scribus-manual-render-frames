# Scribus Render Frames

## What are Render Frames

_Render Frames_ are frames that render a PDF generated by an external application that processes plain text commands.  
The PDF is rendered as an image.

When exporting the Scribus document to PDF, you can check "Embed PDF & EPS (EXPERIMENTAL)" to get the generated PDF to be embedded _as is_ in the final PDF, instead of being rasterized.

## Tweaking the render frames

If you want to configure more than what the Render Frame exposes to you, you can always create a new type of render frame by duplicating and slightly renaming one of the existing ones.

As an example, you can do that for PdfLatex render frame that need a different preamble.

The files to be modified are in the Scribus installation folder, under `share/scribus/editorconfig/`.

## Troubleshooting

By default, if you don't have pdflatex installed and _correctly_ configured, Scribus will give you the error "The application "pdflatex --interaction nonstopmode" failed to start! Please check the path: " and the frame will have a big blue cross on it with the text "Render Error".

There are four things you can do:

- If you want to use _pdflatex_ and _LaTeX_ is not installed, you need to install it.
- If _pdflatex_ is installed but Scribus can't find it, check that it is correctly installed and / or go to _File > Preferences_ in the _External Tools_ section, and change the command to reflect your installation.
- If you want to use a different tool than LaTeX (as an exemple LilyPond, for creating music scores), in the _External Tools_ section of _File > Preferences_ move that tool to the first place in the list, so that it's the default one.
- Again, if you want to use a different tool than _LaTeX_, but only for one single usage, use the context menu on the _Render Frame_, trigger _Edit Source_, and choose the right _Program_.

If you see a red cross and the name of the generated PDF that should be rendered, then Ghostscript is not _correctly_ installed or configured.  
Make sure that Ghostscript is correctly installed and that Scribus is correctly configured to use it by having a look at the _Post Script Interpreter_ in the _External Tools_ section of _File > Preferences_. 

## Defining render frame sources

Each source is defined in an XML file.

- In the source code the files are in `resources/editorconfig/`
- When installed, the files are in `share/scribus/editorconfig/`

If you want to create a new type of render frame take inspiration from one of the existing ones and use it to create your own XML file.

### Creating a fake renderer

If you want to test the render frames but don't have any of the tool installed, this Bash script creates a sample PDF file with the name that Scribus expects:

```sh
#!/usr/bin/env bash

# This is a fake version of pdflatex that simply outputs an hello world PDF.
# It has been created to test the Scribus render frame, without installing pdflatex

input_file="${@: -1}" # the last argument
# output_file="${input_file%.tex}.pdf" # if it would be a .tex file...
output_file=$input_file".pdf" # but it's without extension

printf '%s\n' \
    '%PDF-1.4' \
    '%€‚ƒ' \
    '1 0 obj<</Type/Catalog/Pages 2 0 R>>endobj' \
    '2 0 obj<</Type/Pages/Kids[3 0 R]/Count 1>>endobj' \
    '3 0 obj<</Type/Page/Parent 2 0 R/MediaBox[0 0 595 792]/Resources<</Font<</F1 4 0 R>>>>/Contents 5 0 R>>endobj' \
    '4 0 obj<</Type/Font/Subtype/Type1/Name/F1/BaseFont/Helvetica/Encoding/MacRomanEncoding>>endobj' \
    '5 0 obj<</Length 44>>' \
    'stream' \
    'BT /F1 20 Tf 220 700 Td (Hello World!) Tj ET' \
    'endstream' \
    'endobj' \
    'xref' \
    '0 6 ' \
    '0000000000 65535 f ' \
    '0000000015 00000 n ' \
    '0000000058 00000 n ' \
    '0000000107 00000 n ' \
    '0000000217 00000 n ' \
    '0000000312 00000 n ' \
    'trailer' \
    '<</Root 1 0 R/Size 6>>' \
    'startxref' \
    '403' \
    '%%EOF' \ > "$output_file"
```

For testing purposes, you can then

- name the script above `faketex` the script above in your `~/bin/` folder,
- in your `share/scribus/editorconfig/`, copy `100_latex.xml` inito `150_faketex.xml`,
- set the description to `FakeTeX`,
- in the command, replace `pdflatex` by `faketex`,
- restart Scribus and in the preferences use FakeTeX as the first option.
