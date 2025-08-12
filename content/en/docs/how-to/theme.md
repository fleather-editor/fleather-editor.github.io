---
title: "Apply Themes"
description: " How to theme your editor"
lead: ""
date: 2024-04-09T11:53:37+02:00
lastmod: 2024-04-09T11:53:37+02:00
draft: false
images: []
menu:
  docs:
    parent: "how-to"
weight: 999
toc: true
---

## Customizing Text Styles and Heading Presets

### Using the FleatherTheme Widget

The FleatherTheme widget is designed to customize the appearance of document elements within both the FleatherToolbar and FleatherEditor. It accepts a FleatherThemeData object, which allows you to define your desired styling.

``` dart
FleatherTheme fleatherTheme = FleatherTheme(data: FleatherThemeData());
```
### Customizing the FleatherThemeData Widget

To customize the FleatherTheme widget, you must specify all style variations. Note that it's not possible to define only a few styles. Here's how some styles work:

* Bold, Italic, Underline, Strikethrough: These styles overlay the original text styling with the specified changes. For example, they are applied using a basic TextStyle widget that only modifies the relevant property.
* Paragraph Style: This style applies to normal text. It's defined using a TextBlockTheme object, which includes style, decoration, and spacing variables.

``` dart
TextBlockTheme defaultTheme = TextBlockTheme(
  style: Theme.of(context).textTheme.bodyMedium!,
  spacing: VerticalSpacing(top: 1, bottom: 1),
  lineSpacing: VerticalSpacing(top: 3, bottom: 0),
  decoration: BoxDecoration(),
);

FleatherThemeData fleatherTheme = FleatherThemeData(
  bold: TextStyle(fontWeight: FontWeight.bold),
  italic: TextStyle(fontStyle: FontStyle.italic),
  underline: TextStyle(decoration: TextDecoration.underline),
  strikethrough: TextStyle(decoration: TextDecoration.lineThrough),
  link: TextStyle(color: Colors.blue.shade700),
  paragraph: defaultTheme,
  heading1: TextBlockTheme(),
  heading2: TextBlockTheme(),
  heading3: TextBlockTheme(),
  heading4: TextBlockTheme(),
  heading6: TextBlockTheme()
  
  heading5: TextBlockTheme(),
  lists: TextBlockTheme(),
  quote: TextBlockTheme(),
  code: TextBlockTheme(),
  inlineCode: InlineCodeThemeData(
    style: TextStyle(),
    backgroundColor: Colors.grey.shade200,
  ),
);
```

### Applying a theme.

To apply your custom theme, wrap the FleatherToolbar or FleatherEditor with the FleatherTheme widget and pass in your FleatherThemeData object as shown above.

``` dart
FleatherThemeData customTheme = FleatherThemeData(
  // Define your custom styles here
);

FleatherTheme(
  data: customTheme,
  child: FleatherToolbar(
    // Toolbar content
  ),
);
```