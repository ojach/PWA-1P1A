# 1P1A（One Page One App）& OJapp Dynamic PWA Suite

> 軽量なクライアントサイドスクリプトと、動的に生成される Data URL
> Manifest を使って、1ページ・ディレクトリ・サイト全体を独立した
> Progressive Web App（PWA）としてホーム画面に追加できます。

------------------------------------------------------------------------

## 🌟 概要

**1P1A（One Page One App）** は、ページやWebツールごとに静的な
`manifest.json`
を作成・管理する必要をなくす、クライアントサイドのPWA設計・実装アプローチです。

ページの `<head>` に OJapp のスクリプトを追加すると、Web App Manifest が
`data:application/manifest+json` URL として動的に生成されます。

選択するモードによって、ユーザーは個別のページ、ディレクトリ単位のグループ、またはサイト全体を、それぞれ独自のアプリ名・説明・アイコン・ID・起動URL・ナビゲーションスコープ・インストール表示を持つアプリとしてホーム画面に追加できます。

コアスクリプトの利用に、ビルド処理、ユーザー登録、外部API、サーバーサイドでのManifest生成は必要ありません。

------------------------------------------------------------------------

## 🚀 主な機能

-   **ビルド不要・バックエンド不要：** Cloudflare Pages、GitHub
    Pages、Vercel、従来型のWebサーバーなど、静的ホスティング環境でも利用できます。
-   **Dynamic Data URL Manifest：** ブラウザ上で実行時にWeb App
    Manifestを動的生成します。
-   **3つのApp構造：**
    ページ単位（1P1A）、ディレクトリ単位（1G1A）、サイト全体（1S1A）に対応します。
-   **Query対応（オプション）：** 有効にすると、現在のQuery Stringを
    `id`、`start_url`、`scope` に追加します。
-   **カスタムメタデータ：**
    metaタグからアプリ名、説明、アイコン、ID、起動URL、ナビゲーションスコープを設定できます。
-   **インストール表示：**
    静的Manifestを管理することなく、インストール専用の説明文や最大5枚のスクリーンショットを追加できます。
-   **完全クライアントサイド実行：**
    FREEスクリプトはユーザーアカウントや外部APIに依存せず動作します。

------------------------------------------------------------------------

## 💻 クイックスタート & App構造

OJappはスクリプト実行時にmetaタグを読み取ります。

すべてのOJapp用metaタグは、必ず**scriptタグより前**に記述してください。

### 1. 1P1A：One Page One App

現在のページ、またはQueryによって設定されたそのページの状態を、ユーザーのホーム画面に1つのAppとして追加します。

``` html
<!-- オプション設定はscriptより前に記述 -->
<meta name="ojapp:query" content="true">
<meta name="ojapp:title" content="My Custom Tool">
<meta name="ojapp:icon" content="/icon.png">

<script src="https://ojapp.app/js/ojapp_1p1a.js"></script>
```

オプションのmetaタグを指定しない場合、ページURL、ページタイトル、利用可能なページアイコンが自動的に使用されます。

### 2. 1S1A：One Site One App

同一Origin全体を、1つの統合されたAppとして扱います。

``` html
<script src="https://ojapp.app/js/ojapp_1s1a.js"></script>
```

デフォルトでは、`id`、`start_url`、`scope` にOrigin
Root（`/`）が使用されます。

### 3. 1G1A：One Group One App

`/dashboard/`、`/tools/`、`/docs/`
などのディレクトリを、同一Origin内の独立したAppとして扱います。

``` html
<meta name="ojapp:id" content="/dashboard/">
<meta name="ojapp:start-url" content="/dashboard/">
<meta name="ojapp:scope" content="/dashboard/">

<script src="https://ojapp.app/js/ojapp_1s1a.js"></script>
```

ディレクトリグループごとに異なるIDとscopeを指定できるため、個別のManifestファイルやビルド処理を用意することなく、同一Origin内に複数のグループAppを構成できます。

------------------------------------------------------------------------

## 🔗 Queryで設定するApp

Query対応を有効にするには、次のmetaタグを追加します。

``` html
<meta name="ojapp:query" content="true">
```

例えば、

``` text
/timer/?time=5&mode=down&seconds=on&icon=blue
```

というURLの場合、OJappは現在のQuery
Stringを、生成するManifestの以下の値に含めます。

-   `id`
-   `start_url`
-   `scope`

これにより、URLそのものにユーザーが選択したAppの状態を保持できます。

`id` によって設定の異なるインスタンスを識別し、`start_url`
によってホーム画面からAppを起動した際に、その状態を復元できます。

Manifestの各フィールドは、ブラウザごとの実装に従って処理されます。

特に `scope`
については、ブラウザが処理する際にQueryを正規化または削除する場合があります。OJappは設定されたQueryを3つのフィールドすべてに出力し、その後の正規化はブラウザ側の処理に委ねます。

ページの初回読み込み後にQueryを生成または変更する場合は、ユーザーにホーム画面への追加を案内する前に、完成したQuery
URLへ実際に遷移するか、再読み込みしてください。

History
APIによるURL変更だけでは、iOSの**「ホーム画面に追加」**で取得されるURLに反映されない場合があります。

------------------------------------------------------------------------

## 🖼️ インストール表示

OJappでは、対応ブラウザのインストール画面に表示される説明文やスクリーンショットをカスタマイズできます。

### インストール用説明文

検索結果用のページ説明とは別の文章をインストール画面に表示したい場合は、`ojapp:description`
を使用します。

``` html
<meta
  name="ojapp:description"
  content="Install this tool for quick access from your home screen."
>
```

OJappでは、次の優先順位で説明文を使用します。

1.  `ojapp:description`
2.  標準の `<meta name="description">`
3.  どちらも存在しない場合はManifestの `description` を省略

これにより、検索向けのページ説明と、インストール向けのメッセージを分けて管理できます。

### スクリーンショット1枚

1枚だけ設定する場合は、番号なしのmetaタグを使用します。

``` html
<meta
  name="ojapp:screenshot"
  content="/images/install.png"
>
```

### 複数のスクリーンショット

最大5枚まで、番号付きmetaタグで追加できます。

``` html
<meta name="ojapp:screenshot-1" content="/images/install-1.png">
<meta name="ojapp:screenshot-2" content="/images/install-2.png">
<meta name="ojapp:screenshot-3" content="/images/install-3.png">
```

番号付きのスクリーンショットタグが1つでも存在する場合は、番号付きの設定が優先され、番号なしの
`ojapp:screenshot` は無視されます。

同一セット内のスクリーンショットは、同じアスペクト比を使用することを推奨します。

デスクトップとモバイルの両方のインストール画面で確認しやすいため、**1:1の正方形画像**を推奨します。

OJappは選択されたスクリーンショットに対して、デスクトップでは
`wide`、Androidでは `narrow`
を設定します。最終的な表示レイアウトはブラウザによって決定されます。

------------------------------------------------------------------------

## 🛠️ Metadata リファレンス

  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  Meta Tag               対象              説明                                                                    デフォルト / フォールバック
  ---------------------- ----------------- ----------------------------------------------------------------------- ---------------------------------------------------------
  `ojapp:title`          1P1A / 1S1A       App名を設定                                                             1P1A：ページの `<title>`、1S1A：hostname

  `ojapp:description`    1P1A / 1S1A       Manifestのインストール用説明文を設定                                    標準のmeta description。存在しない場合は省略

  `ojapp:icon`           1P1A / 1S1A       AppアイコンURLを設定                                                    利用可能なページアイコン、その後OJappデフォルトアイコン

  `ojapp:screenshot`     1P1A / 1S1A       インストール用スクリーンショットを1枚設定                               省略

  `ojapp:screenshot-1`   1P1A / 1S1A       最大5枚のインストール用スクリーンショットを設定。番号付きセットを優先   省略
  ～                                                                                                               
  `ojapp:screenshot-5`                                                                                             

  `ojapp:query`          1P1A / 1S1A       `"true"` で現在のQueryを `id`、`start_url`、`scope` に追加              無効。Queryを除外

  `ojapp:id`             1S1A / 1G1A       ManifestのApp IDを設定                                                  Origin Root

  `ojapp:start-url`      1S1A / 1G1A       ホーム画面から起動するURLを設定                                         Origin Root

  `ojapp:scope`          1S1A / 1G1A       ナビゲーションスコープを設定                                            Origin Root

  `ojapp:exclude`        1P1A / 1S1A       `"true"` でそのページ上のOJappを無効化                                  無効
  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------

カスタム指定する `id`、`start_url`、`scope`
は、すべて現在のページと同一Originに解決される必要があります。

------------------------------------------------------------------------

## 📱 プラットフォーム別の挙動

### iOS / iPadOS（Safari）

Queryによって設定されたホーム画面エントリは、iPhone実機で動作確認済みです。

同じページから作成した複数のエントリでも、それぞれ異なる起動状態、App名、アイコンを保持できます。

インストールされるアイコンは `apple-touch-icon`
の影響を受ける場合があります。

アイコンを動的に切り替えるページでは、OJappスクリプトが実行される前に
`apple-touch-icon` を設定してください。

### Android（Chrome）

OJappは生成する `id`、`start_url`、`scope` にQueryを含めます。

ChromeはWeb App
Manifest仕様に従って、処理後のナビゲーションscopeを正規化する場合があります。

Queryを利用した複数Appの識別挙動はブラウザやプラットフォームのバージョンによって異なる可能性があるため、対象となるAndroid端末で実際の動作を確認してください。

------------------------------------------------------------------------

## 🧭 モードの選び方

  ------------------------------------------------------------------------------------------------------------------
  モード            App単位                デフォルトのID / Start /   主な用途
                                           Scope                      
  ----------------- ---------------------- -------------------------- ----------------------------------------------
  **1P1A**          現在のページ           現在のページPath           ツール、商品、記事、プロフィール

  **1G1A**          ディレクトリグループ   明示したディレクトリPath   ダッシュボード、ドキュメント、ツールグループ

  **1S1A**          サイト全体             Origin Root `/`            一般的なサイト全体PWA
  ------------------------------------------------------------------------------------------------------------------

------------------------------------------------------------------------

## OJapp FREE

OJapp FREEは、1P1A（One Page. One App.）および1S1A（One Site. One
App.）というPWA設計を簡単に実装するための仕組みを提供します。

通常利用では、OJapp
FREEは以下のOJapp公式ホストスクリプトを通じて提供されます。

https://ojapp.app/

実際に運用されているOJapp FREEのProduction
Runtimeは、このリポジトリではJavaScriptファイルとして配布していません。

このリポジトリでは、学習・研究・改変・独自実装を目的として、Reference
Source Codeを `.txt` ファイルとして公開する場合があります。

OJapp
FREEをそのまま利用したい場合は、ドキュメントに記載されたOJapp公式ホストスクリプトをご利用ください。

------------------------------------------------------------------------

## License

特に記載がない限り、このリポジトリ内に公開されているソースコードにはMIT
Licenseが適用されます。

詳細については `LICENSE` ファイルをご確認ください。

このリポジトリのMIT
Licenseが適用されるのは、**実際にこのリポジトリ内で公開されているソースコードのみ**です。

`ojapp.app` から配信されるOJapp FREEのHosted Runtime、およびOJapp
PROは、このリポジトリとは別のものであり、このリポジトリのMIT
Licenseの対象ではありません。

OJapp PROは、商用のプロプライエタリソフトウェアです。

------------------------------------------------------------------------

## 1P1AとUDAは「設計思想」です

**1P1A（One Page. One App.）** と **UDA（User Defined App）**
は製品ではなく、PWAの設計思想です。

1P1Aは、Webサイト全体ではなく、**ページ単位でAppの境界を定義する**という設計アプローチです。

UDAはこの考え方をさらに拡張し、Query Parameterなどで表現される特定のURL
Stateを利用して、**ユーザー自身がAppを定義できる**という考え方です。

OJappは、これらの設計思想をもとに構築された実装・サービスです。

このリポジトリのMIT
Licenseは、ここで公開されているソースコードにのみ適用されます。1P1AまたはUDAという設計思想そのものにMIT
Licenseが適用されるものではありません。

------------------------------------------------------------------------

## 🤝 Community & Feedback

質問、実機テスト結果、Edge Case、Feature
Requestなど、このリポジトリへのフィードバックを歓迎します。

Reference Implementationは、現在運用されているOJapp FREEのProduction
Versionとは異なる場合があります。

また、Reference ImplementationはOJapp FREE公式Hosted
Runtimeのすべてのアップデートへ追従することを目的としていません。

-   **Website / Utilities:** [OJapp
    1P1A](https://ojapp.app/one-page-one-app/en/)
-   **Developer:** OJapp / Ojach
