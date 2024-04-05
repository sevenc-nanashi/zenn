---
title: Neovim：一定時間操作が無かったら何かやる設定の作り方
published: false
emoji: 💤
type: tech
topics:
  - Neovim
  - Lua
date: 2024-04-12
aliases:
---
> この記事は[Vim駅伝](https://vim-jp.org/ekiden/) 2024/04/12の記事です。
## レシピ

```lua
local debounce_timer = nil
local is_active = true

vim.on_key(function()
  if debounce_timer then
    debounce_timer:stop()
  end
  if not is_active then
    is_active = true
    -- 復帰したときの処理
  end
  debounce_timer = vim.defer_fn(function()
    if not is_active then
      return
    end
    is_active = false
    -- 放置されたときの処理
  end, 30 * 60 * 1000)
end)
```
コメントの部分は好きなように置き換えて下さい。
## なぜ？

A. coc.nvimを落としたかったから。
よくエディタを開きっぱなしにしたまま放置するので、使っていないLS等がリソースを食っている状況でした。
自分の性格上エディタを使い終わったら閉じることは出来ないので、ならdotfilesに書いちゃおうと言う理由です。

## 実例

```lua
local debounce_timer = nil
local is_coc_active = true

vim.on_key(function()
  if debounce_timer then
    debounce_timer:stop()
  end
  if not is_coc_active then
    is_coc_active = true
    vim.cmd('call coc#rpc#start_server()')
    vim.notify('Coc started', vim.log.levels.INFO, { title = 'Coc Killer' })
  end
  debounce_timer = vim.defer_fn(function()
    if not is_coc_active then
      return
    end
    is_coc_active = false
    vim.cmd('call coc#rpc#stop()')
    vim.notify('Coc was killed', vim.log.levels.INFO, { title = 'Coc Killer' })
  end, 30 * 60 * 1000)
end)
```

Neovim組み込みのLSクライアントでも似たような事は出来ると思います。

## 最後に
多分もっと良い書き方があると思います。その時はコメントに書いてくれると助かります。
ありがとうございました。