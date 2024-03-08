# NCV-WebStyleViewer-assets
ニコニコ生放送専用コメントビューア [NCV (NiconamaCommentViewer)](https://www.posite-c.com/application/ncv/) で WebStyleViewer 利用時に適用されるHTML素材です。
## 構成
[assets](./assets) 以下、各設定をディレクトリ単位で管理します。  
[default](./assets/default) ディレクトリは初期設定であり実行ファイルとともに配布されてインストールフォルダ内に配置されます。  
独自のカスタム設定を使用する場合は、アプリケーション設定保存フォルダ ( `%APPDATA%\posite-c\NiconamaCommentViewer` ) 内に **WebStyleAssets** フォルダを作成し、その中で各設定ごとにフォルダ分けします。  
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
2. NCVを起動して「オプション... → 全般 → WebStyleViewerで適用するhtml設定」から任意の設定を選択する[^1]
3. 「ツール → WebStyleViewer」を起動する[^2]
[^1]: 設定フォルダ一覧が取得されるのはオプションウィンドウを開いたときなのでNCVは起動したまま作業しても大丈夫です。
[^2]: 番組に接続する度にその時点で選択されている設定が読み込まれるので WebStyleViewer を開いたままでも次の接続時に新しい設定が読み込まれます。
## カスタム設定作成規則
最低限 **index.html** と **comment.html** は必須です。
### index.html
NCVで WebStyleViewer を開いたときや番組切り替えの度に読み込まれます。  
外部へのアクセスはデフォルトではできないようにしてあります。（手動で設定ファイルを追加することでできるようにしてもいいかも）  
以下は最小限の構成です。
```html
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>
<div id="comment-panel"></div>
<script language="javascript" type="text/javascript">
function addNewComment(comment) {
  document.getElementById('comment-panel').insertAdjacentHTML('beforeend', comment);
}
</script>
</body>
</html>
```

`addNewComment()` はコメント追加用の関数で、新規コメント受信の度にNCV本体から呼ばれるため必須です。中身の処理は行いたい演出に応じて自由に書き換え可能です。  
引数は comment.html に記載されたテンプレートタグにコメントデータを反映させたhtmlタグです。  
```javascript
function addNewComment(comment) {
  document.getElementById('comment-panel').insertAdjacentHTML('beforeend', comment);
}
```
#### ユーザーアイコンを表示する方法
ユーザーアイコンを表示するにはNCV本体でのユーザーアイコン表示が有効になっている必要があります。  
そのうえで取得された画像がBase64エンコードされ、文字列として WebStyleViewer に渡されます。  
画面上への描画はコメント受信の際にJavaScriptなどで処理して行なってください。  
手短にまとめると、
 * NCV本体でユーザーアイコン表示を有効にする
 * `needsUserIcon()` で `true` を返す
 * 画像データ蓄積用のオブジェクトを用意して `addUserIcon()` でデータ追加処理を書く

`needsUserIcon()` はユーザーアイコンを表示したい場合に必須となります。 index.html が読み込まれたタイミングでNCV本体から確認が行われ、画像データを渡すか決定されます。  
ユーザーアイコンが必要な場合は `true` を返してください。必要ない場合は `false` を返すか丸ごと消してしまっても大丈夫です。  
```javascript
function needsUserIcon() {
  return true;
}
```
また、`addUserIcon()` はユーザーアイコン画像の追加関数であり、新規取得した画像データをNCV本体から渡すため必須となります。  
デフォルトではMapオブジェクトへ蓄積していき、新規登録時の適用処理も行なっています。  
画像の蓄積方法や関数内の処理などは自由に書き換えてください。  
第一引数はユーザーID、第二引数はBase64エンコードされたアイコン画像です。  
```javascript
var userIconMap = new Map();
function addUserIcon(userId, image) {
  userIconMap.set(userId, image);
  // 画像取得完了よりコメント反映の方が早いので初回登録時は少し遡って適用する
  let chatElm = commentPanel.getElementsByClassName('chat');
  const len = Math.max(chatElm.length - 50,0);
  for(let i = chatElm.length - 1; i >= len; i--) {
    if (chatElm[i].dataset.anon === "1") { continue; }
    if (userId !== chatElm[i].getElementsByClassName('chat-userid')[0].textContent) { continue; }
    const iconElm = chatElm[i].getElementsByClassName('chat-usericon');
    if (iconElm.length === 0) { break; }
    const elm = document.createElement('img');
    elm.src = 'data:image/jpeg;base64,' + image;
    iconElm[0].appendChild(elm);
  }
}
```
以後新規取得コメントへはユーザーIDを判別したのち適宜ユーザーアイコンの表示を行なってください。
### comment.html
コメントが受信される度に **index.html** に送られる、コメント一つ分のテンプレートタグです。  
WebStyleViewer を開いたときや番組切り替え時、初期化のタイミングでファイルが読み込まれて `CR` および `LF` が削除されて使われます。  
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
|&USERID|ユーザーID|
|&NICKNAME|ユーザー設定ニックネーム|
|&COMMENT|コメント本文|
## ライセンス
このリポジトリに含まれる各ファイルは、CC BY-NC-SA 4.0 ライセンスに基づいてライセンスされています。  
詳細については、[LICENSE](./LICENSE) を参照してください。
