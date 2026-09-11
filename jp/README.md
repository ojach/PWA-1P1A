# 1P1A（One Page One App）& OJapp Dynamic PWA Suite

> 軽量なクライアントサイドスクリプトと、動的に生成されるData URL Manifestを使って、ページ、ディレクトリ、またはサイト全体をスタンドアロンのProgressive Web App（PWA）に変換します。

---

## 🌟 概要

<strong>1P1A（One Page. One App.）</strong>は、ページやツールごとに静的な`manifest.json`を作成・管理する必要をなくす、クライアントサイドのPWA設計手法です。

ページの`<head>`にOJappスクリプトを追加すると、Web App Manifestが`data:application/manifest+json` URLとして動的に生成されます。選択したモードに応じて、訪問者は個別ページ、ディレクトリ単位のグループ、またはサイト全体を、固有のアプリ名、説明、アイコン、識別情報、起動URL、ナビゲーションスコープ、インストール表示を持つアプリとしてホーム画面に追加できます。

コアスクリプトは、ビルド処理、ユーザー登録、外部API、サーバーサイドでのManifest生成を必要としません。

---

## 🚀 主な機能

- **ビルド不要・バックエンド不要：** Cloudflare Pages、GitHub Pages、Vercel、一般的なWebサーバーなどの静的ホスティングで動作します。
- **動的Data URL Manifest：** ブラウザ上で実行時にWeb App Manifestを生成します。
- **3つのアプリ構成：** ページ単位（1P1A）、ディレクトリ単位（1G1A）、サイト全体（1S1A）に対応します。
- **任意のクエリ処理：** 有効にすると、現在のクエリ文字列を`id`、`start_url`、`scope`へ追加します。
- **カスタムメタデータ：** メタタグを使って、アプリ名、説明、アイコン、識別情報、起動URL、ナビゲーションスコープを設定できます。
- **インストール表示：** 静的なManifestを管理せずに、専用のインストール説明文と1枚または複数（最大5枚）のスクリーンショットを追加できます。
- **完全なクライアントサイド実行：** FREE版スクリプトは、ユーザーアカウントや外部APIへの依存なしで動作します。

---

## 💻 クイックスタートとアプリ構成

OJappは、スクリプト実行時にメタタグを読み取ります。すべてのOJappメタタグは、必ずスクリプトタグの**前**に配置してください。

### 1. 1P1A：One Page One App

現在のページ、またはクエリで設定されたそのページの状態を、ユーザーのホーム画面に1つのアプリとして追加します。

```html
<!-- 任意設定はスクリプトより前に記述します -->
<meta name="ojapp:query" content="true">
<meta name="ojapp:title" content="My Custom Tool">
<meta name="ojapp:icon" content="/icon.png">

<script src="https://ojapp.app/js/ojapp_1p1a.js"></script>
```

任意のメタタグを指定しない場合は、ページURL、ページタイトル、利用可能なページアイコンが自動的に使用されます。

### 2. 1S1A：One Site One App

同一オリジン全体を、1つの統合されたアプリにします。

```html
<script src="https://ojapp.app/js/ojapp_1s1a.js"></script>
```

初期状態では、`id`、`start_url`、`scope`にオリジンルート（`/`）が使用されます。

### 3. 1G1A：One Group One App

`/dashboard/`、`/tools/`、`/docs/`などのディレクトリを、同一オリジン内の独立したアプリにします。

```html
<meta name="ojapp:id" content="/dashboard/">
<meta name="ojapp:start-url" content="/dashboard/">
<meta name="ojapp:scope" content="/dashboard/">

<script src="https://ojapp.app/js/ojapp_1s1a.js"></script>
```

ディレクトリグループごとに異なるIDとscopeを設定できるため、個別のManifestファイルやビルド処理を用意せずに、1つのオリジン内へ複数のグループアプリを作成できます。

---

## 🔗 クエリで設定するアプリ

次の記述でクエリ処理を有効にします。

```html
<meta name="ojapp:query" content="true">
```

例：

```text
/timer/?time=5&mode=down&seconds=on&icon=blue
```

OJappは、生成するManifestの次の値に現在のクエリ文字列を含めます。

- `id`
- `start_url`
- `scope`

これにより、URLへアプリの選択状態を保存できます。`id`は設定の異なるインスタンスを識別し、`start_url`はアプリ起動時にその状態を復元します。

ブラウザは、それぞれのプラットフォーム実装に従ってManifestの各項目を処理します。特に、ブラウザによっては処理後の`scope`からクエリが正規化または削除されることがあります。OJappは設定されたクエリを3項目すべてへ記述し、その後の正規化はブラウザに委ねます。

ページが初回読み込み後にクエリを生成または変更する場合は、ホーム画面への追加を案内する前に、完成したクエリURLへ移動するか、そのURLでページを再読み込みしてください。History APIによる変更だけでは、iOSの<strong>「ホーム画面に追加」</strong>で取得されるURLが更新されない場合があります。

---

## 🖼️ インストール表示

OJappでは、対応ブラウザのインストール画面に表示される説明文とスクリーンショットをカスタマイズできます。

### インストール説明文

インストール画面に、ページの検索向け説明文とは異なる文章を表示したい場合は、`ojapp:description`を使用します。

```html
<meta
  name="ojapp:description"
  content="Install this tool for quick access from your home screen."
>
```

OJappは次の優先順位で使用します。

1. `ojapp:description`
2. 標準の`<meta name="description">`
3. どちらも存在しない場合は、Manifestの`description`を省略

これにより、検索向けのページ説明文と、インストール向けの案内文を分けて管理できます。

### スクリーンショット1枚

画像を1枚だけ指定する場合は、番号なしの従来タグを使用します。

```html
<meta
  name="ojapp:screenshot"
  content="/images/install.png"
>
```

### 複数のスクリーンショット

番号付きタグを使うと、最大5枚まで追加できます。

```html
<meta name="ojapp:screenshot-1" content="/images/install-1.png">
<meta name="ojapp:screenshot-2" content="/images/install-2.png">
<meta name="ojapp:screenshot-3" content="/images/install-3.png">
```

番号付きスクリーンショットタグが1つでも存在する場合は、番号付きのセットが優先され、番号なしの`ojapp:screenshot`タグは無視されます。

セット内のすべてのスクリーンショットには、同じアスペクト比を使用してください。デスクトップとモバイルの両方のインストール画面で見やすい、**1:1の正方形画像**を推奨します。OJappは選択したスクリーンショットをデスクトップでは`wide`、Androidでは`narrow`に設定します。最終的な表示レイアウトはブラウザによって決まります。

---

## 🛠️ メタデータ一覧

| メタタグ | 対象 | 説明 | 初期値／フォールバック |
| :--- | :--- | :--- | :--- |
| `ojapp:title` | 1P1A / 1S1A | アプリ名を設定 | 1P1A：ページの`<title>`、1S1A：ホスト名 |
| `ojapp:description` | 1P1A / 1S1A | Manifestのインストール説明文を設定 | 標準のページメタディスクリプション。なければ省略 |
| `ojapp:icon` | 1P1A / 1S1A | アプリアイコンのURLを設定 | 利用可能なページアイコン、次にOJappのデフォルトアイコン |
| `ojapp:screenshot` | 1P1A / 1S1A | インストール用スクリーンショットを1枚設定 | 省略 |
| `ojapp:screenshot-1`～`ojapp:screenshot-5` | 1P1A / 1S1A | 最大5枚のインストール用スクリーンショットを設定。番号付きセットを優先 | 省略 |
| `ojapp:query` | 1P1A / 1S1A | `"true"`にすると、現在のクエリを`id`、`start_url`、`scope`へ追加 | 無効。クエリを削除 |
| `ojapp:id` | 1S1A / 1G1A | Manifestのアプリ識別情報を設定 | オリジンルート |
| `ojapp:start-url` | 1S1A / 1G1A | ホーム画面から開くURLを設定 | オリジンルート |
| `ojapp:scope` | 1S1A / 1G1A | ナビゲーションスコープを設定 | オリジンルート |
| `ojapp:exclude` | 1P1A / 1S1A | `"true"`にすると、そのページでOJappを無効化 | 無効 |

カスタム指定する`id`、`start_url`、`scope`は、すべて現在のページと同じオリジンへ解決される必要があります。

---

## 📱 プラットフォームごとの動作

### iOS / iPadOS（Safari）

クエリで設定したホーム画面項目は、iPhoneで動作確認済みです。同じページから複数の項目を作成しても、それぞれ異なる起動状態、アプリ名、アイコンを保持できます。

インストールされるアイコンは`apple-touch-icon`の影響を受ける場合があります。アイコンを動的に選択するページでは、OJappスクリプトが実行される前に設定してください。

### Android（Chrome）

OJappは、生成する`id`、`start_url`、`scope`へクエリを含めます。ChromeはWeb App Manifest仕様に従って、処理後のナビゲーションスコープを正規化する場合があります。クエリを使った複数インストールの識別動作は、ブラウザやプラットフォームのバージョンによって異なる可能性があるため、対象のAndroid端末で想定した動作を確認してください。

---

## 🧭 モードの選び方

| モード | アプリの単位 | 標準の識別情報／起動URL／scope | 主な用途 |
| :--- | :--- | :--- | :--- |
| **1P1A** | 現在のページ | 現在のページパス | ツール、商品、記事、プロフィール |
| **1G1A** | ディレクトリグループ | 明示的に指定したディレクトリパス | ダッシュボード、ドキュメント、グループ化したツール |
| **1S1A** | サイト全体 | オリジンルート`/` | 一般的なサイト全体のPWA |

---

## OJapp FREE

OJapp FREEは、1P1A（One Page. One App.）と1S1A（One Site. One App.）のPWA設計パターンを簡単に実装するためのサービスです。

通常利用では、OJapp FREEは次の公式ホストスクリプトを通じて提供されます。

https://ojapp.app/

本番環境のOJapp FREEランタイムは、このリポジトリからJavaScriptファイルとして配布されません。

学習、研究、改変、独自実装を目的とした参考ソースコードが、このリポジトリで`.txt`ファイルとして提供される場合があります。

OJapp FREEをそのまま利用したい場合は、ドキュメントに記載されている公式ホストスクリプトを使用してください。

---

## ライセンス

特に記載がない限り、このリポジトリに含まれるソースコードにはMITライセンスが適用されます。

詳細は`LICENSE`ファイルを参照してください。

このリポジトリのMITライセンスが適用されるのは、実際にこのリポジトリで公開されているソースコードだけです。

`ojapp.app`から提供されるOJapp FREEのホスト版ランタイムとOJapp PROは、このリポジトリとは別のものであり、このリポジトリのMITライセンスの対象には含まれません。

OJapp PROは、独自ライセンスの商用ソフトウェアです。

---

## 1P1AとUDAは設計概念です

**1P1A（One Page. One App.）**と**UDA（User Defined App）**は製品名ではなく、PWAの設計概念です。

1P1Aは、アプリの境界をWebサイト全体ではなく、ページ単位で定義する設計手法を表します。

UDAはこの考え方をさらに発展させ、クエリパラメータなど、特定のURL状態を通じてユーザー自身がアプリを定義できるようにする考え方です。

OJappは、これらの概念をもとに構築された実装およびサービスです。

このリポジトリのMITライセンスが適用されるのは、ここで公開されているソースコードだけです。1P1AやUDAの概念そのものには適用されません。

---

## 🤝 コミュニティとフィードバック

質問、テスト結果、特殊な事例、機能リクエストを、このリポジトリで歓迎します。

参考実装は、現在のOJapp FREE本番版と異なる場合があります。また、公式ホスト版ランタイムのすべての更新を追跡することを目的としたものではありません。

- **Webサイト／ツール：** [OJapp 1P1A](https://ojapp.app/one-page-one-app/en/)
- **開発者：** OJapp / Ojach
