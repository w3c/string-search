# String Searching: Explainer

## Participate

- **Issue tracker:** https://github.com/w3c/string-search/issues
- **Document:** https://www.w3.org/TR/string-search/

## Table of Contents

- [Introduction](#introduction)
- [Goals](#goals) · [Non-goals](#non-goals)
- [User Research](#user-research)
- [Principles](#principles)
- [Alternatives Considered](#alternatives-considered)
- [Accessibility, Internationalization, Privacy, and Security Considerations](#accessibility-internationalization-privacy-and-security-considerations)
- [References & Acknowledgements](#references--acknowledgements)

## Introduction

People often search web pages for text, for example with the browser's **Find** command (Ctrl+F / Cmd+F). Features like `window.find()` and [Scroll-to-Text Fragment](https://wicg.github.io/scroll-to-text-fragment/) also rely on this kind of matching.

What counts as a match depends on the user's language, keyboard, and expectations.

*String Searching* is a W3C document, listing the problems that need to be considered by people who specify or implement substring searches for natural-language text.

## Goals

- Give spec authors a reference for the concepts, terms, and pitfalls of natural-language substring search.
- State best practices for specs, implementations, and content authors, so that search works more consistently across browsers and languages.
- Help reviewers of features like `window.find()` and Scroll-to-Text Fragment ask the right internationalization questions.

## Non-goals

- Full-text search with indexes, stemming (`ran` → `run`), segmentation, or named-entity recognition. Substring matching should not return matches that contain words or characters the user didn't ask for.
- String matching in formal languages such as markup, CSS selectors, and identifiers. [CHARMOD-NORM](https://www.w3.org/TR/charmod-norm/) covers this.

## Principles

### Treat natural-language search as separate from identity matching

Formal languages need exact, predictable matching. Human search needs tolerant matching.

### Language, not script, drives expectations

German, Finnish and English all use the Latin script but expect different results. Implementations usually have to guess the language from hints like OS locale, browser UI language, active keyboard, or the page's `lang` attributes.

### More input effort → more selective matching

If the user makes an extra effort (Shift key, typing an accent), they probably want **only** the more specific result.

On a page containing `cafe`, `CAFE`, `café`, `CAFÉ`:

| User types | Should match |
|---|---|
| `e` | all four |
| `E` | `CAFE`, `CAFÉ` |
| `é` | `café`, `CAFÉ` |
| `É` | `CAFÉ` only |

### Expose search options

APIs and UIs that do string search should think about offering options such as:

- Case-sensitive / case-insensitive
- Kana folding (hiragana ↔ katakana)
- Unicode normalization
- Diacritic sensitivity, width folding, whole-word matching, etc.

## Alternatives Considered

### Exact code-point matching

Match only identical code-point sequences.

#### Pros

Simple, fast, and predictable.

#### Cons

Fails in almost every example above: accents, width, kana, etc.

#### Reason for rejection

Users don't experience text as code points. It works for formal languages but not for human search.

### One universal "fold everything" rule (such as NFKC + case fold + strip diacritics)

#### Pros

Catches many width, compatibility, and accent variants.

#### Cons

Too loose for some languages: Finnish users don't want `Hän` to match `Han`, and French `cote` ≠ `côté` in meaning.

#### Reason for rejection

Expectations depend on language. No single fold fits everyone.

## Accessibility, Internationalization, Privacy, and Security Considerations

### Internationalization

This is the main purpose of the document. Some of the main points: language (not script) sets expectations; multilingual pages make things harder; word boundaries in languages without spaces need segmentation.

### Accessibility

Tolerant matching helps users who find it hard to type exact characters, such as people using on-screen keyboards.

### Privacy

No privacy-related issues were found.

### Security

Looser matching makes confusable text (look-alikes such as mathematical letters) match more often. This is fine for Find, but **must not be reused** for security-sensitive comparisons like identifiers.

## References & Acknowledgements

Thanks to the W3C Internationalization Working Group and Interest Group, and to everyone who contributed to the Character Model series over the years. Special thanks to:

- Henri Sivonen, whose test page and issue notes supplied the Finnish/Turkish examples and several ideas
- Richard Ishida, for script analysis (Kashmiri and others)

See also:

- [Character Model for the WWW: String Matching](https://www.w3.org/TR/charmod-norm/)
- [Internationalization Best Practices for Spec Developers](https://www.w3.org/TR/international-specs/)
- [I18N Glossary](https://www.w3.org/TR/i18n-glossary/)
- [Scroll-to-Text Fragment](https://wicg.github.io/scroll-to-text-fragment/)
- [Unicode Normalization Forms (UAX #15)](https://www.unicode.org/reports/tr15/)
- [Unicode Ideographic Variation Database (UTS #37)](https://www.unicode.org/reports/tr37/)
