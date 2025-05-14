---
title: HomebridgeとNature Remoで強引にスマートホーム家電にする
published: false
emoji: 🏠
type: tech
topics:
  - homebridge
date: 2025-04-29
aliases:
---
# はじめに
この記事は家についていた赤外線シーリングライトをHomebridgeとNature RemoでApple Homeのエコシステムに載せた（スマートホーム家電化した）記事です。

# 完成品
リポジトリはここです：
https://github.com/sevenc-nanashi/homebridge-doshisha-b506

（Twitterのリンクを貼る）

# 仕組み
まず、このライトにはまともなAPIはついていません。赤外線リモコンによる操作のみです。
以下は操作の一部です：
- 消灯/普段灯：消灯状態なら普段灯をオンにする。それ以外なら消灯状態に移動する。
- 明るく：明るさを1上げる。最大10。
- 

そしてApple Homeのエコシステムに載せるには以下の状態が