---
description: Strip!
---

# 🥅 Regex

## Regular Expressions

Using regex is a great way to save time when grepping complex strings (among a million other things) here are some regex tricks. Use regex with `grep` by using the `-E` flag.

Two ways to strip links from text:

```regex
(http|ftp|https):\/\/([\w_-]+(?:(?:\.[\w_-]+)+))([\w.,@?^=%&:\/~+#-]*[\w@?^=%&\/~+#-])
```

```regex
(http|https)://[a-zA-Z0-9./?=_%:-]*
```

To collect all the way up to a specific character, use the following character class: `/^[^;]*/`
