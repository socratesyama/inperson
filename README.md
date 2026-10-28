# 特定人物モザイク（Web版 / 解析・確認）

動画は端末内だけで処理され、外部へは送信されません。

## 1. モデルを置く
`buffalo_s.zip` を取得（https://github.com/deepinsight/insightface/releases/download/v0.7/buffalo_s.zip ）し、
中の `det_500m.onnx` と `w600k_mbf.onnx` を `models/` に置きます。
※ InsightFaceのモデルは非商用ライセンスです。

## 2. GitHub → Cloudflare Pages
```
git init && git add . && git commit -m "init"
git branch -M main && git remote add origin <your-repo-url> && git push -u origin main
```
Cloudflare ダッシュボード → Workers & Pages → Pages → Git に接続 → リポジトリ選択。
フレームワーク「なし」、ビルドコマンド空、出力ディレクトリ `/`（空でも可）でデプロイ。
`_headers` により WASM マルチスレッドが有効になります（Chrome/Edge 推奨）。

## 3. 使い方
動画と本人の顔写真を選び「解析開始」。終わると全員に枠とIDが出ます（赤=モザイク対象）。
表のチェックで修正し、「結果をJSON保存」で `tracks.json` を出力。
解析結果は同じ動画を選べば自動で復元されます（ブラウザ内に保存）。
「要確認リスト」で検出が途切れた所・本人トラックの切替・境界の人物を確認できます。
「書き出し開始」で、モザイク付き動画を等倍再生で録画して保存します（音声付き、MP4優先）。
書き出し中は動画の音がスピーカーから出ません。

## 精度を上げる（任意）
- `buffalo_l.zip`（https://github.com/deepinsight/insightface/releases/download/v0.7/buffalo_l.zip ）の `det_10g.onnx`(約17MB) は `models/` に置けます（自動で優先使用）。
- `w600k_r50.onnx`(約167MB) はPagesの25MB制限を超えるため、画面の「高精度モデル」から手元のファイルを選んでください（このブラウザのIndexedDBに保存され、以後は自動で使われます）。`det_10g.onnx` も同様に選べます。

## 手動モザイク
「描画モード: ON」で動画上をドラッグすると、現在位置から3秒の領域ができます。領域を選んで別の位置で描き直すとキーフレームが追加され、間は補間されます。「モザイクをプレビュー」で仕上がりを確認できます。

## モザイクエディタ（editor.html）
AviUtl風の手動編集画面です。動画を開き、プレビュー上をドラッグしてモザイクを作成します。
- タイムライン: バーをドラッグで移動（段の移動も可）、左右端で長さ調整、空き部分クリックでシーク
- プレビュー: 図形をドラッグで移動、四隅/辺で拡大縮小。「キーフレーム記録: ON」で再生位置ごとに位置を記録し、間を補間して追従
- 種類（モザイク/ぼかし）、形（四角/楕円）、粗さを右パネルで変更。分割(S)・複製・削除・元に戻す(Ctrl+Z)
- 自動解析（index.html）の tracks.json を「自動解析の取込」で読み込むと、本人トラックがモザイクとして並びます
- 編集内容は同じ動画を開けば自動復元。「プロジェクト保存」でJSON出力も可
- 「MP4書き出し」は等倍で再生しながら録画します（音声付き）。MP4直接録画に非対応のブラウザではwebmになります
