---
layout: post
title:  "Markdown Syntax"
category: general
tags: syntax markdown
lang: en

---

## Head

# HEADING1(#)
## HEADING2(##)
### HEADING3(###)
#### HEADING4(####)
##### HEADING5(#####)

---
## Inline hightlight

Inline \`code\` has \`back-ticks around\` it.

Inline `code` has `back-ticks around` it.

\> you can \*put\* \*\*Markdown\*\* into a blockquote.

> you can *put* **Markdown** into a blockquote.

---
## Multiple lines hightlight

- \`\`\`javascript

```javascript
var s = "JavaScript syntax highlighting";
alert(s);
```
- \`\`\`python

```python
s = "Python syntax highlighting"
print s
```

- \{ % hightlight ruby %\}

\{ % endhightlight %\} {% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

```
No language indicated, so no syntax highlighting.
But let's throw in a <b>tag</b>.
```

---
## Horizontal line
Three or more...

\-\-\-

---

Hyphens

\*\*\*

***

Asterisks

\_\_\_

___

## Link \[text\]\[url\]

\[blog.cheeger.com\]\[url\]

[blog.cheeger.com][url]

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
[url]: http://blog.cheeger.com/
