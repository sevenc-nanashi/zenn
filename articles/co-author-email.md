---
title: "メアドが分からないけどCo-Authored-Byに入れたい時の解決法"
emoji: "🤝"
type: "tech"
topics:
  - "GitHub"
published: true
---

# 始めに

例えばDiscordでコードの相談に乗って貰ったり、PRをクローズしたけどそのPRの作者に感謝（？）を伝えたかったりして、Co-Authorに入れたいのに、メアドが分からなくてCo-Authored-Byに入れられないってなったときの解決法です。

# 結論

`[ユーザー名]@users.noreply.github.com` （`sevenc-nanashi@users.noreply.github.com`）、または `[ユーザーID]+[ユーザー名]@users.noreply.github.com` （`59691627+sevenc-nanashi@users.noreply.github.com`）が使えます。後者の方はユーザー名を変更しても引き継がれますが、面倒なら前者でも大丈夫だと思います。

```
git commit -m "いろいろ" -m "Co-Authored-By: sevenc-nanashi <sevenc-nanashi@users.noreply.github.com>"
```

# おまけ

`.bashrc`などにこれを追加すると便利です：

```bash
function cab() {
  echo "Co-Authored-By: $1 <$1@users.noreply.github.com>"
}

# $ git commit -am "feat: ほげほげ" -m "$(cab sevenc-nanashi)"
# のように使用
```

# 最後に

内容うすすぎ
困ってる人に届けば幸いです。ありがとうございました。
