# もちカワウソ育成アプリ（公開用）

耕大さんと虹さんのカワウソ育成アプリ。`index.html`（画像・GIFもbase64で埋め込み済み・外部CDNなし）と、
ふたり共通の育成データ `state.json` の2ファイルで動く。

- 公開URL: https://koudaiu1006-jpg.github.io/otter-app/
- 共通データ: https://koudaiu1006-jpg.github.io/otter-app/state.json
- リポジトリ: https://github.com/koudaiu1006-jpg/otter-app

## いまの状態（2026-11-14 14:40時点）

**公開済み。** GitHub Pages は main / (root) でビルドされ `status: built`、
`https://koudaiu1006-jpg.github.io/otter-app/` と `/state.json` はどちらも HTTP 200。
もちカワウソは Lv.3・すくすく期（XP 20 / 150）。

## できたこと

| 機能 | 中身 |
| --- | --- |
| うごく もちカワウソ | 透過GIF（360px・14コマ・約232KB）。ぷるぷる上下＋伸び縮み＋まばたき＋ときどき「ぴゃっ」 |
| せいちょう | あかちゃん期（Lv.1–2）→ すくすく期（Lv.3–4・にじいろの貝がらのポシェット）→ おとな期（Lv.5〜・にじいろのネックレス）。「せいちょう」タブでビフォー→アフターとしんかの記録を並べて見られる |
| たからもの | 2人が集めた6つの持ち物を「たからものだな」と水面の飾りに表示。タップすると「虹さんが いわばで みつけた にじいろの たからもの！」など思い出がひとこと出る |
| なでなで | ぷにっと跳ねる（1回）／ぷるんと大ジャンプ（6回連続で「だいすき！ぎゅーっ♡」）、ハート・波紋・バイブ |
| おさかなキャッチ | そのまま（30秒・最高記録は localStorage） |
| 共通データ | 起動時・「🔄 最新を読み込む」・画面に戻ったとき（60秒以上）に `./state.json?t=...` を fetch |

アプリは起動時と「🔄 最新を読み込む」ボタン、画面に戻ったとき（60秒以上経過時）に
`./state.json?t=...` を fetch して、Lv・XP・4つのゲージ・お世話回数・たからもの・きろく・成長アルバムを描き直す。
fetch できないとき（`file://` で開いた・オフライン）は HTML に埋め込んだ生成時点のデータで表示する。

## 更新のしかた

```bash
# 1) キャラの絵や動きを作り直すとき（画像生成 → 透過 → GIF/サムネイル）
OW=360 OFR=14 OCOL=128 OQM=max python3 otter-pet/make_anim.py

# 2) HTML を作り直す（state.json の内容と画像を埋め込む）
python3 otter-pet/render_app.py
python3 otter-pet/check_app.py        # ALL OK を確認

# 3) 公開（何度実行してもよい）
bash otter-pet/github-pages-repo/deploy.sh

# state.json だけ変わったとき（お世話の記録を反映するだけならこれで十分）
bash otter-pet/github-pages-repo/deploy.sh
```

`make_anim.py` は `avatar-child-mochi(-blink/-chirp).png` から背景を抜き、
`thumb-baby/child/adult.png`（アルバム用の透過サムネイル）と `otter-child-anim.gif` を作る。

`deploy.sh` は次を行う（何度実行してもよい）:

1. remote の `main` に合わせる（ローカルの未push履歴は捨てる）
2. ワークスペース直下の `otter-app.html` → `index.html`
3. `otter-pet/state.json` → `state.json`
4. 差分があれば commit & push（Pickle 名義）

Pages は main のルートを配信しているので、push の1〜2分後に反映される。
`state.json` の fetch は `?t=` を付けてキャッシュを避けている。

## 注意

- `state.json` を更新したら必ず `deploy.sh` で push する。push しないとふたりのスマホには古い状態が出る。
- `otter-pet/check_app.py` は HTML 構造・JS 構文・id・外部参照（fetch 先は相対パスのみ）に加えて、
  透過GIFと3段階のサムネイルが埋め込まれているかも検証する。
- `items` は `{name, emoji, by, memory, at}` の配列（`by` は koudai / hong / both）。文字列だけの旧形式でも表示できる。
