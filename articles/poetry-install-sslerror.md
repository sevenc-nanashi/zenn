---
title: "Windowsでのpoetry installがSSLErrorで落ちる人へ向けて"
emoji: "🛡️"
type: "tech"
topics:
  - "python"
  - "poetry"
published: true
---

# 始めに
```
  MaxRetryError

  HTTPSConnectionPool(host='github.com', port=443): Max retries exceeded with url: /user/repo/info/refs?service=git-upload-pack (Caused by SSLError(FileNotFoundError(2, 'No such file or directory')))

  at .venv\Lib\site-packages\urllib3\util\retry.py:592 in increment
      588│             history=history,
      589│         )
      590│
      591│         if new_retry.is_exhausted():
    → 592│             raise MaxRetryError(_pool, url, error or ResponseError(cause))
      593│
      594│         log.debug("Incremented Retry for (url='%s'): %r", url, new_retry)
      595│
      596│         return new_retry
```

で毎回落ちてたのに解決法の記事が全くがなかったので書きました。

# 結論

`C:\ProgramData\Git\config` の

```
[http]
        sslCAinfo = /bin/curl-ca-bundle.crt
```
を消す/コメントアウトしましょう。それで直りました。

# 最後に

内容うっす
困ってる人に届けば幸いです。ありがとうございました。
