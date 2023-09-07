---
description: The idea is to check this page less and less
---

# 🐧 Linux

<details>

<summary>grep</summary>

```
cat /etc/passwd | grep "/bin/bash" --include 
cat /etc/passwd | grep -v "false\|nologin" --exclude 
```

</details>

<details>

<summary>wc</summary>

```
wc:
-l lines
-m characters
-w words
```

</details>

<details>
<summary>cURL</summary>

```
-O download webpage
-s suppress output
-k skip cert check (to navigate around invalid SSL)
-v(vv) Verbosity, 3 Levels
-I HEAD request, only headers
-i Headers and Response
-H custom header
-A custom user-agent
```

</details>

| Code | Permissions     |
| ---- | --------------- |
| 0    | No Permission   |
| 1    | Execute         |
| 2    | Write           |
| 3    | Execute & Write |
| 4    | Read            |
| 5    | Read & Execute  |
| 6    | Read & Write    |
| 7    | rwx             |





