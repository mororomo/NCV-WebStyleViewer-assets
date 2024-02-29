# NCV-WebStyleViewer-assets
[NCV (NiconamaCommentViewer)](https://www.posite-c.com/application/ncv/) で WebStyleViewer 利用時に適用されるHTML素材です。
## 構成
* [assets](./assets) 以下、各設定をディレクトリ単位で管理します。
* [default](./assets/default) ディレクトリは初期設定であり実行ファイルとともに配布されてインストールフォルダ内に配置されます。
* 独自のカスタム設定を自作する場合は、アプリケーション設定保存フォルダ内に `WebStyleAssets` フォルダを作成し、その中で各設定ごとにフォルダ分けします。
## カスタム設定作成方法
`index.html` と `comment.html` は必須ファイルです。
### `index.html`
* NCVで ***WebStyleViewer*** を開いたときに ***WebView2*** 上に読み込まれるhtmlページです。
* `addNewComment` 関数はコメント追加に使用されるため必須です。
* 関数の中身は行いたい演出に応じて自由に書き換えてください。
```
function addNewComment(comment) {
  document.getElementById('comment-panel').insertAdjacentHTML('beforeend', comment);
}
```
* いろいろ解説
### `comment.html`
* コメントが受信される度に `index.html` に送られるコメント一つ分のhtmlタグです。
* このファイルの中身の指定項目がReplaceされたのち丸々 `index.html` へ送られるため、余分なタグやコメント等は記載しないでください。
* いろいろ解説
