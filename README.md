# NCV-WebStyleViewer-assets
ニコニコ生放送専用コメントビューア [NCV (NiconamaCommentViewer)](https://www.posite-c.com/application/ncv/) で WebStyleViewer 利用時に適用されるHTML素材です。
## 構成
[assets](./assets) 以下、各設定をディレクトリ単位で管理します。  
[default](./assets/default) ディレクトリは初期設定であり実行ファイルとともに配布されてインストールフォルダ内に配置されます。  
独自のカスタム設定を自作する場合は、アプリケーション設定保存フォルダ内に **WebStyleAssets** フォルダを作成し、その中で各設定ごとにフォルダ分けします。  
> posite-c  
> ├── NiconamaCommentViewer  
> 　　 ├── CommentLog  
> 　　 ├── SpeechLexicon  
> 　　 └── WebStyleAssets  
> 　　　　　├── Sample01  
> 　　　　　├── Sample02  
> 　　　　　└── Sample03

## カスタム設定作成規則
**index.html** と **comment.html** は必須ファイルです。
### index.html
NCVで ***WebStyleViewer*** を開いたときに **WebView2** 上に読み込まれる **html** です。  
`addNewComment` 関数はコメント受信の度にNCV本体から呼ばれるため必須です。関数の中身は行いたい演出に応じて自由に書き換え可能です。  
```
function addNewComment(comment) {
  document.getElementById('comment-panel').insertAdjacentHTML('beforeend', comment);
}
```
それ以外はすべて自由です。  
外部への接続はデフォルトではできないようにしてあります。（手動で設定ファイルを追加することでできるようにしてもいいかも）  
### comment.html
コメントが受信される度に **index.html** に送られる、コメント一つ分のhtmlタグです。  
ファイル内の指定項目がReplaceされたのち丸々 **index.html** へ送られるため、余分なタグやコメント等は記載しないでください。  
#### コメント外枠タグ属性置換用変数
|変数|replace後の値|説明|
----|----|----
|&ANONYMITY|␣data-anon="1"|匿名IDコメント（なふだオフ）|
|&PREMIUM|␣data-premium="1"|プレミアムアカウント|
|&SERVERCOMMENT|␣data-server="1"|運営コメント|
|&EMOTION|␣data-emotion="1"|エモーション|

上記の情報はカスタムデータ属性として変換されるので、JavaScriptで処理して描画に反映させてください。  
各データ存在しない場合は空となります。 `="0"` とはなりません。  

#### コメントデータ置換用変数
|変数|説明|
----|----
|&NUMBER|コメント番号|
|&COMMENT|コメント本文|
|&NICKNAME|ユーザー設定ニックネーム|
|&USERID|ユーザーID|
