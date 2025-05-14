---
title: p5.jsで連番{png,jpeg,webp}を吐けるライブラリを書いた
published: true
emoji: 🎥
type: tech
topics:
  - p5.js
date: 2025-01-28
aliases:
---
# p5.jsで連番{png,jpeg,webp}を吐けるライブラリを書いた

タイトル通りです。
https://github.com/sevenc-nanashi/p5-frame-capturer

## なぜ？
- [p5.capture](https://github.com/tapioca24/p5.capture)でできなかったことをやりたかった
	- ディレクトリへの保存
		- File System APIでディレクトリに保存したかった
	- instance modeのサポート
		- 普段Viteを使ってp5を書いているので、instance mode対応が欲しかった

# デモ

AviUtlで連番結合とmuxをしています。
https://youtu.be/UqxoCw3caLs?si=89o-wwVXLdw4BMPZ

# おわりに

バグとかがあればIssueに投げてくれると嬉しいです。