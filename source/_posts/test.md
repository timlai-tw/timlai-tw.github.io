---
title: 1
date: 2024-09-01 10:23:43
tags:
home_cover: /images/banner/azure-devops-build-management.jpg
post_cover: /images/banner/azure-devops-build-management.jpg
---



The background color is `#ffffff` for light mode and `#000000` for dark mode.



>  測試
>

<font color=#FF0000>**圖片太小可以在圖上按右鍵，由新分頁檢視圖片。**</font>

> [!IMPORTANT]
> 這是一個重要的訊息。

> [!NOTE]
> 這是一個普通的提示。

> [!TIP]
> 這是一個小技巧。

> [!WARNING]
> 這是一個警告訊息。


::: info
This is an info box.
:::

::: tips
This is a tip.
:::

::: warning
This is a warning.
:::

::: danger
This is a dangerous warning.
:::

::: success
This is an info box.
:::


{% note primary %}
this is primary note ...
{% endnote %}


{% blockquote %}
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque hendrerit lacus ut purus iaculis feugiat. Sed nec tempor elit, quis aliquam neque. Curabitur sed diam eget dolor fermentum semper at eu lorem.
{% endblockquote %}

{% blockquote @DevDocs https://twitter.com/devdocs/status/356095192085962752 %}
NEW: DevDocs now comes with syntax highlighting. http://devdocs.io
{% endblockquote %}

==**測試**==
==測試==
 

- []
- [x]

H~2~O
X^2^

:)

*[HTML]: Hyper Text Markup Language
*[W3C]:  World Wide Web Consortium
The HTML specification
is maintained by the W3C.

++下底線++

[ ] Mercury
[x] Venus

### Solar System Exploration, 1950s – 1960s

- [ ] Mercury
- [x] Venus
- [x] Earth (Orbit/Moon)
- [x] Mars
- [ ] Jupiter
- [ ] Saturn
- [ ] Uranus
- [ ] Neptune
- [ ] Comet Haley

+++ 折疊內容
abcd1234
+++

<div id="divTips" class="tips">
<font color="black">tips</font>
</div>

<div id="divSuccess" class="success">
<font color="black">success</font>
</div>

<div id="divWarning" class="warning">
<font color="black">warning</font>
</div>

<div id="divDanger" class="danger">
<font color="black">danger</font>
</div>

**This is bold text**	
_This text is italicized_	
~~This was mistaken text~~	
**This text is _extremely_ important**	
***All this text is important***	
This is a <sub>subscript</sub> text	
This is a <sup>superscript</sup> text	

Text that is not a quote

> Text that is a quote

Use `git status` to list all new or modified files that haven't yet been committed.

This site was built using [GitHub Pages](https://pages.github.com/).




<font size='64px'>64</font>

Here is a simple footnote[^1].

A footnote can also have multiple lines[^2].

[^1]: My reference.
[^2]: To add line breaks within a footnote, prefix new lines with 2 spaces.
  This is a second line.



```mermaid
flowchart
  subgraph Assembly
    subgraph AssemblyInitialize
    end
    subgraph Class
    end    
    subgraph AssemblyCleanup
    end
  end
```

<div style="max-width: 400px; text-align:center;">
```mermaid
---
title: Hello Title
config:
  theme: base
  themeVariables:
    primaryColor: "#00ff00"
---
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```
</div>

```mermaid
---
title: Hello Title
config:
  theme: base
  themeVariables:
    primaryColor: "#00ff00"
---
flowchart
	Hello --> World
```

```mermaid
flowchart LR
  subgraph Assembly
    subgraph Class
        subgraph Test
            Test1 --> Test2 --> TestN
        end
    end    
  end
```
