---
title: "Attributes"
description: ""
lead: ""
date: 2023-05-29T11:35:22+02:00
lastmod: 2023-05-29T11:35:22+02:00
draft: false
images: []
menu:
  docs:
    parent: "concepts"
weight: 10
toc: true
---

## Style Attributes

> If you haven't yet, read introduction to Fleather document model called
> Parchment [here](../data-and-document).

Style attributes in Parchment documents are simple key-value pairs, where
keys identify the attribute and value describes the style applied, for
instance, `{ "heading": 1 }` defines heading style for a line of
text with value `1` (equivalent of `h1` in HTML).

Note that one attribute can describe multiple different styles depending
on the current value. E.g. attribute with key "heading" can be set to values
`1`, `2` or `3`, equivalents of `h1`, `h2` and `h3` in HTML. This prevents
a line of text from being formatted as `h1` and `h2` heading at the same time,
which is intentional.

Additionally, each attribute gets assigned one of two scopes. An
attribute can be either *inline-scoped* or *line-scoped*, but not both.
A good example of an inline-scoped attribute is "bold" attribute. Bold
style can be applied to any character within a line, but not to the
line itself. Similarly "heading" style is line-scoped and has effect
only on the line as a whole.

Below table summarizes information about all currently supported
attributes in Fleather:

| Name            | Key         | Scope    | Type     | Valid values                                 |
| --------------- | ----------- | -------- | -------- | -------------------------------------------- |
| Bold            | `b`         | `inline` | `bool`   | `true`                                       |
| Italic          | `i`         | `inline` | `bool`   | `true`                                       |
| Strikethrough   | `s`         | `inline` | `bool`   | `true`                                       |
| Code            | `c`         | `inline` | `bool`   | `true`                                       |
| Backgound color | `bg`        | `inline` | `int`    | 32 lower bits of `int`                       |
| Foregound color | `fg`        | `inline` | `int`    | 32 lower bits of `int`                       |
| Link            | `a`         | `inline` | `String` | Non-empty string                             |
| Heading         | `heading`   | `line`   | `int`    | `1` to `6`                                   |
| Block           | `block`     | `line`   | `String` | `"ul"`, `"ol"`, `"code"`, `"quote"`          |
| Direction       | `direction` | `line`   | `String` | `"rtl"`, `"ltr"`                             |
| Indentation     | `indent`    | `line`   | `int`    | any positive `int`                           |
| Alignment       | `alignment` | `line`   | `String` | `"left"`, `"right"`, `"center"`, `"justify"` |
| Embed           | `embed`     | `inline` | `Map`    | `"hr"`, `"image"`, custom embeds             |

Removing a specific style is as simple as setting corresponding
attribute to `null`.

Here is an example of applying some styles to a document:

```dart
import 'package:parchment/parchment.dart';

void makeItPretty(ParchmentDocument document) {
  /// All attributes can be accessed through [ParchmentAttribute] class.

  // Format 5 characters starting at index 0 as bold.
  document.format(0, 5, ParchmentAttribute.bold);

  // Similarly for italic.
  document.format(0, 5, ParchmentAttribute.italic);

  // Format the first line as a heading (level 1).
  // Note that there is no need to specify character range of the whole
  // line. Simply set index position to anywhere within the line and
  // length to 0.
  document.format(0, 0, ParchmentAttribute.heading.level1);

  // Add a link:
  document.format(10, 15, ParchmentAttribute.link.fromString('https://github.com'));

  // Format a line as code block. Similarly to heading styles there is no need
  // to specify the whole character range of the line. In following example:
  // whichever line is at character index 23 in the document will get
  // formatted as code block.
  document.format(23, 0, ParchmentAttribute.block.code);

  // Remove heading style from the first line. All attributes
  // have `unset` property which can be used the same way.
  document.format(0, 0, ParchmentAttribute.heading.unset);
}
```

### How attributes are stored in Deltas

As mentioned previously a document delta consists of a sequence of `insert`
operations. Attributes (if any) are stored as metadata on each of the
operations.

There is a difference in how line and inline-scoped attributes are
handled. Since Deltas are essentially a flat data structure there is
nothing in the format itself to represent a line of text, which is
required to allow storing line-scoped style attributes.

To solve this issue Parchment document (similarly to Quill.js) reserves the
**newline** character (aka `\n` and `0x0A`) as storage for line-scoped
styles.

Parchment document model is designed to enforce this rule and
prevents malformed changes from being composed into a document. For
instance, an attempt to apply "bold" style to a newline character
will have no effect.

Below is an example of Parchment document's Delta with two lines of text.
The first line is formatted as an `h1` heading and on the second line
there is bold-styled word "Flutter":

```dart
var delta = new Delta();
delta
  ..insert('Fleather Editor')
  ..insert('\n', attributes: {'heading': 1})
  ..insert('A rich text editor for ');
  ..insert('Flutter', attributes: {'b': true});
  ..insert('\n');
```

Note that there is no block-level scope for style attributes. Again,
given flat structure of Deltas there is nothing that can represent a
block of lines which share the same style, e.g. bullet list. And we
already reserved newline character for line styles.

As it turns out, this is not a big issue and it is possible to achieve
a friendly user experience without this extra level in a document model.

The `block` attribute in Parchment documents is line-scoped. To change a
group of lines from "bullet list" to "number list" we need to update
block style on each of the lines individually. Fleather editor abstracts
away such details with help of [heuristic rules](../heuristics).
