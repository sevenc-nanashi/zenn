---
title: Xtoolを使ってWSLでiOS開発をする
published: false
emoji: 🍏
type: tech
topics:
  - xtool ios
date: 2025-05-11
aliases:
---
# Xtoolを使ってWSLでiOS開発をする

:::message alert
この記事は2025/05/14時点での情報です。
バージョン：v1.10.1
:::

## はじめに
Xtoolというものを使えばLinux環境でもiOSの開発ができるというのを聞いたのでやってみました。

## 参考リンク
- [Swift Forum「Xtool: cross-platform Xcode replacement. Build iOS apps on Linux and more!」](https://forums.swift.org/t/xtool-cross-platform-xcode-replacement-build-ios-apps-on-linux-and-more/79803)
- [GitHub「xtool-org/xtool」](https://github.com/xtool-org/xtool)
- [公式サイト](https://xtool.sh)

## 注意
[フォーラムにて規約違反の可能性が指摘されています](https://forums.swift.org/t/xtool-cross-platform-xcode-replacement-build-ios-apps-on-linux-and-more/79803/2)。自己責任でお願いします。

## 1：環境構築 / 要件
以下が必要です：
- WSL2
- [Swift](https://www.swift.org/install/linux/)
- Apple ID

以下の組み合わせのうち、いずれかが必要です：
- [usbipd](https://learn.microsoft.com/en-us/windows/wsl/connect-usb)、[usbmuxd](https://github.com/libimobiledevice/usbmuxd)
- [AltStore Classic](https://faq.altstore.io/altstore-classic/your-altstore)
- その他のサイドロード環境（Sideloadly、TrollStoreなど）

また、この記事内では以下の記法を用います：
```
$ -> WSL：一般ユーザーで実行
> -> Windows：一般ユーザーで実行
! -> Windows：Administratorで実行

<読み替え>（例：cat /home/<ユーザー名>/.ssh/id_ed25519）
```
## 2：Xtoolのセットアップ
1. Xcode.xipをダウンロードします。

以下のURLからダウンロードできます。**curl等でのダウンロードはできません**（一敗）。
<https://developer.apple.com/services-account/download?path=/Developer_Tools/Xcode_16.3/Xcode_16.3.xip>
この時、ダウンロード先のパスを覚えておく必要があります。これは１回しか使われないので、`/tmp`でも大丈夫のはずです（要検証）。
また、念のため`file`コマンドでちゃんとダウンロードできているか確認することを推奨します。（現時点だとXtoolに無効なファイルを指定するとハングするバグがあるため）
```
$ file ~/xtool/xcode.xip
/home/sevenc7c/xtool/xcode.xip: xar archive compressed TOC: 3942, SHA-1 checksum
```

2. Xtoolをインストールします。
```shell
$ curl -fL \
    "https://github.com/xtool-org/xtool/releases/latest/download/xtool-$(uname -m).AppImage" \
    -o ./xtool
$ chmod +x ./xtool
$ cd ./xtool ~/.local/bin/xtool  # PATHが通っていればどこでも可
```

3. Xtoolでログインします。

```shell
$ xtool setup
```

この時、APIキーを使ってログインするかパスワードを使ってログインするかを選ぶことができます：
```shell
Select login mode
0: API Key (requires paid Apple Developer Program membership)
1: Password (works with any Apple ID but uses private APIs)
Choice (0-1):
```
APIキーは[有料のApple Developer Program](https://developer.apple.com/programs/enroll/)に加入する必要があります。もし加入している場合はこちらを使うことが[推奨されています](https://swiftpackageindex.com/xtool-org/xtool/1.10.1/documentation/xtool/installation-linux#:~:text=this%20is%20the%20recommended%20option.)。
加入していない場合はパスワードを選択する必要があります。

:::message
パスワード式のログインは内部APIを使うルートになります。[念のため捨て垢を作っておくことが推奨されています](https://swiftpackageindex.com/xtool-org/xtool/1.10.1/documentation/xtool/installation-linux#:~:text=%20you%20may%20want%20to%20create%20a%20throwaway%20apple%20id%20to%20be%20extra%20cautious.)。
:::

4. Xcode.xipのパスを指定します。

```
Choice (0-1): 10
...
Path to Xcode.xip: <1.でダウンロードした先のパス>
```

その後、解凍されるまで放置すればセットアップは完了です。

## 3：アプリの作成

1. プロジェクトを作成します。
```shell
xtool new Hello
```
Hello下にアプリが作成されているはずです。
```shell
$ cd Hello
```

2. `xtool.yml`を編集します。
```diff yml
 version: 1
-bundleID: com.example.hello
+bundleID: com.sevenc7c.hello
```
## 4：アプリのインストール
### usbipd + usbmuxdを使った場合
1. USBでiPhone/iPad端末をPCに繋ぎます。
2. 管理者権限でコマンドプロンプトを開きます。
3. busidを取得します。
```shell
> usbipd list
Connected:
BUSID  VID:PID    DEVICE                                                        STATE
2-2    0b05:19af  AURA LED Controller, USB 入力デバイス                         Not shared
2-3    046d:c077  USB 入力デバイス                                              Not shared
2-11   1132:4511  USB 大容量記憶装置                                            Not shared
2-14   8087:0026  インテル(R) ワイヤレス Bluetooth(R)                           Not shared
6-3    046d:c31d  USB 入力デバイス                                              Not shared
6-4    05ac:12a8  Apple Mobile Device USB Composite Device                      Not shared

Persisted:
GUID                                  DEVICE
```
Apple Mobile Device USB Composite DeviceのBUSIDを覚えておきます。

4. WSL側にiPhone/iPadの管理を移譲します。
```shell
! usbipd bind --busid <busid>
! usbipd attach --wsl --busid <busid>
```

このとき、`usbipd attach`がDevice busyで動かないときがあります：
```
WSL usbip: error: Attach Request for 6-4 failed - Device busy (exported)
```
その場合は以下のコマンドを連打しながら（上->Enterを繰り返す）USBをつなぎ直すとほとんどの場合でうまくいきます。
```
! usbipd bind --busid <busid> --force && usbipd attach --wsl --busid <busid>
```

もし正常にできていた場合は以下のコマンドでiPhone/iPadが出現するはずです：
```shell
$ lsusb
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 006: ID 05ac:12a8 Apple, Inc. iPhone 5/5C/5S/6/SE/7/8/X/XR
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
```

5. `xtool dev`を実行します。
```
$ xtool dev
```

が、自分の環境だと動きませんでした...（[関連Issue](https://github.com/xtool-org/xtool/issues/19)）

### サイドロード環境の場合

1. ipaをビルドします。
v1.10.1の時点では以下のようにします：
```bash
#!/usr/bin/env bash
set -eux

APP_NAME=<アプリ名>

xtool dev build
mkdir -p xtool/ipa_temp/Payload
cp -r xtool/$APP_NAME.app xtool/ipa_temp/Payload
(cd xtool/ipa_temp && zip -r ../$APP_NAME.ipa Payload)

# 送信部分は必要に応じて置き換えてください。
# （例：自分はZドライブをRamDiskにしているので/mnt/zにコピーしている）
cp ./xtool/$APP_NAME.ipa /mnt/z
```

また、[`xtool dev build --ipa`が次のバージョンで実装される予定です](https://github.com/xtool-org/xtool/pull/24)。

2. ipaを端末にインストールします。
これは使っているサイドロードツールのドキュメントを参照してください。

![](https://storage.googleapis.com/zenn-user-upload/fa18ec732fa9-20250511.png)

うまく行けばアプリがサイドロードできているはずです。

## 終わりに
まだビルドしただけですが、ビルドできたということに大きな意義があると思います。
Xcodeでのプレビューなどが使えない以上、おそらく現実的なのは[Capacitor](https://capacitorjs.jp)を使うことになると思います。（まだXtoolで動かせていませんが...）

ありがとうございました。