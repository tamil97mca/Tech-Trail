# Comprehensive Markdown Cheat Sheet

This cheat sheet provides a complete overview of Markdown syntax, including basic and advanced features, along with their rendered outputs. It's designed for use with Docusaurus and other Markdown-compatible platforms.

<!-- ## Table of Contents

- [Comprehensive Markdown Cheat Sheet](#comprehensive-markdown-cheat-sheet)
  - [Table of Contents](#table-of-contents)
  - [Headings](#headings)
- [Heading 1](#heading-1)
  - [Heading 2](#heading-2)
    - [Heading 3](#heading-3)
      - [Heading 4](#heading-4)
        - [Heading 5](#heading-5)
          - [Heading 6](#heading-6)
- [Heading 1](#heading-1-1)
  - [Heading 2](#heading-2-1)
  - [Paragraphs and Line Breaks](#paragraphs-and-line-breaks)
  - [Emphasis](#emphasis)
    - [Bold](#bold)
    - [Italic](#italic)
    - [Bold and Italic](#bold-and-italic)
  - [Lists](#lists)
    - [Unordered Lists](#unordered-lists)
    - [Ordered Lists](#ordered-lists)
    - [Mixed Lists](#mixed-lists)
  - [Links](#links)
    - [Basic Links](#basic-links)
    - [Links with Titles](#links-with-titles)
    - [Reference-style Links](#reference-style-links)
    - [Automatic Links](#automatic-links)
  - [Images](#images)
    - [Basic Image Syntax](#basic-image-syntax)
    - [Image with Title](#image-with-title)
    - [Linked Image](#linked-image)
  - [Blockquotes](#blockquotes)
  - [Code](#code)
    - [Inline Code](#inline-code)
    - [Code Blocks](#code-blocks)
    - [Syntax Highlighting](#syntax-highlighting)
  - [Horizontal Rules](#horizontal-rules)
  - [Tables](#tables)
  - [Task Lists](#task-lists)
  - [Footnotes](#footnotes)
  - [Strikethrough](#strikethrough)
  - [Emojis](#emojis)
  - [Highlight](#highlight)
  - [Subscript and Superscript](#subscript-and-superscript)
  - [Diagrams with Mermaid](#diagrams-with-mermaid)
  - [Details/Summary (Expandable Content)](#detailssummary-expandable-content)
  - [Callouts](#callouts)
  - [Custom Heading IDs](#custom-heading-ids)
    - [My Custom Heading {#custom-id}](#my-custom-heading-custom-id)
  - [Definition Lists](#definition-lists)
  - [Escaping Characters](#escaping-characters) -->

## Headings

Markdown supports six levels of headings, corresponding to HTML's `<h1>` through `<h6>` tags.

```markdown
# Heading 1
## Heading 2
### Heading 3
#### Heading 4
##### Heading 5
###### Heading 6
```

Output:

# Heading 1
## Heading 2
### Heading 3
#### Heading 4
##### Heading 5
###### Heading 6

Alternatively, for `<h1>` and `<h2>`, you can use the following syntax:

```markdown
Heading 1
=========

Heading 2
---------
```

Output:

Heading 1
=========

Heading 2
---------

## Paragraphs and Line Breaks

To create paragraphs, use a blank line to separate one or more lines of text.

```markdown
This is the first paragraph.

This is the second paragraph.
```

Output:

This is the first paragraph.

This is the second paragraph.

For line breaks within a paragraph, end a line with two or more spaces, then type return.

```markdown
This is the first line.  
And this is the second line.
```

Output:

This is the first line.  
And this is the second line.

## Emphasis

### Bold

Use double asterisks `**` or double underscores `__` to create bold text.

```markdown
**This text is bold**
__This text is also bold__
```

Output:

**This text is bold**
__This text is also bold__

### Italic

Use single asterisks `*` or single underscores `_` to italicize text.

```markdown
*This text is italicized*
_This text is also italicized_
```

Output:

*This text is italicized*
_This text is also italicized_

### Bold and Italic

Combine bold and italic styles using triple asterisks `***` or triple underscores `___`.

```markdown
***This text is bold and italic***
___This text is also bold and italic___
```

Output:

***This text is bold and italic***
___This text is also bold and italic___

## Lists

### Unordered Lists

Use `-`, `*`, or `+` for unordered lists.

```markdown
- Item 1
- Item 2
  - Subitem 2.1
  - Subitem 2.2
- Item 3

* First item
* Second item
* Third item

+ One more item
+ Another item
+ Last item
```

Output:

- Item 1
- Item 2
  - Subitem 2.1
  - Subitem 2.2
- Item 3

* First item
* Second item
* Third item

+ One more item
+ Another item
+ Last item

### Ordered Lists

Use numbers followed by periods for ordered lists.

```markdown
1. First item
2. Second item
   1. Subitem 2.1
   2. Subitem 2.2
3. Third item
```

Output:

1. First item
2. Second item
   1. Subitem 2.1
   2. Subitem 2.2
3. Third item

### Mixed Lists

You can mix ordered and unordered lists.

```markdown
1. First ordered item
2. Second ordered item
   - Unordered subitem
   - Another unordered subitem
3. Third ordered item
   1. Ordered subitem
   2. Another ordered subitem
```

Output:

1. First ordered item
2. Second ordered item
   - Unordered subitem
   - Another unordered subitem
3. Third ordered item
   1. Ordered subitem
   2. Another ordered subitem

## Links

### Basic Links

Create links by enclosing the link text in square brackets `[]` and the URL in parentheses `()`.

```markdown
[Visit Docusaurus](https://docusaurus.io)
```

Output:

[Visit Docusaurus](https://docusaurus.io)

### Links with Titles

Add a title attribute by including it in quotes after the URL.

```markdown
[Docusaurus](https://docusaurus.io "Docusaurus Official Website")
```

Output:

[Docusaurus](https://docusaurus.io "Docusaurus Official Website")

### Reference-style Links

You can also use reference-style links for improved readability.

```markdown
[Docusaurus][1]
[Markdown Guide][markdown]

[1]: https://docusaurus.io
[markdown]: https://www.markdownguide.org "Markdown Guide"
```

Output:

[Docusaurus][1]
[Markdown Guide][markdown]

[1]: https://docusaurus.io
[markdown]: https://www.markdownguide.org "Markdown Guide"

<!-- ### Automatic Links -->

<!-- URLs and email addresses can be turned into links by enclosing them in angle brackets.

```markdown
<https://docusaurus.io>
<contact@example.com>
```

Output: -->

<!-- <https://docusaurus.io>
<contact@example.com> -->

## Images

### Basic Image Syntax

```markdown
![Docusaurus logo](https://docusaurus.io/img/docusaurus.png)
```

Output:

![Docusaurus logo](https://docusaurus.io/img/docusaurus.png)

### Image with Title

```markdown
![Docusaurus logo](https://docusaurus.io/img/docusaurus.png "Docusaurus Logo")
```

Output:

![Docusaurus logo](https://docusaurus.io/img/docusaurus.png "Docusaurus Logo")

### Linked Image

```markdown
[![Docusaurus logo](https://docusaurus.io/img/docusaurus.png)](https://docusaurus.io)
```

Output:

[![Docusaurus logo](https://docusaurus.io/img/docusaurus.png)](https://docusaurus.io)

## Blockquotes

Use the `>` character for blockquotes.

```markdown
> This is a blockquote.
> It can span multiple lines.
>
> > Nested blockquotes are also possible.
> > This is the second level.
>
> Back to the first level.
```

Output:

> This is a blockquote.
> It can span multiple lines.
>
> > Nested blockquotes are also possible.
> > This is the second level.
>
> Back to the first level.

## Code

### Inline Code

Use backticks `` ` `` for inline code.

```markdown
Use the `print()` function in Python.
```

Output:

Use the `print()` function in Python.

### Code Blocks

For code blocks, use triple backticks `` ``` `` or indent with four spaces.

    ```
    function greet(name) {
      console.log(`Hello, ${name}!`);
    }
    ```

Output:

```
function greet(name) {
  console.log(`Hello, ${name}!`);
}
```

### Syntax Highlighting

Specify the language after the opening triple backticks for syntax highlighting.

    ```python
    def greet(name):
        print(f"Hello, {name}!")
    ```

    ```javascript
    function greet(name) {
      console.log(`Hello, ${name}!`);
    }
    ```

Output:

```python
def greet(name):
    print(f"Hello, {name}!")
```

```javascript
function greet(name) {
  console.log(`Hello, ${name}!`);
}
```

## Horizontal Rules

Create horizontal rules using three or more hyphens, asterisks, or underscores.

```markdown
---
***
___
```

Output:

---
***
___

## Tables

Create tables using pipes `|` and hyphens `-`. Colons `:` can be used for alignment.

```markdown
| Left-aligned | Center-aligned | Right-aligned |
|:-------------|:--------------:|--------------:|
| Left         | Center         | Right         |
| aligned      | aligned        | aligned       |
```

Output:

| Left-aligned | Center-aligned | Right-aligned |
|:-------------|:--------------:|--------------:|
| Left         | Center         | Right         |
| aligned      | aligned        | aligned       |

## Task Lists

Create task lists using square brackets with a space `[ ]` for unchecked items and `[x]` for checked items.

```markdown
- [x] Complete task
- [ ] Incomplete task
- [x] Another complete task
  - [ ] Subtask 1
  - [x] Subtask 2
```

Output:

- [x] Complete task
- [ ] Incomplete task
- [x] Another complete task
  - [ ] Subtask 1
  - [x] Subtask 2

## Footnotes

Add footnotes using `[^]` for the reference and define them later in the document.

```markdown
Here's a sentence with a footnote.[^1]

[^1]: This is the footnote content.
```

Output:

Here's a sentence with a footnote.[^1]

[^1]: This is the footnote content.

## Strikethrough

Use double tildes `~~` to strike through text.

```markdown
~~This text is struck through~~
```

Output:

~~This text is struck through~~

## Emojis

Many Markdown processors support emoji shortcodes.

```markdown
:smile: :heart: :thumbsup:
```

Output:

:smile: :heart: :thumbsup:

## Highlight

Some Markdown flavors support text highlighting using double equal signs `==`.

```markdown
==This text is highlighted==
```

Output:

==This text is highlighted==

## Subscript and Superscript

Some Markdown flavors support subscript with single tildes `~` and superscript with carets `^`.

```markdown
H~2~O
X^2^
```

Output:

H~2~O
X^2^

## Diagrams with Mermaid

Docusaurus supports Mermaid diagrams within code blocks.

    ```mermaid
    graph TD;
        A-->B;
        A-->C;
        B-->D;
        C-->D;
    ```

Output:

```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```

## Details/Summary (Expandable Content)

Create expandable sections using HTML `<details>` and `<summary>` tags.

```markdown
<details>
  <summary>Click to expand</summary>
  
  This content is hidden by default but can be expanded.
</details>
```

Output:

<details>
  <summary>Click to expand</summary>
  
  This content is hidden by default but can be expanded.
</details>

## Callouts

Docusaurus supports special callout blocks using `:::` syntax.

```markdown
:::note
This is a note
:::

:::tip
This is a tip
:::

:::info
This is an info box
:::

:::caution
This is a caution
:::

:::danger
This is a danger warning
:::
```

Output:

:::note
This is a note
:::

:::tip
This is a tip
:::

:::info
This is an info box
:::

:::caution
This is a caution
:::

:::danger
This is a danger warning
:::

## Custom Heading IDs

You can add custom IDs to headings for easier linking.

```markdown
### My Custom Heading {#custom-id}
```

Output:

### My Custom Heading {#custom-id}

## Definition Lists

Some Markdown flavors support definition lists.

```markdown
Term 1
: Definition 1

Term 2
: Definition 2a
: Definition 2b
```

Output:

Term 1
: Definition 1

Term 2
: Definition 2a
: Definition 2b

## Escaping Characters

Use a backslash `\` to escape Markdown formatting characters.

```markdown
\*This text is surrounded by literal asterisks\*
```

Output:

\*This text is surrounded by literal asterisks\*

This cheat sheet covers most Markdown features supported by Docusaurus and other popular Markdown processors, along with their rendered outputs. Remember that some advanced features may not be universally supported across all Markdown implementations.