# 建物年代マップ（PLATEAU 3D Tiles × 建築年）

公開URL: https://onetech-jp.github.io/plateau-building-age-map/

PLATEAU の建築物モデル（3D Tiles ストリーミング配信）を CesiumJS で読み込み、
`bldg:yearOfConstruction`（建築年）で色分けする単一HTMLのビューア。ビルド不要・ダウンロード不要。

## 起動

```bash
python3 -m http.server 5193 --directory ~/dev/lab/260919_建物年代マップ
```

→ http://localhost:5193 （Claude Code の preview では `tatemono-nendai-map`）

## 機能

- 地域選択: 建築年を持つ 189 データセット（`datasets.js`）から都道府県→市区町村を選択。LOD2 があれば切替可
- 色分けモード
  - 耐震区分: 〜1981 / 1982〜2000 / 2001〜
  - 築年数（改修期）: 築50年以上 / 30〜49 / 15〜29 / 15年未満（実行年基準）
  - 年代グラデ: 10年刻み
  - ターゲット抽出: 築N年以上 × 用途（複数） × 高さ以上 → 該当のみ強調（ISX/配管更新の営業ターゲット地図）
- 読込済み建物の集計（gml_id で重複除外）、建築年あり率
- クリックで建物属性（名称・建築年・用途・高さ・階数・構造・耐火・延床・用途地域・住所・建物ID）
- 背景: 地理院タイル 淡色 / 航空写真 / 無地
- URL ハッシュで状態共有: `#city=11224&mode=target`

## データの重要な制約（2026-09-19 調査）

- PLATEAU 配信の建築物 3D Tiles（LOD1）全 451 件のうち、建築年属性を持つのは **189 件**。
- **東京23区（2025年度版・2023年度版とも）、横浜市・川崎市・相模原市には建築年が入っていない**
  （都市計画基礎調査の建築年を公開していないため）。首都圏で使えるのは埼玉県（43市町）と千葉県多古町。
- 建築年の値 `1` は不明を意味する（戸田市で不明率 約36%）。
- 属性名は `bldg:yearOfConstruction`（バイナリ SHORT/BYTE 格納のものもある）。用途は `bldg:usage`（日本語ラベル）。

## 再生成

`datasets.js` は PLATEAU データカタログ API
`https://api.plateauview.mlit.go.jp/datacatalog/plateau-datasets` を取得し、各 `tileset.json` の
`properties` に `bldg:yearOfConstruction` があるものだけを抽出して作成（中心座標は root boundingVolume から算出）。

## 出典

- Project PLATEAU（国土交通省）3D都市モデル: https://www.mlit.go.jp/plateau/ （商用利用可）
- 地理院タイル: https://maps.gsi.go.jp/development/ichiran.html
