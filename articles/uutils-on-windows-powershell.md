---
title: uutilsでPowerShellを快適にする
published: true
emoji: 🦀
type: tech
topics:
  - powershell
  - windows
date: 2025-07-19
aliases:
---
# uutilsでPowerShellを快適にする
Windowsでの開発、シェルは何を使っていますか？
VSCodeなどのエディタのターミナルはデフォルトではPowerShellになっているはずです。[^年単位でVSCode使ってないのでうろ覚え...]
しかし、普段からWSLで開発しているとPowerShellのコマンドがcoreutilsと違って苦労することが結構あります。

というわけで、[uutils/coreutils](https://github.com/uutils/coreutils)でcoreutilsをPowerShellで使えるようにします。

## やり方
```powershell
$coreutils = @("rm", "cp", "mv")
foreach ($cmd in $coreutils) {
  if (Test-Path "alias:$cmd") {
    Remove-Item "alias:$cmd" -Force
  }

  $functionName = $cmd
  $functionBody = {
    coreutils $MyInvocation.MyCommand.Name @args
  }.GetNewClosure()

  Set-Item "function:$functionName" $functionBody
}
```

とりあえず使いそうなものを`$coreutils`に入れています。`ls`や`ll`は[eza](https://github.com/eza-community/eza)に向けたり、`cat`は[bat](https://github.com/sharkdp/bat)に向けたりする場合も置いておきます：
```powershell
# 単純な置換で済む場合はこれでOK
Set-Alias ls eza
Set-Alias cat bat
function global:ll {
  eza -al @args
}

```

## おわりに

uutilsはいいぞ。ありがとうございました。