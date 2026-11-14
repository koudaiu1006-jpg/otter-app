# もちカワウソ育成アプリ（公開用）

耕大さんと虹さんのカワウソ育成アプリ。`index.html`（画像もbase64で埋め込み済み・外部CDNなし）と、
ふたり共通の育成データ `state.json` の2ファイルで動く。

- 公開URL: https://koudaiu1006-jpg.github.io/otter-app/
- 共通データ: https://koudaiu1006-jpg.github.io/otter-app/state.json
- リポジトリ: https://github.com/koudaiu1006-jpg/otter-app

## いまの状態（2026-11-14 14:15時点）

**公開済み。** GitHub Pages は main / (root) でビルドされ `status: built`、
`https://koudaiu1006-jpg.github.io/otter-app/` と `/state.json` はどちらも HTTP 200。

アプリは起動時と「🔄 最新を読み込む」ボタン、画面に戻ったとき（60秒以上経過時）に
`./state.json?t=...` を fetch して、Lv・XP・4つのゲージ・お世話回数・もちもの・きろくを描き直す。
fetch できないとき（`file://` で開いた・オフライン）は HTML に埋め込んだ生成時点のデータで表示する。
おさかなゲームの最高記録だけは端末ごとの localStorage。

## 更新のしかた

```bash
# 育成データやアバターを変えたとき（HTML も作り直す）
python3 otter-pet/render_app.py
bash otter-pet/github-pages-repo/deploy.sh

# state.json だけ変わったとき（お世話の記録を反映するだけならこれで十分）
bash otter-pet/github-pages-repo/deploy.sh
```

`deploy.sh` は次を行う（何度実行してもよい）:

1. remote の `main` に合わせる（ローカルの未push履歴は捨てる）
2. ワークスペース直下の `otter-app.html` → `index.html`
3. `otter-pet/state.json` → `state.json`
4. 差分があれば commit & push（Pickle 名義）

Pages は main のルートを配信しているので、push の1〜2分後に反映される。
`state.json` の fetch は `?t=` を付けてキャッシュを避けている。

## 注意

- `state.json` を更新したら必ず `deploy.sh` で push する。push しないとふたりのスマホには古い状態が出る。
- `otter-pet/check_app.py` は HTML 構造・JS 構文・id・外部参照（fetch 先は相対パスのみ）を検証する。
