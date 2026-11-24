# Font Development with FontLab for Baravu

<img width="1200" height="735" alt="image" src="https://github.com/user-attachments/assets/9f1f3d67-329d-4026-92ff-4fb9f5c1f595" />

This guide documents the development, modification, and maintenance of the **Baravu Tulu Tigalari Unicode font** using FontLab.

Unlike a font-development tutorial that starts with an empty project, this guide focuses on working with an existing TrueType font. It covers importing the font, editing glyphs, preserving Unicode character mappings, maintaining OpenType functionality, updating font metadata, testing, and exporting the finished font.

The current modified font is distributed as `TuluBaravu.ttf`. It preserves the original glyphs of the OpenTuluFont Baravu file while being Unicode compatible.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Understanding the Existing Font](#understanding-the-existing-font)
- [Setting Up the Project](#setting-up-the-project)
- [Font Metadata and Naming](#font-metadata-and-naming)
- [Editing and Creating Glyphs](#editing-and-creating-glyphs)
- [Unicode Character Mapping](#unicode-character-mapping)
- [OpenType Features and Glyph Substitution](#opentype-features-and-glyph-substitution)
- [Spacing and Metrics](#spacing-and-metrics)
- [Testing and Validation](#testing-and-validation)
- [Exporting the Font](#exporting-the-font)
- [Version Control and Distribution](#version-control-and-distribution)
- [Tips and Best Practices](#tips-and-best-practices)
- [Resources](#resources)

---

## Prerequisites

Before beginning development, ensure that you have the following:

1. **FontLab:** Download and install [FontLab](https://www.fontlab.com/).
2. **Existing Baravu font:** Use the latest available `.ttf` file as the starting point.
3. **Unicode reference:** Consult the Unicode Tigalari character chart and the relevant Unicode documentation.
4. **Font testing environment:** A system capable of installing and rendering TrueType fonts.
5. **Backup:** Keep an untouched copy of the original font before making modifications.

### Recommended tools

- [FontLab](https://www.fontlab.com/) – Font editing and development.
- [FontForge](https://fontforge.org/) – An alternative font editor and inspection tool.
- [fontTools](https://fonttools.readthedocs.io/) – Python library for inspecting and validating font files.
- [Unicode Character Code Charts](https://www.unicode.org/charts/) – Official Unicode character references.

---

## Understanding the Existing Font

Before editing Baravu, it is important to understand its existing structure.

The current `Tulu.ttf` file contains:

| Property | Value |
|---|---|
| Format | TrueType (.ttf) |
| Font family | Baravu |
| Style | Regular |
| Full name | Baravu Regular |
| PostScript name | Baravu-Regular |
| Version | 1.000 |
| Glyph count | 1,281 |
| Mapped Unicode code points | 163 |

The font also contains several important OpenType tables:

- **GSUB:** Controls glyph substitution, including contextual substitutions and conjunct formation.
- **GPOS:** Controls glyph positioning and adjustments.
- **GDEF:** Provides glyph classification and information used by OpenType layout processing.

These tables are important for scripts such as Tigalari, where character sequences may require contextual glyph substitutions and positioning.

**Important:** Renaming a font does not create new glyphs, change Unicode assignments, or automatically improve script support. Those require separate modifications and testing.

---

## Setting Up the Project

1. Launch FontLab.
2. Open the existing `Tulu.ttf` file using **File > Open**.
3. Inspect the glyph window and confirm that the existing glyphs have loaded.
4. Inspect the font information and character mappings.
5. Save a working copy in FontLab's native project format.

Keep the original `.ttf` file separate from the editable project.

Recommended project structure:

```text
Baravu/
├── README.md
├── LICENSE
├── fonts/
│   ├── TuluBaravu.ttf
│   └── previous-versions/
├── source/
│   └── Baravu.vfc
├── documentation/
│   └── development-guide.md
└── tests/
    ├── unicode-tests.txt
    └── rendering-tests.txt
```

The `.vfc` file represents the editable FontLab project. The `.ttf` file is the compiled font intended for installation and distribution.

---

## Font Metadata and Naming

Font metadata determines how the font is identified by operating systems, applications, and font managers.

To inspect or modify metadata:

1. Open the font information panel in FontLab.
2. Locate the font naming and identification fields.
3. Update the family name, style name, full name, and PostScript name.
4. Ensure that the naming fields are consistent.
5. Update the version number when publishing a new release.

### Baravu naming conventions

| Metadata field | Recommended value |
|---|---|
| Family Name | Baravu |
| Subfamily | Regular |
| Full Name | Baravu Regular |
| PostScript Name | Baravu-Regular |
| Version | 1.000 |

When producing additional styles, use consistent naming conventions, such as Baravu Bold or Baravu Italic, if those styles are actually developed.

Avoid changing the font's naming metadata without checking the resulting font installation behavior. Operating systems may cache previously installed fonts, so uninstalling older versions may be necessary when testing renamed releases.

---

## Editing and Creating Glyphs

Baravu is an existing Tigalari font. Consequently, glyph development should begin by examining the existing character designs rather than recreating the entire script.

### Editing existing glyphs

1. Open the Glyph window.
2. Locate the character you want to modify.
3. Double-click the glyph to open its editing window.
4. Use the Pen Tool and Bézier curve controls to modify its outline.
5. Adjust nodes and handles to maintain consistent proportions.
6. Compare the modified glyph with related characters.

### Creating new glyphs

1. Identify the Unicode character that requires support.
2. Create a corresponding glyph.
3. Draw the glyph using the existing font's visual style.
4. Assign the appropriate Unicode value.
5. Adjust its advance width and side bearings.
6. Test it alongside related characters.

Maintain consistency in stroke thickness, proportions, curvature, and alignment.

Do not create duplicate glyphs for characters that already have appropriate Unicode assignments.

---

## Unicode Character Mapping

Unicode mapping is particularly important for Baravu because it is intended to support Tigalari text digitally.

Each encoded character should map to the appropriate glyph in the font.

When editing character mappings:

1. Open the font's character mapping interface.
2. Identify the relevant Unicode code point.
3. Assign the corresponding glyph.
4. Check that the mapping does not conflict with an existing character.
5. Test the character by entering its Unicode value in a compatible application.

Consult the official Unicode Tigalari block when determining character assignments.

**Unicode reference:** [Tigalari Unicode Character Chart](https://www.unicode.org/charts/)

Pay particular attention to:

- Independent vowels.
- Consonants.
- Dependent vowel signs.
- Virama.
- Consonant conjuncts.
- Numerals.
- Punctuation and other script-specific characters.

A font containing a glyph does not necessarily mean that the corresponding Unicode character is correctly mapped. Both the glyph and its character assignment must be checked.

---

## OpenType Features and Glyph Substitution

OpenType functionality is essential for scripts that require contextual character shaping.

Baravu includes existing GSUB, GPOS, and GDEF tables. These should be preserved and tested when making changes.

### GSUB

The Glyph Substitution table allows a font to replace individual glyphs or sequences of glyphs with alternate forms.

Examples include:

- Contextual glyph substitutions.
- Consonant conjunct formation.
- Alternate glyph selection.
- Script-specific substitutions.

### GPOS

The Glyph Positioning table controls the positioning of glyphs relative to one another.

It can be used for:

- Mark positioning.
- Combining character placement.
- Spacing adjustments.
- Contextual positioning.

### GDEF

The Glyph Definition table provides classifications and information about glyphs for use by OpenType layout engines.

When editing these features, avoid removing existing substitution and positioning rules unless they have been deliberately replaced and tested.

A font that contains the correct Unicode glyphs can still render incorrectly if its OpenType shaping rules are missing or defective.

---

## Spacing and Metrics

Proper spacing is necessary for readable text.

For each glyph, inspect:

- Advance width.
- Left side bearing.
- Right side bearing.
- Baseline alignment.
- Vertical positioning.

Test spacing with actual Tigalari words and character sequences rather than relying exclusively on isolated glyph previews.

When modifying glyph dimensions, ensure that related characters remain visually consistent.

---

## Testing and Validation

Testing should be performed throughout development rather than only after exporting the font.

### 1. Glyph coverage

Verify that all intended Unicode characters are mapped to the correct glyphs.

### 2. Rendering

Install the exported font and test it in multiple applications.

Check:

- Independent vowels.
- Consonant-vowel combinations.
- Dependent vowel signs.
- Virama behavior.
- Conjunct formation.
- Combining marks.
- Numerals and punctuation.

### 3. OpenType functionality

Verify that GSUB and GPOS features operate correctly in applications with suitable Tigalari shaping support.

### 4. Metadata

Inspect the exported font to ensure that its family name, full name, PostScript name, and version are correct.

### 5. Font integrity

Use FontLab's available font validation and audit tools to identify malformed glyph outlines, missing references, and other potential problems.

For additional inspection, Python fontTools can be used to examine font tables and character mappings.

Example:

```python
from fontTools.ttLib import TTFont

font = TTFont("TuluBaravu.ttf")

print("Font tables:", font.keys())
print("Glyph count:", len(font.getGlyphOrder()))

for table in font["cmap"].tables:
    if table.isUnicode():
        print("Unicode mappings:", len(table.cmap))
```

---

## Exporting the Font

Once editing and validation are complete, export the font.

1. Open the export interface in FontLab.
2. Select TrueType (.ttf) as the output format.
3. Confirm the font naming and version information.
4. Export the font.
5. Install the exported file on your system.
6. Test it using actual Tigalari text.

Keep the editable FontLab project alongside the exported font.

Do not overwrite the only copy of the previous working version.

---

## Version Control and Distribution

Baravu is maintained as an open-source font project. Version control helps preserve its development history and makes collaboration easier.

When publishing modifications:

1. Keep the original project's license and attribution.
2. Document the changes made to the font.
3. Use descriptive commit messages.
4. Preserve previous releases where practical.
5. Include the editable source files when possible.
6. Clearly identify the latest stable `.ttf` release.

Suggested Git commit messages:

```text
Update font family name to Baravu
Improve Tigalari glyph outlines
Correct Unicode character mappings
Update OpenType substitution rules
Fix glyph positioning
Release updated Baravu font
```

---

## Tips and Best Practices

- **Preserve existing functionality:** Do not remove existing Unicode mappings or OpenType tables without understanding their purpose.
- **Work from the original source:** Keep an untouched copy of the original font.
- **Use Unicode consistently:** Assign characters according to the official Unicode standard.
- **Test real text:** Individual glyph previews are insufficient for evaluating script shaping.
- **Maintain consistent design:** Ensure that new glyphs match the existing typeface.
- **Document changes:** Record modifications to glyphs, metadata, mappings, and OpenType features.
- **Use version control:** Keep track of changes and make releases reproducible.
- **Test across applications:** Rendering can differ depending on the shaping engine and application.

---

## Resources

- [FontLab](https://www.fontlab.com/) – Font editing and development.
- [FontForge](https://fontforge.org/) – Alternative open-source font editor.
- [fontTools Documentation](https://fonttools.readthedocs.io/) – Font inspection and manipulation.
- [Unicode Consortium](https://www.unicode.org/) – Official Unicode standards.
- [Unicode Character Charts](https://www.unicode.org/charts/) – Character reference charts.
- [OpenType Specification](https://learn.microsoft.com/en-us/typography/opentype/spec/) – Technical documentation for OpenType fonts.
- [OpenTuluFont/Baravu](https://github.com/OpenTuluFont/baravu) – Original Baravu repository.

---

## Conclusion

Baravu development involves more than drawing glyphs. A functional Tigalari Unicode font requires correctly designed glyphs, accurate Unicode assignments, appropriate OpenType substitutions, and reliable character positioning.

FontLab provides the tools necessary to edit and maintain these components. By preserving the existing font structure, documenting changes, and testing the resulting font with actual Tigalari text, developers can continue improving Baravu while maintaining compatibility with Unicode-based digital text.

### Resources## Credits and Acknowledgements

This project builds upon the original Baravu Tulu Tigalari font project and acknowledges the work and contributions of its original developers.

Special thanks to:

- **Deepak Padukone** – [GitHub](https://github.com/deepakpadukone20)
- **Prahlad Tantry** – [GitHub](https://github.com/PrahladTantry)

Their work on the original project provides the foundation for this fork.

This fork maintains attribution to the original contributors while introducing updated font naming and metadata under the Baravu name.

**Original repository:** [OpenTuluFont/Baravu](https://github.com/OpenTuluFont/baravu)

- [FontLab Tutorials](https://www.fontlab.com/fontlab-vi/tutorials/)
- [Typography Basics](https://fonts.google.com/knowledge)
- [Font Creation Community](https://www.typophile.com/)

---

!

