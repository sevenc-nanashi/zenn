---
title: "shields.io × Cloudflare workersで自分だけのバッジを作る"
emoji: "☁️"
type: "tech"
topics:
  - "cloudflareworkers"
  - "shieldsio"
published: true
---

突然ですが、こんなものを作りました。

![バッジ](https://img.shields.io/endpoint?url=https%3A%2F%2Fchunirec.sevenc7c.workers.dev)

多分世界で自分しかやってないであろう、**Chunithm^[正確にはChunirec]のレート表示**バッジです！！！！です！！！！
ちゃんと更新されます。執筆現在では15.56ですが、多分そのうち変わってます。
というわけで、Cloudflare workersでshields.ioのカスタムバッジを作ってみました。

# そもそもカスタムバッジって？

shields.ioにはカスタムバッジが2種類あります。JSON/XML/YAMLからパスを指定して取得するものとスキーマに沿ったJSONを返すエンドポイントから取得するものです。

前者を使えば、たとえばActivityPubのURLからフォロワーを取得してフォロワーバッジを作れます。[![Misskey.systems](https://img.shields.io/badge/dynamic/json?color=8ab942&label=%F0%9D%97%A0%F0%9D%97%B6%20@sevenc_nanashi@misskey.systems&query=%24.totalItems&url=https%3A%2F%2Fmisskey.systems%2Fusers%2F9c8fgzgryd%2Ffollowers)](https://misskey.systems/@sevenc_nanashi)
今回は後者の方を使います。

# JSONエンドポイントバッジとは？
 
`https://img.shields.io/endpoint?url=...&style=...`のように指定するバッジです。
公式によると^[https://shields.io/endpoint より]、リダイレクトするのに比べて以下の点が優れているそうです（要約）、
1. 内容を返す機構と表示する機構が分離している。APIを作る側は、バッジの内容を考えるだけでよく、スタイルとかそこら辺は気にしなくて良くなる。
2. 常に最新のバッジの表示を使える。バッジのテンプレやnpmパッケージなどに追従する必要がない。
3. JSONを返す機構はリダイレクトより作りやすい。ほとんどのフレームワークがサポートしている。
4. ShieldsのCDNに乗っかれる。HTTPのキャッシュ周りを学ぶ必要はなく、キャッシュ周りの挙動はJSONにプロパティを追加するだけ。

# なにを返すべき？

```ts
type Response = {
  // バッジのスキーマバージョン。常に「1」。
  schemaVersion: number;
  // 左側のテキスト。省略する場合は空文字列。
  // URLクエリで上書き可能。
  label?: string;
  // 右側のテキスト。必ず指定する必要がある。
  message: string;
  // 右側の色。shields.ioトップの下の方にある、Colorsのように指定可能。
  // URLクエリで上書き可能。
  color?: string;
  // 左側の色。URLクエリで上書き可能。
  labelColor?: string;
  // エラーバッジとして扱う場合はtrue。この場合、ユーザーが色を上書きすることはできない。
  // キャッシュ動作にも影響する可能性がある。
  isError?: boolean;
  // ロゴの名前。Shieldsまたはsimple-iconsでサポートされているロゴの名前を書く必要がある。
  // URLクエリで上書き可能。
  namedLogo?: string;
  // カスタムロゴを表すSVG文字列。
  // URLクエリで上書き可能。
  logoSvg?: string;
  // ロゴの色。namedLogoとShieldsロゴにのみ適用される。
  // URLクエリで上書き可能。
  logoColor?: string;
  // ロゴの幅。URLクエリで上書き可能。
  logoWidth?: string;
  // ロゴの位置。URLクエリで上書き可能。
  logoPosition?: string;
  // 表示スタイル。デフォルトは「flat」。
  // URLクエリで上書き可能。
  style?: string;
  // HTTPキャッシュの寿命（秒単位）。ShieldsのCDNとブラウザに使用される。
  // 300未満の値は無視される。
  // 指定した値はURLクエリで上書き可能だが、より長い値のみ許可される。
  // 性能とトラフィック対応のチューニングができる。
  cacheSeconds?: number;
}

```

# Cloudflare workersでAPIを作成する

1. npmからwranglerをインストールします。
```
$ npm i -g wrangler
```

2. wranglerにCloudflareを紐付けます。
```
$ wrangler login
```

3. プロジェクトを作成します。
```
$ wrangler init my-app
$ cd my-app
```

4. Workersをデバッグします。

```
$ wrangler dev
```

Chunirecバッジのサンプルを置いておきます。
```ts
import svg from "./chuni.svg.txt"; // .txtや.htmlじゃないとインポートできないので注意
interface Player {
  user_id: number;
  player_name: string;
  title: string;
  title_rarity: string;
  level: number;
  rating: number;
  rating_max: number;
  classemblem: string | null;
  classemblem_base: string | null;
  is_joined_team: boolean;
  updated_at: string;
}

export default {
  async fetch(
    request: Request,
    env: {
      CHUNIREC_API_KEY: string;
    }
  ) {
    const url = new URL("https://api.chunirec.net/2.0/records/profile.json");
    url.searchParams.set("token", env.CHUNIREC_API_KEY);
    url.searchParams.set("region", "jp2");
    const player = (await fetch(url.toString(), {
      // キャッシュさせる
      cf: {
        cacheEverything: true,
        cacheTtl: 60 * 5,
      },
    }).then((res) => res.json())) as Player;

    return new Response(
      JSON.stringify({
        schemaVersion: 1,
        label: player.player_name
          .replace(/[Ａ-Ｚａ-ｚ０-９]/g, (s) => {
            return String.fromCharCode(s.charCodeAt(0) - 0xfee0);
          })
          .replace("．", "."),
        message: player.rating,
        color: "ffe795",
        logoSvg: svg,
      })
    );
  },
};
```

5. デプロイします。

```
$ wrangler publish
```

ここでWorkerのURLが表示されるはずです。

# バッジを試す

[JSONエンドポイントバッジのドキュメント](https://shields.io/endpoint)の下にテストするフォームがあります。そこのURLにWorkerのURLを貼ると、実際にバッジが動いているのが確認できるはずです。

# 最後に

思ったよりWorkersは簡単でした。そのうち他のバッジも作ってみたい。
ありがとうございました。
