---
title: "Ubuntu24.04のユーザ名命名規則"
last_modified_at: 2024-09-20T00:00:00+0900
tags:
  - Linux
---
判読性とか認識性を向上する為に、各所の命名時に長く詳細に書きたいのはあるある


例: `component-subcomponet-env` 


長いのは良いが、命名規則に違反しないように注意

- `--badname`を付与すれば作成できる様子もあるが、何処で悪さするか分からないので非推奨
- `/etc/adduser.conf` を参照のこと。
  - システムユーザと標準ユーザで命名規則が異なる。
  - 以下に加え、33文字以上のユーザを作成しようとすると`badname`判定されるので注意。

```
# Non-system user- and groupnames are checked against this regular
# expression.
# Default: NAME_REGEX="^[a-z][-a-z0-9_]*\$?$"
#NAME_REGEX="^[a-z][-a-z0-9_]*\$?$"

# System user- and groupnames are checked against this regular
# expression.
# Default: SYS_NAME_REGEX="^[A-Za-z_][-A-Za-z0-9_]*\$?$"
#SYS_NAME_REGEX="^[A-Za-z_][-A-Za-z0-9_]*\$?$"
```

- 33文字ユーザ: NG

```
$ echo -ne abcdefghijklmnopqrstuvwxyz1234567 | wc -c
33

$ sudo useradd abcdefghijklmnopqrstuvwxyz1234567
useradd: invalid user name 'abcdefghijklmnopqrstuvwxyz1234567': use --badname to ignore
```

- 32文字ユーザ: OK
```
$ echo -ne abcdefghijklmnopqrstuvwxyz123456 | wc -c
32

$ sudo useradd abcdefghijklmnopqrstuvwxyz123456
$ cat /etc/passwd | grep abcdefghijklmnopqrstuvwxyz123456
abcdefghijklmnopqrstuvwxyz123456:x:1005:1005::/home/abcdefghijklmnopqrstuvwxyz123456:/bin/sh
```