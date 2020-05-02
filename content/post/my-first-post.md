+++ 
draft = true
date = 2020-04-22T22:19:36+07:00
title = "my first post"
description = "my first post description"
slug = "" 
tags = ["my","first"]
categories = ["test","sample"]
externalLink = ""
series = []
+++
# This is an H1
## This is an H2
###### This is an H6
This is also an H1
==================
This is also an H2
------------------

[bottom of the pagea]({{< ref "#who" >}})


*Italic characters* 

_Italic characters_

**bold characters**

__bold charactersabcd__

~~strikethrough text~~

**Unordered list**
*  Item 1
*  Item 2
*  Item 3
    *  Item 3a
    *  Item 3b
    *  Item 3c

**Ordered list**
1.  Step 1
2.  Step 2
3.  Step 3
    1.  Step 3.1
    2.  Step 3.2
    3.  Step 3.3

**List in list**
1.  Step 1
2.  Step 2
3.  Step 3
    *  Item 3a
	*  Item 3b
	*  Item 3c


Quotes or citations
Introducing my quote:

> Neque porro quisquam est qui 
> dolorem ipsum quia dolor sit amet, 
> consectetur, adipisci velit...

Inline code characters
Use the backtick to refer to a `function()`.
 
There is a literal ``backtick (`)`` here.

Code blocks
Indent every line of the block by at least 4 spaces.

This is a normal paragraph:

    This is a code block.
    With multiple lines.

Alternatively, you can use 3 backtick quote marks before and after the block, like this:

```
This is a code block
```

To add syntax highlighting to a code block, add the name of the language immediately
after the backticks: 

```javascript
var oldUnload = window.onbeforeunload;
window.onbeforeunload = function() {
    saveCoverage();
    if (oldUnload) {
        return oldUnload.apply(this, arguments);
    }
};
```


Links to external websites
This is [an example](http://www.example.com/) inline link.

[This link](http://example.com/ "Title") has a title attribute.

Links are also auto-detected in text: http://example.com/



Images
Inline image syntax looks like this:

![Alt text](/images/avatar.jpg)

![Alt text](/path/to/image.png "Optional title attribute")

![Alt text](/url/to/image.jpg)

![Mockup for feature A](/images/avatar.jpg)


Tables

| Day     | Meal    | Price |
| --------|---------|-------|
| Monday  | pasta   | $6    |
| Tuesday | chicken | $8    |

Table 2

First Header | Second Header
------------ | -------------
Content from cell 1 | Content from cell 2
Content in the first column | Content in the second column


Emphasis

*This text will be italic*
_This will also be italic_

**This text will be bold**
__This will also be bold__

_You **can** combine them_

http://github.com - automatic!
[GitHub](http://github.com)


Inline code
I think you should use an
`<addr>` element here instead.


- [x] @mentions, #refs, [links](), **formatting**, and <del>tags</del> supported
- [x] list syntax required (any unordered or ordered list supported)
- [x] this is a complete item
- [ ] this is an incomplete item

:EMOJICODE:.

@octocat :+1: This PR looks great - it's ready to merge! :shipit:

gist

<script type="application/javascript" src="https://gist.github.com/spf13/7896402.js"></script>

{{< gist spf13 7896402 >}}


{{< highlight html >}}
<section id="main">
  <div>
   <h1 id="title">{{ .Title }}</h1>
    {{ range .Pages }}
        {{ .Render "summary"}}
    {{ end }}
  </div>
</section>
{{< /highlight >}}

{{< relref "#anchors" >}} => #anchors:9decaf7


***this is self content***
