---
title: Voicevoxの合成音声部分だけを抜いて遊ぶ
published: false
emoji: 💬
type: tech
topics:
  - Voicevox
  - VoicevoxCore
date: 2023-11-03
aliases:
---
# はじめに
[Voicevox](https://voicevox.hiroshiba.jp)という音声合成ソフトがあります。
![](https://storage.googleapis.com/zenn-user-upload/c50a9b417003-20231103.png)
HTTP APIがあるのは有名ですが、実はC APIもあったりします（もちろん色々な言語のFFI的なライブラリで使えます）。
この記事ではそれで色々遊んでいきたいと思います。

# 