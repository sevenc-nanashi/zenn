---
published: true
title: 色変記事：実質2.5ヶ月でRubyとRustを使って入緑した話
emoji: 🟩
type: tech
topics:
  - atcoder
  - ruby
  - rust
date: 2024-09-14
aliases:
---
くぅ～疲れましたw これにて入緑です！
![](https://storage.googleapis.com/zenn-user-upload/461986507b5c-20240928.jpeg)
ちゃんとAtCoderを始めてから2.5ヶ月。ABC373で水パフォ（1297）を出して入緑しました！嬉しい！

と言うわけで何か書いてみようと思います。
# スペック
- 17歳（高3）
- プログラミングは9~10年ほど
- 普段はVoicevoxのエディタを開発している
- AtCoderはアルゴリズム何も知らない状態でやったのが15回ほど（グラフの左半分）
	- 灰パフォなのでノーカンということで…
# 言語
基本的にはRuby、計算量が重そうな場合はRustを使っています。最近はCrystalも気になっています。
Rubyは競プロでも使える便利メソッドが色々とあって便利です：
- [`Enumerable#each_slice`](https://docs.ruby-lang.org/ja/latest/method/Enumerable/i/each_slice.html)（`[1, 2, 3, 4].each_slice(2)` -> `[[1, 2], [3, 4]]`）
- [`Enumerable#each_cons`](https://docs.ruby-lang.org/ja/latest/method/Enumerable/i/each_cons.html)（`[1, 2, 3, 4].each_cons(2)` -> `[[1, 2], [2, 3], [3, 4]]`）
- [`Enumerable#chunk`](`https://docs.ruby-lang.org/ja/latest/method/Enumerable/i/chunk.html)（`[1, 1, 2, 3, 2].chunk { _1 }` -> `[[1, [1, 1]], [2, [2]], [3, [3]], [2, [2]]]`）
- [`Range#bsearch`](https://docs.ruby-lang.org/ja/latest/method/Range/i/bsearch.html)（二分探索）

もちろんACLもあります：[universato/ac-library-rb](https://github.com/universato/ac-library-rb)

例えば[ABC369: B - Piano 3](https://atcoder.jp/contests/abc369/tasks/abc369_b)はこれで解けます：
```rb
N = gets.chomp.to_i
AS =
  N.times.map do
    input = gets.chomp.split
    [input[0].to_i, input[1]]
  end

l = AS.filter { |a, s| s == "L" }.map(&:first)
r = AS.filter { |a, s| s == "R" }.map(&:first)

puts (
       l.each_cons(2).sum { |a, b| (b - a).abs } +
         r.each_cons(2).sum { |a, b| (b - a).abs }
     )
```
# やったこと
## 環境を整える
コーディング環境は大事です。
![](https://storage.googleapis.com/zenn-user-upload/79fab7ce580c-20240928.png)
*自分のNeovim。かわいいね*
自分は以下のツールを使っています：
- [oj](https://github.com/online-judge-tools/oj)：提出、コードテスト
- [oj-prepare](https://github.com/online-judge-tools/template-generator)：一括ダウンロード
- [atcoder-judge-monitor](https://github.com/sevenc-nanashi/atcoder-judge-monitor)：提出結果チェック（自作）

これらにより、エディタ内でダウンロード->提出->確認が完結するのでとても便利になりました。
ブラウザは問題を見るためだけになっています。
また、制約に色をつける[atcoder-limit-colorizer](https://github.com/sevenc-nanashi/atcoder-limit-colorizer)を自作しました。だいたい[ABC372 - E](https://atcoder.jp/contests/abc372/tasks/abc372_e)のせい。
![](https://storage.googleapis.com/zenn-user-upload/37e3282a19fd-20240928.jpeg)
*$k \leq 10$に気が付かず解かなかった人は自分以外にもいると思いたい*
## 解説を読む
解けなかった問題、特にD問題は解説を読むようにしています。
[evimaさん](https://youtube.com/@evimalab?si=0x4JYriiG2m4l929)の解説はわかりやすいのでおすすめです。（いつもありがとうございました！）
## [AtCoder Daily Training](https://atcoder.jp/contests/adt_top)
火曜日、水曜日、木曜日に開催されるバーチャルコンテストです。
最近はHard（CCDEF相当）をやっています。DPとかの練習になります。
## [Educational DP contest](https://atcoder.jp/contests/dp)
DP練習問題です。AからEまで終わらせました。
# アルゴリズム
ここら辺はよく使います：
- 累積和
- DP
- UnionFind
- 総当たり
- 幅優先探索/深さ優先探索
- 二分探索
- SortedSet（RubyだとRBTree）のlower_boundとupper_bound
- ダイクストラ

もっともPriorityQueueやRBTreeやUnionFindなどの実装はライブラリ頼りですが...
# 最後に
Rubyはいいぞ！
実は、[ぼすきー](https://voskey.icalo.net)のプログラミングチャンネルで競プロの話題が出たのが始まりでした。
そのうち入水もしてみたいです（まぁ2年くらいかかりそうですが…）。

ありがとうございました。