# マイアプリ テンプレート

よく使うWebページを、スマホのアプリ画面のようにまとめられるHTMLテンプレートです。

`my-app-template.html`には **OJapp Free** が導入済みです。公開したページをホーム画面に追加すると、アプリのように起動できます。

## 使い方

### 1. HTMLを開く

`my-app-template.html`をテキストエディタで開きます。

### 2. 4か所を書き換える

HTML内で `【編集` を検索すると、変更する場所が見つかります。

1. **アプリ名・説明・ホーム画面アイコン**
2. **全体の色と見た目**
3. **画面上部の文章**
4. **表示するアプリ一覧**

アプリ一覧は、次の部分を書き換えます。

```js
{
  name: "ショップ",
  url: "https://example.com/shop/",
  icon: "🛍️",
  color: "#fff0f4"
}
```

- `name`：アイコンの下に表示する名前
- `url`：開きたいページのURL
- `icon`：絵文字または画像URL
- `color`：アイコンの背景色

`{ ... }`をコピーすれば、アプリを何個でも追加できます。

### 3. Web上に公開する

編集したHTMLを、HTTPSに対応したWebサーバーへアップロードします。

ファイル名を`index.html`にすると、フォルダのURLだけで開けます。

### 4. ホーム画面に追加する

公開したページをスマホで開きます。

- **iPhone**：Safariの共有ボタン →「ホーム画面に追加」
- **Android**：Chromeのメニュー →「ホーム画面に追加」または「アプリをインストール」

ホーム画面から起動した場合、画面下の追加案内は自動で非表示になります。

## ホーム画面アイコンについて

次のURLを、自分の正方形画像へ変更してください。

```html
<meta name="ojapp:icon" content="https://example.com/icon.png">
```

画像は **512×512pxのPNG** を推奨します。

## OJapp Free

このテンプレートは、次の1行でページをホーム画面アプリ化しています。

```html
<script src="https://ojapp.app/js/ojapp.js"></script>
```

[OJapp Freeについて](https://ojapp.app/one-page-one-app)
