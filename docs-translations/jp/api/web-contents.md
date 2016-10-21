# webContents

> ウェブページのレンダリングとコントールを行います。

`webContents` は
[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)です。ウェブページのレンダリングとコントロールを担います。また、[`BrowserWindow`](browser-window.md)のプロパティです。`webContents` オブジェクトに
アクセスする例を示します。

```javascript
const {BrowserWindow} = require('electron')

let win = new BrowserWindow({width: 800, height: 1500})
win.loadURL('http://github.com')

let contents = win.webContents
console.log(contents)
```

## メソッド

`webContents` モジュールから次のメソッドにアクセスできます。

```javascript
const {webContents} = require('electron')
console.log(webContents)
```

### `webContents.getAllWebContents()`

Returns `WebContents[]` - すべての `WebContents` インスタンスの配列を返します。これにはすべてのウィンドウ、ウェブビュー、オープン中のデベロッパーツール、デベロッパーツールエクステンション、バックグラウンドページが含まれます。

### `webContents.getFocusedWebContents()`

`WebContents` - このアプリケーションでフォーカスされているウェブコンテンツを
返します。コンテンツがない場合は　`null` を返します。

### `webContents.fromId(id)`

* `id` Integer

`WebContents` - 指定したIDの WebContents インスタンスを返します

## クラス: WebContents

> BrowserWindow インスタンスのコンテンツのレンダリングとコントールを行います。

### インスタンスイベント

#### イベント: 'did-finish-load'

ナビゲーションが終了した、すなわち、スピナーの回転が止まり、`onload` イベントがディスパッチされた時に出力されます。

#### イベント: 'did-fail-load'

返り値:

* `event` Event
* `errorCode` Integer
* `errorDescription` String
* `validatedURL` String
* `isMainFrame` Boolean

このイベントは `did-finish-load` と同じですが、ロードが失敗、または、たとえば、
`window.stop()` の呼び出しでキャンセルされた時に出力されます。エラーコードとその意味の
完全なリストは[ここ](https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h)にあります。
リダイレクトレスポンスは `errorCode` に"-3"を出力します。このエラーは明示的に無視したい
かもしれませんので注意してください。

#### イベント: 'did-frame-finish-load'

返り値:

* `event` Event
* `isMainFrame` Boolean

フレームがナビゲーションを終了した時に出力します。

#### イベント: 'did-start-loading'

タブのスピナが回転を開始した時点に相当します。

#### イベント: 'did-stop-loading'

タブのスピナが回転を停止した時点に相当します。

#### イベント: 'did-get-response-details'

返り値:

* `event` Event
* `status` Boolean
* `newURL` String
* `originalURL` String
* `httpResponseCode` Integer
* `requestMethod` String
* `referrer` String
* `headers` Object
* `resourceType` String

リクエストしたリソースに関する詳細が利用可能になった時に出力されます。`status` はリソースを
ダウンロードするためのソケットコネクションを示します。

#### イベント: 'did-get-redirect-request'

返り値:

* `event` Event
* `oldURL` String
* `newURL` String
* `isMainFrame` Boolean
* `httpResponseCode` Integer
* `requestMethod` String
* `referrer` String
* `headers` Object

リソースをリクエストしたがリダイレクトを受け取った時に出力されます。

#### イベント: 'dom-ready'

返り値:

* `event` Event

指定したフレームにドキュメントがロードされた時に出力されます。

#### イベント: 'page-favicon-updated'

返り値:

* `event` Event
* `favicons` String[] - URLの配列

ページがファビコンのURLを受け取った時に出力されます。

#### イベント: 'new-window'

返り値:

* `event` Event
* `url` String
* `frameName` String
* `disposition` String - 値は、`default`, `foreground-tab`, `background-tab`,
  `new-window`, `save-to-disk`, `other` のいずれかです。
* `options` Object - 新たな `BrowserWindow` の作成に使用されるオプション
  `BrowserWindow`.
* `additionalFeatures` Array - `window.open()` に与えられた非標準的な機能
  （Chromium や Electron では対応のない機能）

ページが `url` を新たなウィンドウで開くようリクエストした時に出力されます。リクエストは
`window.open` による場合もあれば、`<a target='_blank'>` のような外部リンクの場合も
あります。

デフォルトでは、`url`のための新たな `BrowserWindow` が作成されます。

`event.preventDefault()` を呼び出すと新たなウィンドウの作成を防ぎます。
そのような場合、`BrowserWindow` インスタンスへの参照を `event.newGuest` に設定
するとElectronのランタイムで使用できます。

#### イベント: 'will-navigate'

返り値:

* `event` Event
* `url` String

ユーザまたはページがナビゲーションを開始しようとした時に出力されます。`window.location`
オブジェクトが変更されたとき、またはユーザがページ内のリンクをクリックしたときに
起こりえます。

このイベントはナビゲーションが `webContents.loadURL` や `webContents.back` のような
APIでプログラムから開始されたときは出力されません。

また、アンカーリンクのクリックや window.location.hash` の更新などの、ページ内ナビゲーションでも出力されません。この目的には`did-navigate-in-page` イベントを使用してください。

`event.preventDefault()` を呼び出すとナビゲーションを防ぎます。

#### イベント: 'did-navigate'

返り値:

* `event` Event
* `url` String

ナビゲーションが終了した時に出力されます。

このイベントは、アンカーリンクのクリックや window.location.hash` の更新などの、ページ内ナビゲーションでも出力されません。この目的には`did-navigate-in-page` イベントを使用してください。

#### イベント: 'did-navigate-in-page'

返り値:

* `event` Event
* `url` String
* `isMainFrame` Boolean

ページ内ナビゲーションが生じた時に出力されます。

ページ内ナビゲーションが生じた場合、ページRULは変わりますが、ページ外部へのナビゲーションは起こしません。これが起きる例としては、アンカーリンクがクリックされた場合やDOMの `hashchange`
イベントが発火された場合です。

#### イベント: 'crashed'

返り値:

* `event` Event
* `killed` Boolean

レンダラプロセスがクラッシュした、または、強制終了させられた時に出力されます。

#### イベント: 'plugin-crashed'

返り値:

* `event` Event
* `name` String
* `version` String

プラグインプロセスがクラッシュした時に出力されます。

#### イベント: 'destroyed'

`webContents`が破棄された時に出力されます。

#### イベント: 'devtools-opened'

デベロッパーツールが開かれた時に出力されます。

#### イベント: 'devtools-closed'

デベロッパーツールが閉じられた時に出力されます。

#### イベント: 'devtools-focused'

デベロッパーツールがフォーカス/オープンされた時に出力されます。

#### イベント: 'certificate-error'

返り値:

* `event` Event
* `url` URL
* `error` String - エラーコード
* `certificate` [Certificate](structures/certificate.md)
* `callback` Function

`url` の `certificate` の検証が失敗した時に出力されます。

使用方法は、[`app` の `certificate-error` イベント](app.md#event-certificate-error)と同じです。

#### イベント: 'select-client-certificate'

返り値:

* `event` Event
* `url` URL
* `certificateList` [Certificate[]](structures/certificate.md)
* `callback` Function

クライアントの認証がリクエストされた時に出力されます。

使用方法は、[`app` の `select-client-certificate` いbねと](app.md#event-select-client-certificate)と同じです。

#### イベント: 'login'

返り値:

* `event` Event
* `request` Object
  * `method` String
  * `url` URL
  * `referrer` URL
* `authInfo` Object
  * `isProxy` Boolean
  * `scheme` String
  * `host` String
  * `port` Integer
  * `realm` String
* `callback` Function

`webContents`がBasic認証を求めた時に出力されます。

使用方法は、[`app` の `login` イベント](app.md#event-login)と同じです。

#### イベント: 'found-in-page'

返り値:

* `event` Event
* `result` Object
  * `requestId` Integer
  * `activeMatchOrdinal` Integer - アクティブマッチの位置
  * `matches` Integer - マッチの数
  * `selectionArea` Object - 最初にマッチした領域の座標

[`webContents.findInPage`] リクエストの結果が得られた時に出力されます。

#### イベント: 'media-started-playing'

メディアが演奏を開始した時に出力されます。

#### イベント: 'media-paused'

メディアが演奏を停止した時、または、演奏が終了した時に出力されます。

#### イベント: 'did-change-theme-color'

ページのテーマカラーが変更された時に出力されます。これは通常、次のメタタグに遭遇した場合です。

```html
<meta name='theme-color' content='#ff0000'>
```

#### イベント: 'update-target-url'

返り値:

* `event` Event
* `url` String

マウスがリンクの上に移動した時、または、キーボードがリンクにフォーカスを移動した時に
出力されます。

#### イベント: 'cursor-changed'

返り値:

* `event` Event
* `type` String
* `image` NativeImage (オプション)
* `scale` Float (オプション) - カスタムカーソルのスケーリングファクター
* `size` Object (オプション) - `image` のサイズ
  * `width` Integer
  * `height` Integer
* `hotspot` Object (オプション) - カスタムカーソルのホットスポットの座標
  * `x` Integer - x 座標
  * `y` Integer - y 座標

カーソルのタイプが変わった時に出力されます。`type` パラメタは、`default`,
`crosshair`, `pointer`, `text`, `wait`, `help`, `e-resize`, `n-resize`,
`ne-resize`, `nw-resize`, `s-resize`, `se-resize`, `sw-resize`, `w-resize`,
`ns-resize`, `ew-resize`, `nesw-resize`, `nwse-resize`, `col-resize`,
`row-resize`, `m-panning`, `e-panning`, `n-panning`, `ne-panning`, `nw-panning`,
`s-panning`, `se-panning`, `sw-panning`, `w-panning`, `move`, `vertical-text`,
`cell`, `context-menu`, `alias`, `progress`, `nodrop`, `copy`, `none`,
`not-allowed`, `zoom-in`, `zoom-out`, `grab`, `grabbing`, `custom` のいずれかです。

`type` パラメタが `custom` の場合、`image` パラメタは `NativeImage` 内のカスタム
カーソルイメージを保持し、`scale`, `size`, `hotspot` はカスタムカーソルに関する
補足情報を保持します。

#### イベント: 'context-menu'

返り値:

* `event` Event
* `params` Object
  * `x` Integer - x 座標
  * `y` Integer - y 座標
  * `linkURL` String - コンテキストメニューが起動されたノードを含むリンクのURL
  * `linkText` String - リンクに付随するテキスト。リンクのコンテンツが画像の場合は、
    空文字列の場合もあります
  * `pageURL` String - コンテキストメニューが起動された最上位のページのURL
  * `frameURL` String - コンテキストメニューが起動されたサブフレームのURL
  * `srcURL` String - コンテキストメニューが起動された要素のソースURL。ソース
    URLを持つ要素は画像、オーディオ、ビデオのいずれかです。
  * `mediaType` String - コンテキストメニューが起動されたノードのタイプ。
    `none`, `image`, `audio`, `video`, `canvas`, `file`, `plugin`
    のいずれかです。
  * `hasImageContents` Boolean - 空でないコンテンツを持つ画像上でコンテキスト
    メニューが起動されたか否か
  * `isEditable` Boolean - コンテキストが編集可能であるか否か
  * `selectionText` String - コンテキストメニューが起動されたセレクションの
    テキスト
  * `titleText` String - コンテキストメニューが起動されたセレクションの
    タイトルまたは代替テキスト
  * `misspelledWord` String - もしあれば、カーソル位置のミススペルの単語
  * `frameCharset` String - メニューが起動されたフレームの文字エンコーディング
  * `inputFieldType` String - コンテキストメニューが入力フィールで起動された場合、
    そのフィールドのタイプ。`none`, `plainText`, `password`, `other`
    のいずれかです。
  * `menuSourceType` String - コンテキストメニューが起動されたインプットソース。
    `none`, `mouse`, `keyboard`, `touch`, `touchMenu` のいずれかです。
  * `mediaFlags` Object - コンテキストメニューが起動されたメディア要素のフラグ。
    詳細は以下を参照してください。
  * `editFlags` Object - これらのフラグは、レンダラが対応するアクションを実行可能だと
    信じているいるか否かを示します。詳細は以下を参照してください。

`mediaFlags` は、次のプロパティを持つオブジェクトです。

* `inError` Boolean - メディア要素はクラッシュしたか否か
* `isPaused` Boolean - メディア要素は停止されたか否か
* `isMuted` Boolean - メディア要素はミュートされたか否か
* `hasAudio` Boolean - メディア要素はオーディオを持つか否か
* `isLooping` Boolean - メディア要素はループ中か否か
* `isControlsVisible` Boolean - メディア要素のコントールは可視か否か
* `canToggleControls` Boolean - メディア要素のコントールはトグル可能か否か
* `canRotate` Boolean - メディア要素のコントールは回転可能か否か

`editFlags` は、次のプロパティを持つオブジェクトです。

* `canUndo` Boolean - レンダラは取り消し（Undo）が可能と信じているか否か
* `canRedo` Boolean - レンダラはやり直し（Redo）が可能と信じているか否か
* `canCut` Boolean - レンダラはカットが可能と信じているか否か
* `canCopy` Boolean - レンダラはコピーが可能と信じているか否か
* `canPaste` Boolean - レンダラはペーストが可能と信じているか否か
* `canDelete` Boolean - レンダラは削除が可能と信じているか否か
* `canSelectAll` Boolean - レンダラはすべて選択が可能と信じているか否か

処理する必要がある新たなコンテキストメニューが存在する時に出力されます。

#### イベント: 'select-bluetooth-device'

返り値:

* `event` Event
* `devices` [Objects]
  * `deviceName` String
  * `deviceId` String
* `callback` Function
  * `deviceId` String

`navigator.bluetooth.requestDevice` の呼び出しによりブルートゥースデバイスの
選択が必要な時に呼び出されます。`navigator.bluetooth` APIを使うためには、`webBluetooth`
が有効でなければなりません。`event.preventDefault` が呼ばれない場合は、利用可能な最初の
デバイスが選択されます。`callback` は選択された `deviceId` を引数に呼び出さなければ
なりません。`callback` に空文字列が渡すとリクエストがキャンセルされます。

```javascript
const {app, webContents} = require('electron')
app.commandLine.appendSwitch('enable-web-bluetooth')

app.on('ready', () => {
  webContents.on('select-bluetooth-device', (event, deviceList, callback) => {
    event.preventDefault()
    let result = deviceList.find((device) => {
      return device.deviceName === 'test'
    })
    if (!result) {
      callback('')
    } else {
      callback(result.deviceId)
    }
  })
})
```

#### イベント: 'paint'

返り値:

* `event` Event
* `dirtyRect` [Rectangle](structures/rectangle.md)
* `image` [NativeImage](native-image.md) - フレーム全体の画像データ

新たなフレームが生成された時に出力されます。`dirtyRect` 領域のみがバッファに
渡されます。

```javascript
const {BrowserWindow} = require('electron')

let win = new BrowserWindow({webPreferences: {offscreen: true}})
win.webContents.on('paint', (event, dirty, image) => {
  // updateBitmap(dirty, image.getBitmap())
})
win.loadURL('http://github.com')
```

### インスタンスメソッド

#### `contents.loadURL(url[, options])`

* `url` URL
* `options` Object (オプション)
  * `httpReferrer` String - HTTPリファラURL
  * `userAgent` String - リクエストを発行したユーザエージェント
  * `extraHeaders` String - "\n"区切りの追加ヘッダー

ウィンドウに `url` をロードします。`url` は、`http://` や `file://` などの
プロトコル接頭辞を含んでいなければなりません。httpキャッシュを避けてロードしたい
場合は、それを行うための `pragma` ヘッダーを使用してください。

```javascript
const {webContents} = require('electron')
const options = {extraHeaders: 'pragma: no-cache\n'}
webContents.loadURL('https://github.com', options)
```

#### `contents.downloadURL(url)`

* `url` String

ナビゲーションなしに `url` のリソースのダウンロードを開始します。`session` の
`will-download` イベントが発火されます。

#### `contents.getURL()`

`String` - カレントウェブページのURLを返します。

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('http://github.com')

let currentURL = win.webContents.getURL()
console.log(currentURL)
```

#### `contents.getTitle()`

`String` - カレントウェブページのタイトルを返します

#### `contents.isDestroyed()`

`Boolean` - ウェブページが廃棄されたか否かを返します。

#### `contents.isFocused()`

`Boolean` - ウェブページがフォーカスされたか否かを返します。

#### `contents.isLoading()`

Returns `Boolean` - ウェブページがリソースをローディング中であるか否かを返します。

#### `contents.isLoadingMainFrame()`

`Boolean` - 主たるフレーム（とその中のiframeやフレームも）がリソースをローディング中であるか否かを返します。

#### `contents.isWaitingForResponse()`

`Boolean` - ウェブページがページの主たるリソースからの最初のレスポンスの待機中であるか
否かを返します。

#### `contents.stop()`

すべての保留中のナビゲーションを中止します。

#### `contents.reload()`

カレントウェブページをリロードします。

#### `contents.reloadIgnoringCache()`

カレントウェブページをリロードし、キャッシュを無視します。

#### `contents.canGoBack()`

Returns `Boolean` - ブラウザが前のウエブページに戻れるか否かを返します。

#### `contents.canGoForward()`

Returns `Boolean` - ブラウザが次のウエブページに進めるか否かを返します。

#### `contents.canGoToOffset(offset)`

* `offset` Integer

Returns `Boolean` - ブラウザが `offset` に行けるか否かを返します。

#### `contents.clearHistory()`

ナビゲーション履歴をクリアします。

#### `contents.goBack()`

ブラウザを1つ前のウェブページに戻します。

#### `contents.goForward()`

ブラウザを1つ先のウェブページに戻します。

#### `contents.goToIndex(index)`

* `index` Integer

ブラウザを指定したインデックスのウェブページに移動します。

#### `contents.goToOffset(offset)`

* `offset` Integer

ブラウザを「現在のエントリ」から指定したオフセットのページに移動します。

#### `contents.isCrashed()`

`Boolean` - レンダラプロセスがクラッシュしたか否かを返します。

#### `contents.setUserAgent(userAgent)`

* `userAgent` String

このウェブページのユーザエージェントを上書きします。

#### `contents.getUserAgent()`

`String` - このウェブページのユーザエージェントを返します。

#### `contents.insertCSS(css)`

* `css` String

カレントウェブページにCSSを注入します。

#### `contents.executeJavaScript(code[, userGesture, callback])`

* `code` String
* `userGesture` Boolean (オプション)
* `callback` Function (オプション) - スクリプトの実行後に呼び出されます
  * `result`

ページの中で `code` を評価します。

ブラウザウィンドウでは、HTML APIの中には `requestFullScreen` のようにユーザの操作で
しか起動できないものがあります。`userGesture` に `true` を設定すると、この制限が
なくなります。

#### `contents.setAudioMuted(muted)`

* `muted` Boolean

カレントウェブページのオーディをミュートします。

#### `contents.isAudioMuted()`

`Boolean` - このページがミュートされているか否かを返します。

#### `contents.setZoomFactor(factor)`

* `factor` Number - ズーム係数

ズーム係数を指定した値に変更します。ズーム係数はズーム率を100で割った数です。たとえば、
300% = 3.0 です。

#### `contents.getZoomFactor(callback)`

* `callback` Function

現在のズーム係数を得るためのリクエストを送信します。`callback` は
`callback(zoomFactor)` として呼び出されます。

#### `contents.setZoomLevel(level)`

* `level` Number - ズームレベル

ズームレベルを指定したレベルに変更します。オリジナルサイズを0として、プラスマイナス1は
各々オリジナルサイズの300%と50%というデフォルトの限界サイズまで、20%大きく、または小さく
ズームすることを表します。

#### `contents.getZoomLevel(callback)`

* `callback` Function

現在のズームレベルを得るためのリクエストを送信します。`callback` は
`callback(zoomLevel)` として呼び出されます。

#### `contents.setZoomLevelLimits(minimumLevel, maximumLevel)`

* `minimumLevel` Number
* `maximumLevel` Number

ズームレベルの最大値と最小値を設定します。

#### `contents.undo()`

ウェブページで編集コマンド `undo` を実行します。

#### `contents.redo()`

ウェブページで編集コマンド `redo` を実行します。

#### `contents.cut()`

ウェブページで編集コマンド `cut` を実行します。

#### `contents.copy()`

ウェブページで編集コマンド `copy` を実行します。

#### `contents.copyImageAt(x, y)`

* `x` Integer
* `y` Integer

指定した位置にある画像をクリップボードにコピーします。

#### `contents.paste()`

ウェブページで編集コマンド `paste` を実行します。

#### `contents.pasteAndMatchStyle()`

ウェブページで編集コマンド `pasteAndMatchStyle` を実行します。

#### `contents.delete()`

Eウェブページで編集コマンド `delete` を実行します。

#### `contents.selectAll()`

ウェブページで編集コマンド `selectAll` を実行します。

#### `contents.unselect()`

ウェブページで編集コマンド `unselect` を実行します。

#### `contents.replace(text)`

* `text` String

ウェブページで編集コマンド `replace` を実行します。

#### `contents.replaceMisspelling(text)`

* `text` String

ウェブページで編集コマンド `replaceMisspelling` を実行します。

#### `contents.insertText(text)`

* `text` String

フォーカスされた要素に `text` を挿入します。

#### `contents.findInPage(text[, options])`

* `text` String - 検索する内容。空ではいけません。
* `options` Object (オプション)
  * `forward` Boolean - 前方に検索するか、後方に検索するか。デフォルトは `true`。
  * `findNext` Boolean - 操作は最初のリクエストか、2回目以降か。デフォルトは `false`。
  * `matchCase` Boolean - 検索は大文字小文字を区別するか否か。デフォルトは `false`。
  * `wordStart` Boolean - ワードの先頭部分だけを検索するか否か。デフォルトは `false`。
  * `medialCapitalAsWordStart` Boolean - When combined with `wordStart` と共に
    指定された場合、マッチが大文字で始まり小文字または文字以外が続く場合、ワードの中間一致を
    認めるか否か。その他のワード内マッチを認める。デフォルトは `false`。

ウェブページ内で `text` にマッチするすべてを検索するリクエストを開始し、リクエストに使用されたリクエストIDを表す `Integer` を返します。リクエストの結果は、[`found-in-page`](web-contents.md#event-found-in-page) イベントを
購読することにより得られます。

#### `contents.stopFindInPage(action)`

* `action` String - [`webContents.findInPage`] リクエストの終了時に行うアクションを指定します
  * `clearSelection` - 選択をクリアします。
  * `keepSelection` - 選択を通常の選択に変換します。
  * `activateSelection` - 選択肢あノードをフォーカスし、クリックします。

`webContents`への `findInPage` リクエストを `action` を指定して、中止します。

```javascript
const {webContents} = require('electron')
webContents.on('found-in-page', (event, result) => {
  if (result.finalUpdate) webContents.stopFindInPage('clearSelection')
})

const requestId = webContents.findInPage('api')
console.log(requestId)
```

#### `contents.capturePage([rect, ]callback)`

* `rect` [Rectangle](structures/rectangle.md) (オプション) - キャプチャするページの領域。
* `callback` Function

ページの `rect` 内のスナップショットをキャプチャします。完了時に、`callback` が `callback(image)`
として呼び出されます。ここで `image` はスナップショットのデータを格納する[NativeImage](native-image.md) の
インスタンスです。`rect` を指定しないと、ページの可視領域全体をキャプチャします。

#### `contents.hasServiceWorker(callback)`

* `callback` Function

任意のServiceWorker　`callback` が登録されているかチェックし、レスポンスとして
論理値を返します。

#### `contents.unregisterServiceWorker(callback)`

* `callback` Function

任意のServiceWorker `callback` の登録を解除し、レスポンスとして論理値、JS Promiseが
満たされた場合は `true`、棄却された場合は `false` を返します。

#### `contents.print([options])`

* `options` Object (オプション)
  * `silent` Boolean - 印刷設定に関してユーザに尋ねません。デフォルトは `false`。
  * `printBackground` Boolean - ウェブページの背景色と背景画像も印刷します。
    デフォルトは `false`。

ウィンドウのウェブページを印刷します。`silent` に `true` を指定すると、Electronは
システムのデフォルトプリンタとデフォルトの印刷設定を使用します。

ウェブページでの `window.print()` の呼び出しは、`webContents.print({silent: false, printBackground: false})` の呼び出しに等しいです。

常に新たなページに印刷したい場合は、CSSスタイルのe `page-break-before: always; ` を使用してください。

#### `contents.printToPDF(options, callback)`

* `options` Object
  * `marginsType` Integer - 使用するマージンのタイプを指定します。デフォルト
    マージンには `0`を、マージンなしには `1` を、最小マージンには `2` を指定して
    ください。
  * `pageSize` String - 生成されるPDFのページサイズを指定します。`A3`, `A4`, `A5`,
    `Legal`, `Letter`, `Tabloid`、 ミクロン単位の `height` と `width` を含むオブジェクトのいずれかを指定できます。
  * `printBackground` Boolean - CSSの背景を印刷するか否か。
  * `printSelectionOnly` Boolean - 選択部分のみを印刷するか否か。
  * `landscape` Boolean - 横向きは `true`、 縦向きは `false`。
* `callback` Function

Chromiumのプレビュー印刷カスタム設定を使って、ウィンドウのウェブページをPDFで出力します。

完了時に `callback` が `callback(error, data)` の形で呼び出されます。ここで、
`data` は生成されたPDFデータを含む `Buffer` です。

デフォルトで空の `options` は次のようにみなされます。

```javascript
{
  marginsType: 0,
  printBackground: false,
  printSelectionOnly: false,
  landscape: false
}
```

常に新たなページに印刷したい場合は、CSSスタイルのe `page-break-before: always; ` を使用してください。

以下は、`webContents.printToPDF` の例です。

```javascript
const {BrowserWindow} = require('electron')
const fs = require('fs')

let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('http://github.com')

win.webContents.on('did-finish-load', () => {
  // デフォルトの印刷オプションを使用する
  win.webContents.printToPDF({}, (error, data) => {
    if (error) throw error
    fs.writeFile('/tmp/print.pdf', data, (error) => {
      if (error) throw error
      console.log('Write PDF successfully.')
    })
  })
})
```

#### `contents.addWorkSpace(path)`

* `path` String

指定したパスをデベロッパーツールのワークスペースに追加します。デベロッパーツールの作成後に使用しなければ
なります。

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.webContents.on('devtools-opened', () => {
  win.webContents.addWorkSpace(__dirname)
})
```

#### `contents.removeWorkSpace(path)`

* `path` String

指定したパスをデベロッパーツールのワークスペースから取り除きます。

#### `contents.openDevTools([options])`

* `options` Object (オプション)
  * `mode` String - 指定したドック状態でデベロッパーツールを開きます。`right`, `bottom`,
  `undocked`, `detach`のいずれかを指定できます。デフォルトは最後に使用したドック状態です。
  `undocked`モードでは再ドックできますが、`detach` モードではそれができません。

デベロッパーツールを開きます。

#### `contents.closeDevTools()`

デベロッパーツールを閉じます。

#### `contents.isDevToolsOpened()`

`Boolean` - デベロッパーツールが開いているか否かを返します。

#### `contents.isDevToolsFocused()`

`Boolean` - デベロッパーツールがフォーカスされているか否かを返します。

#### `contents.toggleDevTools()`

デベロッパーツールの表示をトグルします。

#### `contents.inspectElement(x, y)`

* `x` Integer
* `y` Integer

位置 (`x`, `y`)にある要素の検証を開始します。

#### `contents.inspectServiceWorker()`

サービスワーカーコンテキストのデベロッパーツールを開きます。

#### `contents.send(channel[, arg1][, arg2][, ...])`

* `channel` String

`channel` 経由で非同期メッセージをレンダラプロセスに送信します。同時に任意の引数を
送信することもできます。引数は内部でJSONでシリアライズされます。したがって、関数チェーンや
プロトタイプチェインは含まれません。

レンダラプロセスは `ipcRenderer` モジュールで `channel`をリッスンすることにより
メッセージを処理できます。

メインプロセスからレンダラプロセスへメッセージを送信する例を示します。

```javascript
// メインプロセスで
const {app, BrowserWindow} = require('electron')
let win = null

app.on('ready', () => {
  win = new BrowserWindow({width: 800, height: 600})
  win.loadURL(`file://${__dirname}/index.html`)
  win.webContents.on('did-finish-load', () => {
    win.webContents.send('ping', 'whoooooooh!')
  })
})
```

```html
<!-- index.html -->
<html>
<body>
  <script>
    require('electron').ipcRenderer.on('ping', (event, message) => {
      console.log(message)  // 'whoooooooh!'を出力
    })
  </script>
</body>
</html>
```

#### `contents.enableDeviceEmulation(parameters)`

* `parameters` Object
  * `screenPosition` String - エミュレートするスクリーンタイプを指定します。
      (デフォルト: `desktop`)
    * `desktop` String - デスクトップスクリーンタイプ
    * `mobile` String - モバイルスクリーンタイプ
  * `screenSize` Object - エミュレートするスクリーンサイズを設定します (screenPosition == mobile)。
    * `width` Integer - エミュレートするスクリーンの幅を設定します。
    * `height` Integer - エミュレートするスクリーンの高さを設定します。
  * `viewPosition` Object - スクリーン上のビューの位置 (screenPosition == mobile)
    (デフォルト: `{x: 0, y: 0}`)
    * `x` Integer - 左上角からのX軸のオフセットを設定します。
    * `y` Integer - 左上角からのY軸のオフセットを設定します。
  * `deviceScaleFactor` Integer - デバイススケール係数を設定します（`0`は
    オリジナルデバイススケール係数をデフォルトにします) (デフォルト: `0`)
  * `viewSize` Object - エミュレートするビューサイズを設定します（空は上書きしない
    ことを意味します）。
    * `width` Integer - エミュレートするビューの幅を設定します。
    * `height` Integer - エミュレートするビューの高さを設定します。
  * `fitToView` Boolean - 利用可能なスペースに合わせる必要がある場合、エミュレートする
    ビューを縮小するべきか否か(デフォルト: `false`)
  * `offset` Object - （ビューモードに合わない）利用可能なスペース内におけるエミュレートするビューのオフセット (デフォルト: `{x: 0, y: 0}`)
    * `x` Float - 左上角からのX軸のオフセットを設定します。
    * `y` Float - 左上角からのY軸のオフセットを設定します。
  * `scale` Float - ビューモードに合わない）利用可能なスペース内におけるエミュレートするビューのスケール (デフォルト: `1`)

指定したパラメータによるデバイスエミュレーションを有効にします。

#### `contents.disableDeviceEmulation()`

`webContents.enableDeviceEmulation` により有効にしたデバイスエミュレーションを
無効にします。

#### `contents.sendInputEvent(event)`

* `event` Object
  * `type` String (**必須**) - イベントのタイプ。`mouseDown`,
    `mouseUp`, `mouseEnter`, `mouseLeave`, `contextMenu`, `mouseWheel`,
    `mouseMove`, `keyDown`, `keyUp`, `char` のいずれかを指定できます。
  * `modifiers` String[] - イベントの修飾キーの配列。`shift`, `control`,
    `alt`, `meta`, `isKeypad`, `isAutoRepeat`,
    `leftButtonDown`, `middleButtonDown`, `rightButtonDown`, `capsLock`,
    `numLock`, `left`, `right` を含めることができます。

ページに入力t `event` を送ります。

キーボードイベントについては、`event` オブジェクトは次のプロパティも持ちます。

* `keyCode` String (**必須**) - キーボードイベントして送られる文字。
  [Accelerator](accelerator.md)で有効なキーコードのみ使用するべきです。

マウスイベントについては、`event` オブジェクトは次のプロパティも持ちます。

* `x` Integer (**必須**)
* `y` Integer (**必須**)
* `button` String - 押されたボタン。`left`, `middle`, `right` のいずれかを指定できます。
* `globalX` Integer
* `globalY` Integer
* `movementX` Integer
* `movementY` Integer
* `clickCount` Integer

`mouseWheel` イベントについては、`event` オブジェクトは次のプロパティも持ちます。

* `deltaX` Integer
* `deltaY` Integer
* `wheelTicksX` Integer
* `wheelTicksY` Integer
* `accelerationRatioX` Integer
* `accelerationRatioY` Integer
* `hasPreciseScrollingDeltas` Boolean
* `canScroll` Boolean

#### `contents.beginFrameSubscription([onlyDirty ,]callback)`

* `onlyDirty` Boolean (オプション) - デフォルトは `false`。
* `callback` Function

presentationイベントを購読してフレームのキャプ者を開始します。`callback` は
presentationイベントがあった時に `callback(frameBuffer, dirtyRect)` の形で
呼ばれます。

ここで、The `frameBuffer` は生のピクセルデータを含む `Buffer` です。ほとんどの
マシンではピクセルデータは32ビットのBGRAフォーマットで効率的に格納されます。しかし、
実際の表現はプロセッサのエンディアンに依存します（ほとんどのモダンプロセッサは
リトルエンディアンです。ビッグエンディアンのマシンでは、データは32ビットのARGBフォーマット
です）。

`dirtyRect` は、ページのどの部分が再描画されたかを記述するオブジェクトで、プロパティ `x, y, width, height` を持ちます。 `onlyDirty` に `true` を設定すると、`frameBuffer` は
再描画領域のみ含みます。`onlyDirty` のデフォルトは `false` です。

#### `contents.endFrameSubscription()`

フレームpresentationイベントの購読を終了します。

#### `contents.startDrag(item)`

* `item` object
  * `file` String
  * `icon` [NativeImage](native-image.md)

現在のドラッグ&amp;ドロップ操作のドラッグアイテムとして `item` を設定ます。`file`は、
ドラッグされるフィルの絶対パスです。`icon` は、ドラッグ中にカーソル下に表示する画像です。

#### `contents.savePage(fullPath, saveType, callback)`

* `fullPath` String - ファイルの絶対パス。
* `saveType` String - 保存タイプを指定します。
  * `HTMLOnly` - ページのHTMLのみ保存します。
  * `HTMLComplete` - HTMLページ全体を保存します。
  * `MHTML` - HTML全体ページをMHTMLとして保存します。
* `callback` Function - `(error) => {}`.
  * `error` Error

ページの保存プロセスが首尾よく開始された場合、`true` を返します。

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()

win.loadURL('https://github.com')

win.webContents.on('did-finish-load', () => {
  win.webContents.savePage('/tmp/test.html', 'HTMLComplete', (error) => {
    if (!error) console.log('Save page successfully')
  })
})
```

#### `contents.showDefinitionForSelection()` _macOS_

選択されたワードでページ内を検索するポップアップ辞書を表示します。

#### `contents.isOffscreen()`

`Boolean` - *オフスクリーンレンダリング* が有効になっているか否かを示します。

#### `contents.startPainting()`

*オフスクリーンレンダリング* が有効になっているが描画していない場合、描画を開始します。

#### `contents.stopPainting()`

If *オフスクリーンレンダリング* が有効になっており、描画中の場合、描画を中止します。

#### `contents.isPainting()`

`Boolean` - *オフスクリーンレンダリング* が有効になっている場合、現在描画中か
否かを返します。

#### `contents.setFrameRate(fps)`

* `fps` Integer

*オフスクリーンレンダリング* が有効になっている場合、フレームレートを指定した値に設定します。
`1` から `60` までの値のみ受け付けます。

#### `contents.getFrameRate()`

`Integer` - *オフスクリーンレンダリング* が有効になっている場合、現在の
フレームレートを返します。

#### `contents.invalidate()`

*オフスクリーンレンダリング* が有効になっている場合、フレームを破棄して、`'paint'`
イベントを通じて新たなフレームを生成します。

### インスタンスプロパティ

#### `contents.id`

この WebContents のユニークなIDを表す整数です。

#### `contents.session`

この WebContents で使用されているセッションオブジェクト ([session](session.md)) です。

#### `contents.hostWebContents`

この `WebContents` を所有しているかもしれない `WebContents`です。

#### `contents.devToolsWebContents`

この `WebContents` のためのデベロッパーツール の `WebContents` です。

**注:** ユーザはこのオブジェクトをけっして保管するべきではありまあせん。この
オブジェクトはデベロッパーツールが閉じられると `null` になるからです。

#### `contents.debugger`

の `WebContents` のためのデバッガーです。

## クラス: Debugger

> Chromeのリモートデバッギングプロトコルのための代替トランスポートです。

ChromeのデベロッパーツールはJavascriptのランタイムで利用できる、ページと相互作用して
ページを操作できる[特別なバインディング ][rdp]を持っています。

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()

try {
  win.webContents.debugger.attach('1.1')
} catch (err) {
  console.log('Debugger attach failed : ', err)
}

win.webContents.debugger.on('detach', (event, reason) => {
  console.log('Debugger detached due to : ', reason)
})

win.webContents.debugger.on('message', (event, method, params) => {
  if (method === 'Network.requestWillBeSent') {
    if (params.request.url === 'https://www.github.com') {
      win.webContents.debugger.detach()
    }
  }
})

win.webContents.debugger.sendCommand('Network.enable')
```

### インスタンスメソッド

#### `debugger.attach([protocolVersion])`

* `protocolVersion` String (オプション) - 要求されたデバッギングプロトコルバージョン。

`webContents` にデバッガーをアタッチします。.

#### `debugger.isAttached()`

`Boolean` - デバッガーが `webContents` にアタッチされているか否かを返します。

#### `debugger.detach()`

`webContents` からデバッガーをデタッチします。

#### `debugger.sendCommand(method[, commandParams, callback])`

* `method` String - メソッド名。リモートデバッギングプロトコルで定義されている
  メソッドでなければなりません。
* `commandParams` Object (オプション) - リクエストパラメタを示すJSONオブジェクト。
* `callback` Function (オプション) - レスポンス。
  * `error` Object - コマンドの失敗を示すエラーメッセージ。
  * `result` Object - リモートデバッギングプロトコルのコマンド説明の `returns`
    属性で定義されているレスポンス。

指定したコマンドをデバッグ対象に送信します。

### インスタンスイベント

#### イベント: 'detach'

* `event` Event
* `reason` String - デバッガーをデタッチする理由。

デバッグセッションが終了した時に出力されます。これは、`webContents` が閉じられた時か、
アタッチした `webContents` でデベロッパーツールが起動された時のいずれかで
発生します。

#### イベント: 'message'

* `event` Event
* `method` String - メソッド名。
* `params` Object - リモートデバッギングプロトコルの `parameters` 属性で定義
  されているイベントパラメタ。

デバッグ対象がinstrumentationイベントを発行するたびに出力されます。

[rdp]: https://developer.chrome.com/devtools/docs/debugger-protocol
[`webContents.findInPage`]: web-contents.md#contentsfindinpagetext-options
