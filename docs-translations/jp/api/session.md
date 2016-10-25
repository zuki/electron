# session

> セッション、クッキー、キャッシュ、プロキシー設定などを管理します。

`session`モジュールは、新しい`Session`オブジェクトの作成に使用できます。

既存ページの `session` にも、[`WebContents`](web-contents.md)の `session` プロパティを使ったり、`session` モジュールからアクセスできます。

```javascript
const {BrowserWindow} = require('electron')

let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('http://github.com')

const ses = win.webContents.session
console.log(ses.getUserAgent())
```

## メソッド

`session`メソッドは以下のメソッドを持ちます。

### `session.fromPartition(partition[, options])`

* `partition` String
* `options` Object
  * `cache` Boolean - キャッシュを有効にするか否か。

`Session` - `partition`文字列から新しい`Session`インスタンスを返します。同じ`partition`
を持つ既存の `Session` が存在する場合は、それが返されます。そうでない場合は、新たな
`Session` インスタンスが `options` を使って作成されます。

`partition`が`persist:`から始まっている場合は、同じ`partition`を持つアプリ内のすべての
ページで利用可能な永続セッションを使います。`persist:`で始まっていない無い場合は、ページはインメモリセッションを使います。`partition`が空の場合、アプリのデフォルトセッションが返されます。

## プロパティ

`session`モジュールは以下のプロパティを持ちます。

### session.defaultSession

`Session` オブジェクト - アプリのデフォルトセッションオブジェクト。

## クラス: Session

> セッションのプロパティの取得と設定を行います。

`session`モジュールで、`Session`オブジェクトを作成できます:

```javascript
const {session} = require('electron')
const ses = session.fromPartition('persist:name')
console.log(ses.getUserAgent())
```

### インスタンスイベント

`Session`のインスタンスでは以下のイベントが利用できます。

#### イベント: 'will-download'

* `event` Event
* `item` [DownloadItem](download-item.md)
* `webContents` [WebContents](web-contents.md)

Electronが `webContents` の `item`をダウンロードしようとする時に、出力されます。

`event.preventDefault()` をコールするとダウンロードをキャンセルします。`item` は
プロセスの次のループからは利用できなくなります。


```javascript
const {session} = require('electron')
session.defaultSession.on('will-download', (event, item, webContents) => {
  event.preventDefault()
  require('request')(item.getURL(), (data) => {
    require('fs').writeFileSync('/somewhere', data)
  })
})
```

### インスタンスメソッド

`Session`のインスタンスでは以下のメソッドが利用できます。

#### `ses.getCacheSize(callback)`

* `callback` Function
  * `size` Integer - 使用しているバイト単位のキャッシュサイズ。

セッションの現在のキャッシュサイズを返します。

#### `ses.clearCache(callback)`

* `callback` Function - 操作が完了したらコールされます。

セッションのHTTPキャッシュをクリアします。

#### `ses.clearStorageData([options, ]callback)`

* `options` Object (オプション)
  * `origin` String - `window.location.origin` の表現 `scheme://host:port` に
    従うべきです。
  * `storages` String[] - クリアするストレージの種類で、次を含めることができます:
    `appcache`、 `cookies`、 `filesystem`、 `indexdb`、 `local storage`、
    `shadercache`、 `websql`、 `serviceworkers`
  * `quotas` String[] - クリアする割当の種類で、次を含めることができます:
    `temporary`, `persistent`, `syncable`.
* `callback` Function (オプション) - 操作が完了したらコールされます。

ウェブストレージのデータをクリアします。

#### `ses.flushStorageData()`

書き込まれていないDOMStorageデータをディスクに書き込みます。

#### `ses.setProxy(config, callback)`

* `config` Object
  * `pacScript` String - PACファイルに関連付けらえたURL
  * `proxyRules` String - 使用するプロキシを指定するルール
  * `proxyBypassRules` String - プロキシ設定を無視すべきURLを指定するルール
* `callback` Function - 操作が完了したらコールされます。

プロキシ設定を設定します。

`pacScript` と `proxyRules` が共に指定されたら、`proxyRules`オプションは無視され、`pacScript`設定が適用されます。

`proxyRules`は次のルールに従わなければなりません。

```
proxyRules = schemeProxies[";"<schemeProxies>]
schemeProxies = [<urlScheme>"="]<proxyURIList>
urlScheme = "http" | "https" | "ftp" | "socks"
proxyURIList = <proxyURL>[","<proxyURIList>]
proxyURL = [<proxyScheme>"://"]<proxyHost>[":"<proxyPort>]
```

具体例:

* `http=foopy:80;ftp=foopy2` - `http://`URLにはHTTPプロキシ `foopy:80` を、
  `ftp://`URLにはHTTPプロキシ `foopy2:80` を使用します。
* `foopy:80` - 全てのURLで `foopy:80` を使用します。
* `foopy:80,bar,direct://` - 全てのURLでHTTPプロキシ `foopy:80` を使用し、
  `foopy:80` が利用できない場合は `bar` でフェイルオーバーし、それでも駄目なら
  プロキシを使いません。
* `socks4://foopy` - 全てのURLでSOCKS v4プロキシ `foopy:1080` を使用します。
* `http=foopy,socks5://bar.com` - http URLにはHTTPプロキシ `foopy` を使い、
  `foopy`が利用できない場合は、SOCKS5 proxy `bar.com` にフェイルオーバーします。
* `http=foopy,direct://` - 　http URLではHTTPプロキシ `foopy`を使用し、
  `foopy`が利用できない場合は、プロキシを使いません。
* `http=foopy;socks=foopy2` -  http URLではHTTPプロキシ `foopy` を使用し、
  それ以外のすべてのURLでは `socks4://foopy2` を使用します。

`proxyBypassRules` は以下で説明する規則のコンマ区切りのリストです。

* `[ URL_SCHEME "://" ] HOSTNAME_PATTERN [ ":" <port> ]`

   パターン HOSTNAME_PATTERN にマッチするすべてのホスト名にマッチします。

   例:
     "foobar.com", "*foobar.com", "*.foobar.com", "*foobar.com:99",
     "https://x.*.y.com:99"

 * `"." HOSTNAME_SUFFIX_PATTERN [ ":" PORT ]`

   特定のドメイン接尾字にマッチします。

   例:
     ".google.com", ".com", "http://.google.com"

* `[ SCHEME "://" ] IP_LITERAL [ ":" PORT ]`

   IPアドレスリテラルのURLにマッチします。

   例:
     "127.0.1", "[0:0::1]", "[::1]", "http://[::1]:99"

*  `IP_LITERAL "/" PREFIX_LENGHT_IN_BITS`

   指定した範囲に含まれるIPリテラルの任意のURLにマッチします。IP範囲はCIDR記法を
   使って指定します。

   例:
     "192.168.1.1/16", "fefe:13::abc/33".

*  `<local>`

   ローカルアドレスにマッチします。`<local>` の意味は、ホストが "127.0.0.1", "::1",
   "localhost" のいずれかとマッチするか否かです。

### `ses.resolveProxy(url, callback)`

* `url` URL
* `callback` Function

`url` のプロキシ情報を解決します。リクエストが実行された時、`callback` は、
`callback(proxy)` の形式でコールされます。

#### `ses.setDownloadPath(path)`

* `path` String - ダウンロード場所

ダウンロードの保存ディレクトリを設定します。規定値のダウンロードディレクトリは、各自の
アプリフォルダー配下の `Downloads` ディレクトリです。

#### `ses.enableNetworkEmulation(options)`

* `options` Object
  * `offline` Boolean (オプション) - ネットワークの停止をエミュレートするか否か。既定値は `false`。
  * `latency` Double (オプション) - ミリ秒単位のRTT。既定値は `0` で、レイテンシ制限を無効にする。
  * `downloadThroughput` Double (オプション) - Bps単位のダウンロード速度。既定値は `0`
   で、ダウンロード制限を無効にする。
  * `uploadThroughput` Double (オプション) - Bps単位のアップロード速度。既定値は `0`
   で、アップロード制限を無効にする。

指定した設定で `session` 用のネットワークをエミュレートします。

```javascript
// GPRSコネクションをスループット 50kbps、レイテンシ 500ms でエミュレートする
window.webContents.session.enableNetworkEmulation({
  latency: 500,
  downloadThroughput: 6400,
  uploadThroughput: 6400
})

// ネットワーク停止をエミュレートする
window.webContents.session.enableNetworkEmulation({offline: true})
```

#### `ses.disableNetworkEmulation()`

`session`ですでに有効になっているネットワークエミュレーションを無効化します。オリジナルの
ネットワーク設定にリセットします。

#### `ses.setCertificateVerifyProc(proc)`

* `proc` Function

`session`の証明書検証ロジックを設定します。サーバー証明書確認がリクエストされる度に、
`proc` が `proc(hostname, certificate, callback)` の形でコールされます。
`callback(true)`をコールすると証明書を受け入れ、`callback(false)`をコールすると
拒否します。

`setCertificateVerifyProc(null)`をコールすると既定の証明書検証ロジックに戻ります。

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()

win.webContents.session.setCertificateVerifyProc((hostname, cert, callback) => {
  callback(hostname === 'github.com')
})
```
#### `ses.setPermissionRequestHandler(handler)`

* `handler` Function
  * `webContents` Object - アクセス権を要求する[WebContents](web-contents.md)。
  * `permission` String - 'media', 'geolocation', 'notifications', 'midiSysex',
    'pointerLock', 'fullscreen', 'openExternal' のいずれか。
  * `callback` Function - アクセス権を許可または拒否。

`session`に対するアクセス権要求への応答に使用するハンドラを設定します。`callback(true)`を
コールするとアクセスを許可し、`callback(false)`をコールすると拒否します。

```javascript
const {session} = require('electron')
session.fromPartition('some-partition').setPermissionRequestHandler((webContents, permission, callback) => {
  if (webContents.getURL() === 'some-host' && permission === 'notifications') {
    return callback(false) // denied.
  }

  callback(true)
})
```

#### `ses.clearHostResolverCache([callback])`

* `callback` Function (オプション) - 操作が完了した時に呼び出されます。

ホストリゾルバキャッシュをクリアします。

#### `ses.allowNTLMCredentialsForDomains(domains)`

* `domains` String - 統合認証が有効になっているサーバのカンマ区切りのリスト。

HTTP NTLM認証またはNegotiate認証に証明書を常に送信するか否かを動的に設定します。

```javascript
const {session} = require('electron')
// `example.com`, `foobar.com`, `baz`で終わる
// すべてのURLを統合認証とみなす
session.defaultSession.allowNTLMCredentialsForDomains('*example.com, *foobar.com, *baz')

// すべてのURLを統合認証とみなす
session.defaultSession.allowNTLMCredentialsForDomains('*')
```

#### `ses.setUserAgent(userAgent[, acceptLanguages])`

* `userAgent` String
* `acceptLanguages` String (オプション)

このセッションの `userAgent` と `acceptLanguages` を上書きします。

`acceptLanguages` は言語コードのカンマ区切りの順序付きリストでなければなりません。
たとえば、`"en-US,fr,de,ko,zh-CN,ja"`。

このメソッドは既存の `WebContents` には影響を与えません。また、各 `WebContents` で
`webContents.setUserAgent` を使って、セッション全体のユーザエージェントを上書きする
ことができます。

#### `ses.getUserAgent()`

`String` - このセッションのユーザエージェントを返します。

#### `ses.getBlobData(identifier, callback)`

* `identifier` String - 有効なUUID.
* `callback` Function
  * `result` Buffer - Blobデータ。

`Blob` - `identifier`に紐付くblobデータ。

### インスタンスプロパティ

`Session` のインスタンスでは以下のプロパティが利用できます。

#### `ses.cookies`

このセッションのCookiesオブジェクト。

#### `ses.webRequest`

このセッションのWebRequestオブジェクト。

#### `ses.protocol`

このセッションのProtocolオブジェクト（[protocol](protocol.md)モジュールのインスタンス）。

```javascript
const {app, session} = require('electron')
const path = require('path')

app.on('ready', function () {
  const protocol = session.fromPartition('some-partition').protocol
  protocol.registerFileProtocol('atom', function (request, callback) {
    var url = request.url.substr(7)
    callback({path: path.normalize(`${__dirname}/${url}`)})
  }, function (error) {
    if (error) console.error('Failed to register protocol')
  })
})
```

## クラス: Cookies

> セッションクッキーを検索し、変更します。

`Cookies` クラスのインスタンスは `Session` の `cookies` プロパティを使ってアクセス
されます。

たとえば:

```javascript
const {session} = require('electron')

// すべてのクッキーを検索
session.defaultSession.cookies.get({}, (error, cookies) => {
  console.log(error, cookies)
})

// 指定のURLに紐付いたすべてのクッキーを検索
session.defaultSession.cookies.get({url: 'http://www.github.com'}, (error, cookies) => {
  console.log(error, cookies)
})

// 指定のクッキーデータを持つクッキーを設定する
// 同等なクッキーがすでに存在する場合は上書きする
const cookie = {url: 'http://www.github.com', name: 'dummy_name', value: 'dummy'}
session.defaultSession.cookies.set(cookie, (error) => {
  if (error) console.error(error)
})
```

### インスタンスイベント

`Cookies` のインスタンスでは以下のイベントが利用できます。

#### イベント: 'changed'

* `event` Event
* `cookie` Object - 変更されたクッキー。
* `cause` String - 以下の値のいずれかによる変更の理由。
  * `explicit` - クッキーは消費者のアクションにより直接変更された。
  * `overwrite` - クッキーは自身を上書きする挿入操作により自動的に削除された。
  * `expired` - クッキーは失効により自動的に削除された。
  * `evicted` - クッキーはガベージコレクションにより自動的に撤去された。
  * `expired-overwrite` - クッキーはすでに失効している有効期限日で上書きされた。
* `removed` Boolean - クッキーが削除された場合は `true`、そうでない場合は `false`。

クッキーが追加、編集、削除、失効により変更された時に出力されます。

### インスタンスメソッドI

`Cookies` のインスタンスでは以下のメソッドが利用できます。

#### `cookies.get(filter, callback)`

* `filter` Object
  * `url` String (オプション) - `url`に日も注いたクッキーを抽出します。空値はすべてのURLのクッキーを抽出することを意味します。
  * `name` String (オプション) - クッキーを名前でフィルタリングします。
  * `domain` String (オプション) - ドメインが `domains` にマッチ、または `domains` の
    サブドメインであるキッキーを抽出します。
  * `path` String (オプション) - パスが `path` にマッチするキッキーを抽出します。
  * `secure` Boolean (オプション) - クッキーをSecureプロパティでフィルタリングします。
  * `session` Boolean (オプション) - セッションクッキーまたは永続クッキーを除去します。
* `callback` Function

`filter` にマッチするすべてのクッキーを得るリクエストを送信します。`callback`は
完了時に `callback(error, cookies)` の形でコールされます。

`cookies` は `cookie` オブジェクトの配列です。

* `cookie` Object
  *  `name` String - クッキーの名前。
  *  `value` String - クッキーの値。
  *  `domain` String - クッキーのドメイン。
  *  `hostOnly` String - クッキーはホストのみクッキーであるか否か。
  *  `path` String - クッキーのパス。
  *  `secure` Boolean - クッキーはセキュアのマーク付けがされているか否か。
  *  `httpOnly` Boolean - クッキーはHTTPのみのマーク付けがされているか否か。
  *  `session` Boolean - クッキーはセッションクッキーか、有効期限日付きの永続クッキーか。
  *  `expirationDate` Double (オプション) - UNIXエポックからの秒数によるクッキーの
    有効期限日。セッションクッキーでは提供されない。

#### `cookies.set(details, callback)`

* `details` Object
  * `url` String - クッキーに紐付けるURL。
  * `name` String - クッキーの名前。省略された場合は既定値で空。
  * `value` String - クッキーの値。省略された場合は既定値で空。
  * `domain` String - クッキーのドメイン。省略された場合は既定値で空。
  * `path` String - クッキーのパス。省略された場合は既定値で空。
  * `secure` Boolean - クッキーはセキュアのマーク付けがされるべきか否か。既定値は `false`。
  * `httpOnly` Boolean - クッキーはホストのみのマーク付けがされるべきか否か。既定値は `false`。
  * `expirationDate` Double -	UNIXエポックからの秒数によるクッキーの有効期限日。
    省略された場合は、クッキーはセッションクッキーとなり、セッション間で保持されなくなります。
* `callback` Function

`details` でクッキーを設定します。`callback`は完了後に `callback(error)` の形で
コールされます。

#### `cookies.remove(url, name, callback)`

* `url` String - クッキーに紐付いたURL。
* `name` String - 削除するクッキーの名前。
* `callback` Function

`url` と `name` にマッチするクッキーを削除します。, `callback`は完了後に
`callback()` の形でコールされます。

## クラス: WebRequest

> そのライフタイムのさまざまな段階でリクエストの内容をインターセプトし変更します。

`webRequest` クラスのインスタンスは `Session` の `webRequest` プロパティを使って
アクセスされます。

`webRequest` のメソッドは、オプションの `filter` と `listener` を受け取ります。
`listener` は、APIのイベントが発生した時に、`listener(details)` の形でコールされます。
`details` オブジェクトはリクエストを説明します。`listener` として `null` を渡すとイベントの購読を中止します。

`filter` オブジェクトは、URLパターンにマッチしないリクエストの除外に使用されるURLパターンの
配列である `urls` プロパティを持ちます。`filter`を省略した場合、全てのリクエストにマッチします。

いくつかのイベントでは、`listener` に `callback` が渡されます。これは `listener` の
作業が動作した時に、`response`オブジェクトを引数にコールされる必要があります。

リクエストに `User-Agent` ヘッダーを追加する例を示します。

```javascript
const {session} = require('electron')

// 以下のURLに対するすべてのリクエストのユーザエージェントを変更する
const filter = {
  urls: ['https://*.github.com/*', '*://electron.github.io']
}

session.defaultSession.webRequest.onBeforeSendHeaders(filter, (details, callback) => {
  details.requestHeaders['User-Agent'] = 'MyAgent'
  callback({cancel: false, requestHeaders: details.requestHeaders})
})
```

### インスタンスメソッド

`WebRequest` のインスタンスでは以下のメソッドが利用できます。

#### `webRequest.onBeforeRequest([filter, ]listener)`

* `filter` Object
* `listener` Function

リクエストが発生しようとしている時、`listener(details, callback)`で`listener` がコールされます。

* `details` Object
  * `id` Integer
  * `url` String
  * `method` String
  * `resourceType` String
  * `timestamp` Double
  * `uploadData` Array (オプション)
* `callback` Function

`uploadData`は `data`オブジェクトの配列です。

* `data` Object
  * `bytes` Buffer - 送信されるコンテンツ
  * `file` String - アップロードされるファイルパス
  * `blobUUID` String - blobデータのUUID。データの抽出には [ses.getBlobData](session.md#sesgetblobdataidentifier-callback) メソッドを使ってください。

`callback` は `response`オブジェクトを引数にコールされなければなりません。。

* `response` Object
  * `cancel` Boolean (オプション)
  * `redirectURL` String (オプション) - オリジナルリクエストが送信もしくは完了するのを
    中断し、代わりに指定したURLにリダイレクトします。

#### `webRequest.onBeforeSendHeaders([filter, ]listener)`

* `filter` Object
* `listener` Function

リクエストヘッダーが提供されると、HTTPリクエストが送信される前に、listener` が
 `listener(details, callback)` の形でコールされます。これは、サーバにTCP接続が
 行われた後で、HTTPデータが送信される前に行われます。

* `details` Object
  * `id` Integer
  * `url` String
  * `method` String
  * `resourceType` String
  * `timestamp` Double
  * `requestHeaders` Object
* `callback` Function

`callback` は `response`オブジェクトを引数にコールされなければなりません。

* `response` Object
  * `cancel` Boolean (オプション)
  * `requestHeaders` Object (オプション) - 付与されると、リクエストはそれらのヘッダーで作成されます。

#### `webRequest.onSendHeaders([filter, ]listener)`

* `filter` Object
* `listener` Function

リクエストがサーバーに送信されようとする直前に `listener` が `listener(details)` の形で
コールされます。先に実行された `onBeforeSendHeaders` レスポンスの変更は、このリスナーが
起動した時点で有効になっています。

* `details` Object
  * `id` Integer
  * `url` String
  * `method` String
  * `resourceType` String
  * `timestamp` Double
  * `requestHeaders` Object

#### `webRequest.onHeadersReceived([filter,] listener)`

* `filter` Object
* `listener` Function

リクエストのHTTPレスポンスヘッダーを受信したとき、`listener` が `listener(details, callback)` の形でコールされます。

* `details` Object
  * `id` String
  * `url` String
  * `method` String
  * `resourceType` String
  * `timestamp` Double
  * `statusLine` String
  * `statusCode` Integer
  * `responseHeaders` Object
* `callback` Function

`callback` は `response`オブジェクトを引数にコールされなければなりません。

* `response` Object
  * `cancel` Boolean
  * `responseHeaders` Object (オプション) - 指定されると、サーバはこれらのヘッダーで
    レスポンスしたと仮定されます。
  * `statusLine` String (オプション) - ヘッダーの状態を変更するために `responseHeaders` を上書きした時は、指定されるべきです。そうでない場合は、
  オリジナルのレスポンスヘッダーの状態が使用されます。

#### `webRequest.onResponseStarted([filter, ]listener)`

* `filter` Object
* `listener` Function

レスポンスボディの最初のバイトを受信したとき、`listener` が `listener(details)` の形で
コールされます。HTTPリクエストでは、これは、ステータス行とレスポンスヘッダーが利用可能で
あることを意味します。

* `details` Object
  * `id` Integer
  * `url` String
  * `method` String
  * `resourceType` String
  * `timestamp` Double
  * `responseHeaders` Object
  * `fromCache` Boolean  - レスポンスがディスクキャッシュから取得されたか否かを示します
  * `statusCode` Integer
  * `statusLine` String

#### `webRequest.onBeforeRedirect([filter, ]listener)`

* `filter` Object
* `listener` Function

サーバーがリダイレクトを開始しはじめたとき、`listener` が `listener(details)` の形で
コールされます。

* `details` Object
  * `id` String
  * `url` String
  * `method` String
  * `resourceType` String
  * `timestamp` Double
  * `redirectURL` String
  * `statusCode` Integer
  * `ip` String (オプション) - 実際にリクエストが送信されたサーバーのIPアドレス
  * `fromCache` Boolean
  * `responseHeaders` Object

#### `ses.webRequest.onCompleted([filter, ]listener)`

* `filter` Object
* `listener` Function

リクエストが完了した時、`listener` が `listener(details)` の形でコールされます。

* `details` Object
  * `id` Integer
  * `url` String
  * `method` String
  * `resourceType` String
  * `timestamp` Double
  * `responseHeaders` Object
  * `fromCache` Boolean
  * `statusCode` Integer
  * `statusLine` String

#### `webRequest.onErrorOccurred([filter, ]listener)`

* `filter` Object
* `listener` Function

エラーが発生した時、`listener` が `listener(details)` の形でコールされます。

* `details` Object
  * `id` Integer
  * `url` String
  * `method` String
  * `resourceType` String
  * `timestamp` Double
  * `fromCache` Boolean
  * `error` String - エラーの説明
