# NCV-WebStyleViewer-assets
ニコニコ生放送専用コメントビューア [NCV (NiconamaCommentViewer)](https://www.posite-c.com/application/ncv/) で WebStyleViewer 利用時に適用されるHTML素材です。
## 構成
[assets](./assets) 以下、各設定をディレクトリ単位で管理します。  
[default](./assets/default) ディレクトリは初期設定であり実行ファイルとともに配布されてインストールフォルダ内に配置されます。  
独自のカスタム設定を自作する場合は、アプリケーション設定保存フォルダ ( `%APPDATA%\posite-c\NiconamaCommentViewer` ) 内に **WebStyleAssets** フォルダを作成し、その中で各設定ごとにフォルダ分けします。  
> posite-c  
> └── NiconamaCommentViewer  
> 　　 ├── CommentLog  
> 　　 ├── SpeechLexicon  
> 　　 └── WebStyleAssets  
> 　　　　　├── Sample01  
> 　　　　　├── Sample02  
> 　　　　　└── Sample03

> [!NOTE]
> カスタム設定はインストールフォルダ内とアプリケーション設定保存フォルダ内のどちらでも配置可能ですが、同名の設定フォルダが存在した場合はインストールフォルダ側が優先されます。
## カスタム設定変更手順
1. 設定フォルダを用意して必要なファイルを揃える
2. NCVを起動して「オプション... → 一般設定 → WebStyleViewerで適用するhtml設定」から任意の設定を選択する[^1]
3. 「ツール → WebStyleViewer」を起動する[^2]
[^1]: 設定フォルダ一覧が取得されるのはオプションウィンドウを開いたときなのでNCVは起動したまま作業しても大丈夫です。
[^2]: 番組に接続する度にその時点で選択されている設定が読み込まれるので WebStyleViewer を開いたままでも次の接続時に新しい設定が読み込まれます。
## カスタム設定作成規則
最低限 **index.html** と **comment.html** は必須です。
### index.html
NCVで **WebStyleViewer** を開いたときに WebView2 上に読み込まれる html です。  
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
> [!CAUTION]
> ファイル内の指定項目が変換されたのち丸々 **index.html** へ送られるため、余分なタグやコメント等は記載しないでください。  
#### コメント外枠タグ属性置換用変数
|変数|変換後の値|説明|
----|----|----
|&ANONYMITY|␣data-anon="1"|匿名IDコメント（なふだオフ）|
|&PREMIUM|␣data-premium="1"|プレミアムアカウント|
|&SERVERCOMMENT|␣data-server="1"|運営コメント|
|&EMOTION|␣data-emotion="1"|エモーション|

これらの情報はカスタムデータ属性として変換されるので、JavaScript等で処理して描画に反映させてください。  
> [!NOTE]
> 各データ存在しない場合は空となります。 `="0"` とはなりません。  

#### コメントデータ置換用変数
|変数|説明|
----|----
|&NUMBER|コメント番号|
|&COMMENT|コメント本文|
|&NICKNAME|ユーザー設定ニックネーム|
|&USERID|ユーザーID|
