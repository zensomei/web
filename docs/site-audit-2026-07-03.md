# Site audit: legacy static website before Astro migration

作業日: 2026-07-03

対象リポジトリ: `zensomei/web`  
公開URL: https://zensomei.github.io/web/  
監査時点の基準コミット: `1c940ac` (`origin/master`, "Update index.html")

## 目的

このメモは、現行のGitHub Pagesサイトを壊さずに保存し、今後Astroへ移行する前提情報をCodex / ChatGPTが参照できるようにするための監査記録です。

今回はAstro化、デザイン変更、CSS整理、コンテンツ更新、CV/PDF更新は行っていません。

## 現在のブランチと公開方式

- デフォルトブランチ: `master`
- GitHub Pages公開元: `master` ブランチのルート `/`
- GitHub Pages build type: `legacy`
- カスタムドメイン: なし
- 公開URL: https://zensomei.github.io/web/
- HTTPS enforced: 有効

バックアップとして、以下を作成済みです。

- バックアップブランチ: `backup/legacy-mobirise-20260703`
- annotated tag: `legacy-before-astro-20260703`
- タグメッセージ: `Legacy static website before Astro migration`

## 現在のサイト構成

現行サイトは、ビルドステップを持たない静的HTMLサイトです。`index.html` が主要ページで、CSS/JS/画像/PDFをリポジトリ内に直接配置しています。

主要なトップレベルファイル:

- `index.html`: サイト本体。プロフィール、Recent Work、Main Publications、Main Backgrounds、フッターまでを1ファイルに直書き。
- `_config.yml`: GitHub Pages / Jekyll用設定。`jekyll-sitemap` と `jekyll-seo-tag` を指定。
- `robots.txt`: 検索エンジン向け設定。
- `google6d8d66b7b47b2c45.html`: Google Search Console等の所有確認用と思われるファイル。
- `.gitignore`: `desktop.ini` のみ無視。
- `assets/`: Mobirise / Bootstrap由来のCSS、JS、画像。
- `documents/`: CV PDF。

## `index.html` の構造

`index.html` はMobiriseで生成されたHTMLです。

確認できた主な構造:

- `<head>`
  - Mobirise v5.9.13 のgeneratorコメント/メタタグ
  - `viewport` metaあり
  - Bootstrap / Mobirise / theme CSSを読み込み
  - Google Fonts (`Inter Tight`) を外部読み込み
- navigation
  - `Zen Somei` ロゴ/名前
  - `Publications`, `Backgrounds` へのページ内リンク
  - コメントアウトされたHome dropdownあり
- profile section
  - 顔写真らしき画像 `assets/images/img-2999.jpeg`
  - 氏名、所属、研究興味、CV、LinkedIn、メール
- Recent Work
  - YouTube iframe: `https://www.youtube.com/embed/h4mIWzhCgyw`
  - Eurohaptics'22 Demo Awardの説明
- Main Publications
  - ToH'24 under review表記の項目
  - Eurohaptics'22 paper項目
  - 論文画像、外部論文リンク、説明文をHTMLに直書き
- Main Backgrounds
  - Internships
  - Fellowships
  - Awards
  - Education
  - テーブルで経歴を直書き
- footer
  - Mobiriseクレジットリンクあり

## `assets/` 配下の構成

主なディレクトリ:

- `assets/bootstrap/`
  - Bootstrap CSS/JS。CSSヘッダからBootstrap 5.0.1系と確認。
- `assets/dropdown/`
  - Mobirise系navbar/dropdown CSS/JS。
- `assets/images/`
  - サイトで使う画像と `hashes.json`。
- `assets/mobirise/`
  - Mobirise追加CSS。セクション固有の `cid-*` クラス定義が多い。
- `assets/smoothscroll/`
  - スムーススクロール用JS。
- `assets/theme/`
  - MobiriseテーマCSS/JS。
- `assets/ytplayer/`
  - YouTube背景/プレイヤー関連と思われるJS。

画像アセット:

- `assets/images/img-2999.jpeg`: プロフィール画像として使用。
- `assets/images/mbr-3.png`: favicon / navbar logoとして使用。
- `assets/images/toh2023-1.svg`: PublicationsのToH項目で使用。
- `assets/images/euro2022-3.png`: PublicationsのEurohaptics項目で使用。
- `assets/images/euro2022-4.png`: 現行HTMLでは直接参照が見当たらない。移行時に利用有無を確認。
- `assets/images/hashes.json`: Mobirise生成物。`img-2998.jpeg` への参照があるが、そのファイルは現在のtreeには見当たらない。

## `documents/` 配下の構成

Git上では以下2つのPDFが追跡されています。

- `documents/CV_somei.pdf`
- `documents/CV_Somei.pdf`

注意: この2ファイルは大文字小文字だけが異なります。Windowsの通常のcase-insensitiveなファイルシステムでは同時に安全にcheckoutできず、今回のclone直後から `documents/CV_Somei.pdf` が変更扱いになりました。Astro移行前に、どちらを公開用CVとして残すかを確認し、ファイル名を一意に整理する必要があります。

現行HTMLからリンクされているのは `documents/CV_somei.pdf` です。

## `_config.yml`

内容:

```yml
plugins:
  - jekyll-sitemap
  - jekyll-seo-tag
```

GitHub Pages legacy buildでJekyll pluginを使う意図と思われます。ただし現行サイトは実質的に静的HTMLで、JekyllテンプレートやMarkdownページは見当たりません。

Astro移行後は、GitHub ActionsでAstro buildを行う構成にする場合、このファイルの扱いを再検討します。

## GitHub Actions / package manager / build script

監査時点では以下は見つかりません。

- `.github/workflows/`
- `package.json`
- lockfile (`package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`)
- `astro.config.*`

つまり、現時点ではローカルbuild/checkコマンドは定義されていません。

## 外部生成ツール・ライブラリ依存

確認できる依存:

- Mobirise Website Builder v5.9.13
- Bootstrap 5.0.1系
- Google Fonts (`Inter Tight`)
- YouTube iframe
- Mobirise theme/dropdown/smoothscroll/ytplayer JS

注意点:

- HTML/CSS/JSにMobirise固有の `mbr-*`, `cid-*`, `data-bs-version` が多数ある。
- `assets/theme/js/script.js` はかなり大きく、汎用テーマ機能や動画背景処理など、現行ページで使っていない可能性がある処理も含む。
- 生成CSSはセクション固有クラスが多く、手作業で安全に部分削除するのは難しい。

## 現状サイトの良い点

- 公開方式が単純で、`master` のルートをそのままGitHub Pagesで配信している。
- 主要コンテンツが `index.html` に集約されており、移行対象の情報を発見しやすい。
- プロフィール、研究興味、主要論文、経歴、受賞、CVリンクが一通り揃っている。
- 画像やPDFはリポジトリ内にあり、外部サービス依存が比較的少ない。
- 既存URL `https://zensomei.github.io/web/` を維持しやすい。

## 現状サイトの問題点

- コンテンツと見た目が `index.html` に密結合している。
- 論文、経歴、受賞、学歴などがHTMLテーブルや段落に直書きされているため、更新ミスが起きやすい。
- Mobirise生成HTML/CSS/JSに強く依存しており、構造を理解しないまま編集すると崩れやすい。
- `documents/CV_somei.pdf` と `documents/CV_Somei.pdf` のcase-only重複があり、Windows/macOS環境で事故が起きやすい。
- `assets/images/hashes.json` に存在しない画像名への参照がある。
- `meta name="description"` が空。
- 画像altが `Mobirise Website Builder` のままで、アクセシビリティ/SEO上よくない。
- 日本語氏名を含むテキストはUTF-8で扱う必要がある。ツールによっては表示が文字化けするため、編集時のencoding確認が必要。
- Publicationsに `under review` など時点依存の表記があり、更新時に要確認。

## スマホ対応上の懸念

- Bootstrap gridは使われているが、Mobirise生成CSSに依存しており、各セクションの余白・フォントサイズ・画像比率が現在のスマホ表示に最適化されているとは限らない。
- Backgrounds内の経歴はHTML tableで表現されているため、狭い画面で日付列と内容列が窮屈になる可能性が高い。
- Publicationsの長いタイトル、著者リスト、説明文がモバイル幅で読みづらくなる可能性がある。
- Navbarはcollapse対応だが、Mobirise dropdown JSに依存している。
- YouTube iframeは固定 `width="500" height="400"` が指定されており、CSS側の補正が効かない場合にモバイルで崩れる可能性がある。

## コンテンツ更新上の懸念

- 所属、肩書き、フェローシップ、インターン期間、論文ステータスなどがすべてHTMLに埋め込まれている。
- 論文情報にDOI、paper URL、project URL、video URL、award情報などの構造化フィールドがない。
- CVリンクのファイル名がcase-only重複しており、更新時にどちらを差し替えるべきか不明確。
- 英語プロフィールとして使うには、短いbio、research statement、selected works、contact/social linkなどをデータ化した方がよい。
- 今後Codexに安全に更新させるには、YAML/Markdownの更新テンプレートとレビュー手順が必要。

## Astro化するときに移行すべき情報

候補データファイル:

- `src/content/profile.yml`
  - 氏名
  - 表記名
  - 所属
  - 肩書き
  - 研究興味
  - email
  - LinkedIn等の外部リンク
  - CVファイルパス
- `src/content/publications.yml`
  - title
  - authors
  - venue
  - year
  - status
  - type
  - links
  - image
  - description
  - award/acceptance rate
- `src/content/experiences.yml`
  - internships
  - education
  - fellowships
- `src/content/awards.yml`
  - award name
  - date
  - event
  - links
- `src/content/works.yml` またはMarkdown
  - Recent Work / project / video information

静的アセットとして移行・保持すべきもの:

- プロフィール画像
- 論文/研究画像
- CV PDF
- Google verification fileが現在も必要なら保持
- `robots.txt`

## 消してよさそうなもの / 残すべきもの

今回削除は行っていません。以下は移行時の確認候補です。

残すべきもの:

- 現行公開URLの互換性
- `index.html` に含まれるプロフィール、研究、経歴、受賞、論文情報
- 現行CVへの導線
- 使用中画像: `img-2999.jpeg`, `mbr-3.png`, `toh2023-1.svg`, `euro2022-3.png`
- `google6d8d66b7b47b2c45.html` が現在もSearch Consoleで使われているなら保持
- `robots.txt`

削除または整理候補:

- Mobirise生成CSS/JS一式。ただしAstro移行完了後、表示確認とURL互換を確認してから削除する。
- `assets/images/euro2022-4.png` が未使用なら整理候補。
- `assets/images/hashes.json` はMobirise由来の可能性が高く、Astro移行後は不要になりうる。
- `documents/CV_Somei.pdf` と `documents/CV_somei.pdf` の重複。どちらを正式な公開CVにするか確認してから整理する。

## 移行時に注意すべき点

- GitHub Pagesのbase pathは `/web/`。Astroでは `site` と `base` 設定を誤るとアセットURLが壊れる。
- 現行Pages sourceは `master` `/`。Astro移行時にGitHub Actions配信へ切り替えるか、`docs/` 配信にするか、`gh-pages` ブランチ配信にするかを明示的に決める。
- case-onlyファイル名重複を先に解決する。特にWindows/macOSでの作業時に危険。
- PDF/CVの中身は勝手に更新しない。更新する場合は本人確認済みの最新版を使う。
- 研究者HPとして、論文ステータスや所属を推測で更新しない。
- 既存URLに外部からリンクされている可能性があるため、CV URLや論文URLを変更する場合はredirectまたは互換パスを検討する。
- MobiriseからAstroへ移すとCSS/JSが大きく変わるため、まずデータ抽出、次にコンポーネント化、最後にデザイン刷新の順が安全。
- YouTube iframe、外部論文リンク、LinkedIn、研究室リンクなど外部リンクは移行後にリンクチェックする。
- 日本語名、英語名、プロフィール文はUTF-8で管理し、エディタ/ツールのencodingを確認する。

## 次フェーズでやるべき作業案

1. case-only PDF重複の解消方針を決める。
   - 現行HTMLは `CV_somei.pdf` を参照。
   - `CV_Somei.pdf` を残す必要があるか確認。
2. Astro移行方針を決める。
   - GitHub Pagesの公開方式をGitHub Actionsにするか。
   - URL base `/web/` をどう設定するか。
3. コンテンツデータの初期スキーマを作る。
   - profile
   - publications
   - experiences
   - awards
   - works/news
4. `index.html` からコンテンツを抽出し、YAML/Markdownの初期データに落とす。
5. Astroプロジェクトを別ブランチで作成し、既存URL互換を保ちながら静的buildできるようにする。
6. 最低限のCIを追加する。
   - install
   - build
   - link/checkまたはHTML check
7. README / CONTRIBUTING / update template / Issue templateを整備する。
8. モバイル表示をPlaywright等で確認する。
9. デザイン刷新は、情報設計とデータ分離が済んだ後に行う。

## 今回実施したこと

- GitHub Pages設定の確認。
- 現行リポジトリ構成の確認。
- Mobirise / Bootstrap依存の確認。
- 静的アセット、PDF、Jekyll設定、Actions/package有無の確認。
- バックアップブランチ作成。
- annotated tag作成。
- この監査メモの追加。

## 今回実施していないこと

- Astroのインストール。
- `index.html` の編集。
- 既存CSS/JS/画像/PDFの削除・整理。
- デザイン変更。
- コンテンツ内容の更新。
- GitHub Pages公開設定の変更。
