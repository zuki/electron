# app

> アプリケーションのイベントのライフサイクルを制御します。

次の例は、最後のウィンドウが閉じた時にアプリケーションを終了させる方法を示しています。

```javascript
const {app} = require('electron')
app.on('window-all-closed', () => {
  app.quit()
})
```

## イベント

`app` オブジェクトは次のイベントを出力されます。

### イベント: 'will-finish-launching'

アプリケーションの基礎起動が終わった時に出力されます。Windows と Linuxでは、
`will-finish-launching` イベントと`ready`イベントは同じです。macOSでは、
このイベントは `NSApplication`の `applicationWillFinishLaunching` 通知
に相当します。通常、`open-file`と`open-url` のリスナーを設定し、クラッシュ
リポータと自動アップデータを開始します。

ほとんどの場合、 `ready` イベントハンドラーですべてを行うべきです。

### イベント: 'ready'

返り値:

* `launchInfo` Object _macOS_

Electronが初期化を終えた時に出力されます。macOSでは、`launchInfo` は、アプリケーションが
通知センターから起動された場合、アプリケーションのオープンに使用された
`NSUserNotification` の `userInfo` を保持します。`app.isReady()` を呼び出して、
このイベントがすでに発火されたかチェックすることができます。

### イベント: 'window-all-closed'

全てのウィンドウが閉じられた時に出力されます。

このイベントを購読しておらず、すべてのウィンドウが閉じられた場合、既定の挙動は
アプリケーションを終了させることです。しかし、購読している場合は、アプリケーションを終了
させるか否かを制御します。ユーザーが `Cmd + Q`を押したり、開発者が`app.quit()`をコールすると、Electronは最初にすべてのウィンドウをクローズしようとし、次に、`will-quit`
イベントを出力します。この場合、`window-all-closed`イベントは出力されません。

### イベント: 'before-quit'

返り値:

* `event` Event

アプリケーションがウィンドウをクローズし始める前に出力されます。`event.preventDefault()`をコールすると、アプリケーションを終了させる既定の挙動を止めることができます。

### イベント: 'will-quit'

返り値:

* `event` Event

全てのウィンドウが閉じられ、アプリケーションが終了する時に出力されます。`event.preventDefault()`をコールすると、アプリケーションを終了させる既定の挙動を止めることができます。

`will-quit`イベント と `window-all-closed` イベントの違いは、`window-all-closed` イベントの説明を見てください。

### イベント: 'quit'

返り値:

* `event` Event
* `exitCode` Integer

アプリケーションが終了した時に出力されます。

### イベント: 'open-file' _macOS_

返り値:

* `event` Event
* `path` String

アプリケーションでファイルを開こうとした時に出力されます。`open-file`イベントは通常、
アプリケーションがすでに起動されており、OSがファイルを開くためにアプリケーションを再使用
したい時に出力されます。ファイルがdockにドロップアウトされたが、アプリケーションがまだ
起動していない時にも`open-file` は出力されます。このケースを処理するために、
アプリケーションの起動のかなり早い段階で、`open-file` イベントをリッスンするように
してください（まだ `ready` イベントが出力される前に）。

このイベントを処理したい時には `event.preventDefault()` をコールすべきです。

Windowsでは、ファイルパスを取得するために、 `process.argv` をパースする必要があります。

### イベント: 'open-url' _macOS_

返り値:

* `event` Event
* `url` String

アプリケーションでURLを開こうとした時に出力されます。URLスキームがアプリケーションで開かれるように登録されていなければなりません。

このイベントを処理したい場合は、`event.preventDefault()`をコールすべきです。

### イベント: 'activate' _macOS_

返り値:

* `event` Event
* `hasVisibleWindows` Boolean

アプリケーションがアクティブになった時に出力されます。通常は、ユーザーがアプリケーションの
ドックアイコンをクリックした時に発生します。

### イベント: 'continue-activity' _macOS_

返り値:

* `event` Event
* `type` String - アクティビティを識別する文字列。
  [`NSUserActivity.activityType`][activity-type]にマップします。
* `userInfo` Object - 別のデバイス上のアクティビティにより格納されたアプリケーション
  固有の状態を含んでいます。

異なるデバイスのアクティビティが再開しようとした時に [ハンドオフ][handoff]の間、出力されます。
このイベントを処理したい場合は、`event.preventDefault()` をコールするべきです。

ユーザーアクティビティは、アクティビティの元となったアプリケーションと同じ開発チームIDを持ち、
そのアクティビティタイプをサポートしているアプリケーションでのみ継続することができます。
サポートするアクティビティタイプはアプリケーションの `Info.plist` で
`NSUserActivityTypes` キーにより指定します。

### イベント: 'browser-window-blur'

返り値:

* `event` Event
* `window` BrowserWindow

[browserWindow](browser-window.md) からフォーカスが外れた時に出力されます。

### イベント: 'browser-window-focus'

返り値:

* `event` Event
* `window` BrowserWindow

[browserWindow](browser-window.md) にフォーカスが当たったとき出力されます。

### イベント: 'browser-window-created'

返り値:

* `event` Event
* `window` BrowserWindow

新しい [browserWindow](browser-window.md) が作成された時に出力されます。

### イベント: 'certificate-error'

返り値:

* `event` Event
* `webContents` [WebContents](web-contents.md)
* `url` URL
* `error` String - エラーコード
* `certificate` Object
  * `data` Buffer - PEM形式でエンコードされたデータ
  * `issuerName` String - 発行者の一般名
  * `subjectName` String - 対象の一般名
  * `serialNumber` String - Hex値で表された文字列
  * `validStart` Number - 証明書の有効期限の開始日（秒単位）
  * `validExpiry` Number - 証明書の有効期限の終了日（秒単位）
  * `fingerprint` String - 証明書のフィンガープリント
* `callback` Function

 `url` の  `certificate` 検証に失敗した時に発生します。証明書を信頼するために、
 `event.preventDefault()` で既定の動作を止め、 `callback(true)`をコールする
 必要があります。

```javascript
const {app} = require('electron')

app.on('certificate-error', (event, webContents, url, error, certificate, callback) => {
  if (url === 'https://github.com') {
    // 検証ロジック
    event.preventDefault()
    callback(true)
  } else {
    callback(false)
  }
})
```

### イベント: 'select-client-certificate'

返り値:

* `event` Event
* `webContents` [WebContents](web-contents.md)
* `url` URL
* `certificateList` [Certificate[]](structures/certificate.md)
* `callback` Function

クライアント証明書が要求された時に出力されます。

`url` は、クライアント証明書を要求するナビゲーションエントリーに対応します。は、
リストからフィルタリングしたエントリを引数として `callback` をコールする必要が
あります。`event.preventDefault()` を使用して、アプリケーションが証明書ストアの
最初の証明書を使用するのを止めます。

```javascript
const {app} = require('electron')

app.on('select-client-certificate', (event, webContents, url, list, callback) => {
  event.preventDefault()
  callback(list[0])
})
```

### イベント: 'login'

返り値:

* `event` Event
* `webContents` [WebContents](web-contents.md)
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

`webContents` がベーシック認証をしようとした時に出力されます。

既定の動作はすべての認証をキャンセルすることです。これを上書きするために、
`event.preventDefault()` で規定の動作を止め、クレデンシャルを使って、
`callback(username, password)` をコールするべきです。

```javascript
const {app} = require('electron')

app.on('login', (event, webContents, request, authInfo, callback) => {
  event.preventDefault()
  callback('username', 'secret')
})
```

### イベント: 'gpu-process-crashed'

返り値:

* `event` Event
* `killed` Boolean

gpu プロセスがクラッシュした時、または強制終了させられた時に出力されます。

### イベント: 'accessibility-support-changed' _macOS_ _Windows_

返り値:

* `event` Event
* `accessibilitySupportEnabled` Boolean - Chromeのアクセシビリティサポートが有効になった場合は `true`、そうでなければ `false`。

Chromeのアクセシビリティサポートが変わった時に出力されます。このイベントはスクリーンリーダー
などの支援テクノロジが有効になったり、向こうになったりした時に発火します。詳細は https://www.chromium.org/developers/design-documents/accessibility を参照して
ください。

## メソッド

`app` オブジェクトは次のメソッドを持ちます。

**Note:** いくつかのメソッドは、特定のオペレーティングシステム向けに提供され、そのようにラベルで表示します。

### `app.quit()`

全てのウィンドウを閉じようとします。`before-quit`イベントが最初に出力されます。すべてのウィンドウを閉じることに成功したら、`will-quit`イベントが出力され、既定では、アプリケーションが終了します。

このメソッドは、全ての`beforeunload`と`unload`イベントハンドラが正しく実行されることを
保証します。`beforeunload` イベントハンドラで、`false`を返すことでウィンドウの終了を
キャンセルできます。

### `app.exit(exitCode)`

* `exitCode` Integer (オプション)

`exitCode`で今すぐ終了します。`exitCode` の既定値は 0 です。

全てのウィンドウが、ユーザーに確認することなく、すぐに閉じられ、`before-quit` イベント
と`will-quit` イベントは出力されません。

### `app.relaunch([options])`

* `options` Object (オプション)
  * `args` String[] (オプション)
  * `execPath` String (オプション)

現在のインスタンスの終了時にアプリケーションを再起動します。

デフォルトでは、新しいインスタンスは現在のインスタンと同じ作業ディレクトリとコマンドライン
引数を使用します。`args` が指定されると、コマンドライン引数として渡されます。`execPath` が
指定されると、現在のアプリケーションを再起動せずに `execPath` が起動されます。

このメソッドは実行されているアプリケーションを終了させないことに注意してください。
アプリケーションをリスタートするためには、 `app.relaunch` をコールした後に
`app.quit` か `app.exit` をコールしなければなりません。

`app.relaunch` が複数回コールされた時は、現在のインスタンスが終了した際に複数の
インスタンスが起動されます。

現在のインスタンスを直ちにリスタートし、新しいインスンタンスに新たなコマンドライン引数を
追加する例を示します。

```javascript
const {app} = require('electron')

app.relaunch({args: process.argv.slice(1).concat(['--relaunch'])})
app.exit(0)
```

### `app.isReady()`

`Boolean` - Electronが初夏化処理を終えている場合は `true`、そうでない場合は
`false` を返します。

### `app.focus()`

Linuxでは、最初の可視ウィンドウをフォーカスします。macOSでは、アプリケーションをアクティブ
アプリケーションにします。Windowsでは、アプリケーションの最初のウィンドウをフォーカスします。

### `app.hide()` _macOS_

すべてのアプリケーションウィンドウを最初化することなく隠します。

### `app.show()` _macOS_

隠されていたアプリケーションウィンドウを表示します。自動的にはそれらにフォーカスしません。

### `app.getAppPath()`

`String` - 現在のアプリケーションディレクトリを返します。

### `app.getPath(name)`

* `name` String

`String` - `name`に関連した特定のディレクトリやファイルへのパスを返します。失敗したら、`Error`をスローします。

`name`で次のパスをリクエストできます。

* `home` ユーザーのホームディレクトリ
* `appData` 既定では以下を指し示す、ユーザーごとのアプリケーションディレクトリ。
  * Windowでは、`%APPDATA%`
  * Linuxでは、`$XDG_CONFIG_HOME` または `~/.config`
  * macOSでは、`~/Library/Application Support`
* `userData` アプリの設定ファイルを格納するディレクトリで、既定では`appData` ディレクトリ配下のアプリ名ディレクトリです
* `temp` 一時ディレクトリ
* `exe` 現在の実行ファイル
* `module` `libchromiumcontent` ライブラリ
* `desktop` 現在のユーザーのデスクトップディレクトリ
* `documents` ユーザーの "My Documents"用ディレクトリ
* `downloads` ユーザーのダウンロード用ディレクトリ
* `music` ユーザーのミュージック用ディレクトリ
* `pictures` ユーザーのピクチャー用ディレクトリ
* `videos` ユーザーのビデオ用ディレクトリ
* `pepperFlashSystemPlugin`  Pepper Flashプラグインのそのシステムのバージョンのフルパス

### `app.setPath(name, path)`

* `name` String
* `path` String

`name`に関連した特定のディレクトリやファイルへの`path` を上書きします。存在しないパスを指定した場合、このメソッドがディレクトリを作成します。失敗したら、`Error`をスローします。

`app.getPath`で定義されている `name` のパスのみ、上書きできます。

既定では、webページのクッキーとキャッシュは`userData`ディレクトリ配下に格納されます。
この場所を変更したい場合は、 `app` モジュールの `ready` イベントが出力される前に
`userData`パスを上書きする必要があります。

### `app.getVersion()`

`String` - ロードしたアプリケーションのバージョンを返します。アプリケーションの
 `package.json`ファイルにversionがない場合は、現在のバンドルまたは実行ファイルの
 バージョンが返ります。

### `app.getName()`

`String` - 現在のアプリケーション名、`package.json` ファイルで指定された名前、を返します。

`package.json` の `name` フィールドは通常、npmのモジュール仕様に従って、短い小文字の
名前です。通常は、大文字で始まるアプリケーションの正式名である `productName` を指定
するべきです。Electronは `name` より `productName` を優先します。

### `app.setName(name)`

* `name` String

現在のアプリケーション名を上書きします。

### `app.getLocale()`

`String` - 現在のアプリケーションのロケールを返します。返される可能性のある値は
[ここ](locales.md)に書かれています。

**注:** パッケージ化したアプリを配布する際には、`locales` フォルダも同梱しなければ
なりません。

**注:** Windowsでは、`ready` イベントが出力された後に、コールしなければなりません。

### `app.addRecentDocument(path)` _macOS_ _Windows_

* `path` String

最近のドキュメント一覧に`path`を追加します。

この一覧はOSが管理しています。Windowsではタスクバーから、macOSではdockメニューから
この一覧を見ることができます。

### `app.clearRecentDocuments()` _macOS_ _Windows_

最近のドキュメント一覧をクリアします。

### `app.setAsDefaultProtocolClient(protocol[, path, args])` _macOS_ _Windows_

* `protocol` String - `://`なしのプロトコルの名前。アプリケーションで `electron://`
  リンクを処理したい場合は、`electron` をパラメタにこのメソッドをコールしてください。
* `path` String (オプション) _Windows_ - 既定値は `process.execPath`。
* `args` String[] (オプション) _Windows_ - 既定値はからの配列。

`Boolean` - コールが成功したか否かを返します。

このメソッドは、現在の実行ファイルを指定したプロトコル（すなわち、URIスキーム）のデフォルトハンドラに
設定します。このメソッドはアプリケーションをオペレーションシステムにより深く統合することを
可能にします。登録されると、`your-protocol://` を持つ全てのリンクが現在の実行ファイルで
開かれるようになります。（プロトコルを含む）リンク全体がアプリケーションにパラメタとして
渡されます。

Windowsでは、2つのオプションのパラメタ、実行ファイルのパスを指定する `path` と、起動時に実行ファイルに渡される引数の配列を指定する `args` を与えることができます。

**注:** macOSでは、アプリケーションの `info.plist` に追加されれているプロトコルしか
登録できません。これは実行時には変更できません。しかし、ビルド時にシンプルなテクスト
エディタやスクリプトでファイルを変更することができます。詳細は[Apple's documentation][CFBundleURLTypes]を参照してください。

このAPIは内部ではWindowsレジストリとLSSetDefaultHandlerForURLScheme関数を使用します。

### `app.removeAsDefaultProtocolClient(protocol[, path, args])` _macOS_ _Windows_

* `protocol` String - `://`なしのプロトコルの名前。
* `path` String (オプション) _Windows_ - 既定値は `process.execPath`。
* `args` String[] (オプション) _Windows_ - 既定値は、空の配列。

`Boolean` - コールが成功したか否かを返します。

このメソッドは現在の実行ファイルが指定したプロトコル（すなわち、URIスキーム）の
デフォルトハンドラか否かをチェックします。もしそうであれば、アプリケーションを
デフォルトハンドラから外します。

### `app.isDefaultProtocolClient(protocol[, path, args])` _macOS_ _Windows_

* `protocol` String - `://`なしのプロトコルの名前。
* `path` String (オプション) _Windows_ - 既定値は `process.execPath`。
* `args` String[] (オプション) _Windows_ - 既定値は、空の配列。

返り値: `Boolean`

このメソッドは現在の実行ファイルが指定したプロトコル（すなわち、URIスキーム）の
デフォルトハンドラか否かをチェックします。もしそうであれば、`true` を返します。
そうでなければ、`false` を返します。

**注:** macOSでは、このメソッドを使って、アプリケーションがプロトコルのデフォルト
ハンドラとして登録されているか否かをチェックすることができます。macOSマシンの
`~/Library/Preferences/com.apple.LaunchServices.plist` をチェックすることでも
これを確認することができます。
詳細は[Apple's documentation][LSCopyDefaultHandlerForURLScheme] を参照
してください。

このAPI は内部ではWindowsレジストリとLSCopyDefaultHandlerForURLScheme関数を使用します。

### `app.setUserTasks(tasks)` _Windows_

* `tasks` [Task[]](structures/task.md) - `Task` オブジェクトの配列

Windowsのジャンプリストの[Tasks][tasks]カテゴリに`tasks`を追加します。

`tasks` は、次のフォーマットの `Task` オブジェクトの配列です。

`Task` オブジェクト:

* `program` String - 実行するプログラムのパス。通常は現在のプログラムを開く
  `process.execPath` を指定するべきです。
* `arguments` String - `program` が実行される際のコマンドライン引数。
* `title` String - ジャンプリストに表示する文字列。
* `description` String - このタスクの説明。
* `iconPath` String - ジャンプリストに表示するアイコンの絶対パス。アイコンを含む任意の
  リソースファイルを指定できます。通常はプログラムアイコンを表示するために `process.execPath` を
  指定できます。
* `iconIndex` Number - アイコンファイルにおけるアイコンのインデックス。アイコン
 ファイルが2つ以上のアイコンで構成されている場合、アイコンを識別するためにこの値を
 設定します。アイコンファイルが1つのアイコンで構成されている場合、この値は `0` です。

`Boolean` - コールが成功したか否かを返します。

**注:** さらにもっとジャンプリストをカスタマイズしたい場合は、代わりに
`app.setJumpList(categories)` を使ってください。

### `app.getJumpListSettings()` _Windows_

`Object` - 以下を持つ `Object` を返します。

* `minItems` Integer - ジャンプリストに表示されるアイテムの最小数（この値に関する
  より詳細な説明は[MSDN docs][JumpListBeginListMSDN]を見てください）。T
* `removedItems` [JumpListItem[]](structures/jump-list-item.md) - ユーザが
  明示的にジャンプリストのカスタムカテゴリから削除したアイテムに対応する
  `JumpListItem` オブジェクトの配列。このアイテムを `app.setJumpList()` の**次の**
  コールで再度追加してはいけません。Windowsでは削除されたアイテムのいずれかを含む
  カスタムカテゴリは表示しません。

### `app.setJumpList(categories)` _Windows_

* `categories` [JumpListCategory[]](structures/jump-list-category.md)
  または `null` - `JumpListCategory` オブジェクトの配列。

アプリケーションのためのカスタムジャンプリストを設定または削除し、次のいずれかの文字列を
返します。

* `ok` - 問題なし。
* `error` - 1つ以上のエラーが発生しました。実行時のログ出力を有効にして、起こり得る
  原因を解明してください。
* `invalidSeparatorError` - 区切り記号をジャンプリストのカスタムカテゴリに
  追加しようとしました。区切り記号は標準的な `Tasks` カテゴリでのみ許されています。
* `fileTypeRegistrationError` - アプリケーションが処理可能なタイプとして
  登録されていないファイルタイプのファイルリンクをジャンプリストに追加しようと
  しました。
* `customCategoryAccessDeniedError` - ユーザのプライバシーポリシーまたは
  グループポリーの設定により、カスタムカテゴリはジャンプリストに追加できません。

`categories` が `null` の場合、以前に設定されたカスタムジャンプリストは（もしあれば）、
（Windowsにより管理されている）アプリケーションの標準的なジャンプリストで置き換えられます。

`JumpListCategory` オブジェクトは次のプロパティを持つべきです。

* `type` String - 次のいずれかです:
  * `tasks` - このカテゴリのアイテムは標準的な `Tasks` カテゴリに分類されます。
    そのようなカテゴリが一つだけ存在する可能性があり、それは常にジャンプリストの
    一番下に表示されます。
  * `frequent` - アプリケーションに頻繁にオープンされるファイルのリストを表示
    します。カテゴリの名前とそのアイテムはWindowsにより設定されます。
  * `recent` - アプリケーションに最近オープンされるファイルのリストを表示
    します。カテゴリの名前とそのアイテムはWindowsにより設定されます。
    `app.addRecentDocument(path)` を使うことで、アイテムがこのカテゴリに間接的に
    追加される場合もあります。
  * `custom` - タスクまたはファイルのリンクを表示します。アプリケーションは `name` を
    設定しなければなりません。
* `name` String - `type` が `custom` の場合、設定されなければなりません。それ以外の場合は、省略されるべきです。
* `items` Array - `type` が `tasks` または `custom` の場合は、[`JumpListItem`](jump-list-item.md) オブジェクトの配列で。それ以外の場合は、省略されるべきです。

**注:** `JumpListCategory` オブジェクトが `type` プロパティも `name` プロパティも
設定されていない場合は、その `type` は `tasks` だと仮定されます。`name` プロパティが
設定され、`type` プロパティが省略されている場合は、`type` は `custom` だと仮定されます。

**注:** ユーザーはカスタムカテゴリからアイテムを削除できます。ただし、windowsは、
再度 `app.setJumpList(categories)` をコールして成功した**後**でないと、
削除したアイテムをカスタムカテゴリに戻すことを許可しません。それ以前に削除したアイテムを
カスタムカテゴリに再度追加しようとすると、すべてのカスタムカテゴリがジャンプリストから
削除されます。削除したアイテムのリストは `app.getJumpListSettings()` を使って得ることが
できます。

`JumpListItem` オブジェクトは次のプロパティを持つべきです。

* `type` String - 次のいずれかです。
  * `task` - タスクは指定された引数でアプリケーションを起動します。
  * `separator` - 標準的な `Tasks` カテゴリのアイテムを分けるのに使用できます。
  * `file` - ファイルリンクはジャンプリストを作成したアプリケーションを使ってファイルを
    開きます。これを動作させるために、アプリケーションは（デフォルトのハンドラである必要は
    ありませんが）そのファイルタイプのハンドラとして登録されていなければなりません。
* `path` String - オープンするファイルのパス。`type` が `file` の場合のみ設定する
  必要があります。
* `program` String - 実行するプログラムのパス。通常は現在のプログラムを開く
  `process.execPath` を指定するべきです。`type` が `task` の場合のみ設定する
    必要があります。
* `args` String - `program` が実行される際のコマンドライン引数。`type` が `task` の
  場合のみ設定する必要があります。
* `title` String - ジャンプリストのアイテムに表示するテキスト。`type` が `task` の
  場合のみ設定する必要があります。
* `description` String - タスクの説明（ツールチップに表示されます）。`type` が
  `task` の場合のみ設定する必要があります。
* `iconPath` String - ジャンプリストに表示するアイコンの絶対パス。アイコンを含む
  任意のリソースファイル（`.ico`, `.exe`, `.dll`など）を指定できます。通常はプログラム
  アイコンを表示するために `process.execPath` を指定できます。
* `iconIndex` Number - リソースファイルにおけるアイコンのインデックス。
  リソースファイルに複数のアイコンが含まれている場合、このタスクのアイコンとして表示する
  アイコンを0から始まるインデックスで指定するために、この値を使うことができます。リソース
  ファイルがアイコンを1つしか含んでいない場合、このプロパティには `0` を設定するべきです。

以下は、カスタムジャンプリストを作成する非常にシンプルな例です。

```javascript
const {app} = require('electron')

app.setJumpList([
  {
    type: 'custom',
    name: 'Recent Projects',
    items: [
      { type: 'file', path: 'C:\\Projects\\project1.proj' },
      { type: 'file', path: 'C:\\Projects\\project2.proj' }
    ]
  },
  { // nameを持つので、`type` は "custom" だと仮定される
    name: 'Tools',
    items: [
      {
        type: 'task',
        title: 'Tool A',
        program: process.execPath,
        args: '--run-tool-a',
        icon: process.execPath,
        iconIndex: 0,
        description: 'Runs Tool A'
      },
      {
        type: 'task',
        title: 'Tool B',
        program: process.execPath,
        args: '--run-tool-b',
        icon: process.execPath,
        iconIndex: 0,
        description: 'Runs Tool B'
      }
    ]
  },
  { type: 'frequent' },
  { // nameもtypeも持たないので、`type` は "tasks" だと仮定される
    items: [
      {
        type: 'task',
        title: 'New Project',
        program: process.execPath,
        args: '--new-project',
        description: 'Create a new project.'
      },
      { type: 'separator' },
      {
        type: 'task',
        title: 'Recover Project',
        program: process.execPath,
        args: '--recover-project',
        description: 'Recover Project'
      }
    ]
  }
])
```

### `app.makeSingleInstance(callback)`

* `callback` Function

このメソッドは、アプリケーションをシングルインスタンスアプリケーションにします。すなわち、
アプリケーションを複数のインスタンスで実行することを許可せず、アプリケーションが
シングルインスタンスのみで実行していることを保証し、ほかのインスタンスは
このインスタンスに通知し、終了します。

2つ目のインスタンスが実行されると、`callback` が `callback(argv, workingDirectory)`
の形ででコールされます。`argv` は、2つ目のインスタンスのコマンドライン引数の配列であり、
`workingDirectory` はそのカレントワーキングディレクトリです。通常、アプリケーションは
そのメインウィンドウにフォーカスをあて、最小化を解除することでこれに反応します。

`callback` は、 `app`の`ready` イベントの出力後に実行されることが保証されています。

プロセスがアプリケーションのプライマリインスタンスであり、アプリケーションがロードを
続けなければならない場合、このメソッドは `false`を返します。プロセスがほかのインスタンスに
パラメーターを送信した場合は `true`を返します。その場合は、直ちに終了しなければなりません。

macOSでは、ユーザーがFinderで2つ目のアプリインスタンスを開こうとして、`open-file` や
`open-url`イベントが出力されると、システムが自動的にシングルインスタンスを強制します。
しかし、ユーザーがコマンドラインでアプリケーションを開始すると、システムのシングル
インスタンス化機構は回避されるので、このメソッドを使ってシングルインスタンスを強制しなければ
なりません。

以下は、2つ目のインスタンスが起動した時に、第一インスタンスのウィンドウをアクティブにする例です。

```javascript
const {app} = require('electron')
let myWindow = null

const shouldQuit = app.makeSingleInstance((commandLine, workingDirectory) => {
  // 誰かが2つめのインスタンスを実行しようとしたので、自身のウィンドウをフォーカスする。
  if (myWindow) {
    if (myWindow.isMinimized()) myWindow.restore()
    myWindow.focus()
  }
})

if (shouldQuit) {
  app.quit()
}

// myWindowを作成し、アプリケーションの残りをロード、その他もろもろ...
app.on('ready', () => {
})
```

### `app.releaseSingleInstance()`

`makeSingleInstance` により作成されたすべてのロックを開放します。これはアプリケーションの
複数のインスタンスが再度実行され、並行して実行することを許します。

### `app.setUserActivity(type, userInfo[, webpageURL])` _macOS_

* `type` String - アクティビティを一意に識別します。[`NSUserActivity.activityType`][activity-type] に対応します。
* `userInfo` Object - 別のデバイスでの使用のために格納するアプリケーション固有の状態。
* `webpageURL` String - 再開するデバイス上に適当なアプリケーションがインストールされて
  いない場合に、ブラウザにロードするウェブページ。

Creates an `NSUserActivity` を作成し、現在のアクティビティとして設定します。
このアクティビティは、後で別のデバイスへ[アンドオフ][handoff]する対象となります。

### `app.getCurrentActivityType()` _macOS_

`String` - 現在実行しているアクティビティのタイプを返します。


### `app.setAppUserModelId(id)` _Windows_

* `id` String

[アプリケーションユーザーモデルID][app-user-model-id] を `id`に変更します。

### `app.importCertificate(options, callback)` _LINUX_

* `options` Object
  * `certificate` String - pkcs12 ファイルのパス。
  * `password` String - 証明書のパスフレーズ。
* `callback` Function
  * `result` Integer - インポートの結果。

pkcs12形式の証明書をプラットフォームの証明書ストアにインポートします。`callback` は、
インポート操作の `result` を引数にコールされます。`result` の値で、`0` は成功を、
それ以外は、Chromiumの[net_error_list](https://code.google.com/p/chromium/codesearch#chromium/src/net/base/net_error_list.h)による失敗を表します。

### `app.disableHardwareAcceleration()`

現在のアプリケーションでハーロウぇアクセレーションを無効にします。

このメソッドはアプリケーションの準備が済む前にしかコールできません。

### `app.setBadgeCount(count)` _Linux_ _macOS_

* `count` Integer

`Boolean` - コールが成功したか否かを返します。

現在のアプリケーションにカウンターバッジを設定します。`count` に `0` を指定すると、バッジが
隠されます。

macOSでは、バッジはドックアイコン上に表示されます。Linuxでは、Unityランチャーでのみ動作します。

**注:** Unityランチャーで動かすには、`.desktop` ファイルの存在が必要です。 より詳細な
情報については[デスクトップ環境の統合][unity-requiremnt]を読んでください。

### `app.getBadgeCount()` _Linux_ _macOS_

`Integer` - カウンターバッジに表示されている現在値を返します。

### `app.isUnityRunning()` _Linux_

`Boolean` - 現在のデスクトップ環境がUnityランチャーであるか否かを返します。

### `app.getLoginItemSettings()` _macOS_ _Windows_

`Object` - 以下を持つ `Object` を返します。

* `openAtLogin` Boolean - アプリケーションがログイン時にオープンするよう設定されて
  いる場合は、 `true`。
* `openAsHidden` Boolean - アプリケーションがログイン時に隠された状態でオープンする
  よう設定されている場合は、 `true`。この設定は、macOSでのみサポートされています。
* `wasOpenedAtLogin` Boolean - アプリケーションがログイン時に自動的にオープンされた
  場合は、 `true`。この設定は、macOSでのみサポートされています。
* `wasOpenedAsHidden` Boolean - アプリケーションが隠しログイン項目として
  オープンされた場合は、 `true`。これは、アプリケーションは起動時にウィンドウを
  開くべきではないことを意味します。この設定は、macOSでのみサポートされています。
* `restoreState` Boolean - アプリケーションが前回のセッションの状態を復元するべき
  ログイン項目としてオープンされた場合は、 `true`。これは、前回アプリケーションが
  クローズされた時に開いていたウィンドウをアプリケーションは復元するべきであることを
  意味します。この設定は、macOSでのみサポートされています。

**注:** このAPIは[MASビルド](docs/tutorial/mac-app-store-submission-guide.md) では無効です。

### `app.setLoginItemSettings(settings)` _macOS_ _Windows_

* `settings` Object
  * `openAtLogin` Boolean - ログイン時にアプリケーションを開くには `true` を、
    アプリケーションをログイン項目から削除するには `false` を指定します。
    既定値は `false`。
  * `openAsHidden` Boolean - アプリケーションを隠した状態で開くには `true`。
    既定値は `false`。ユーザはこの設定をシステム環境設定で編集できます。したがって、
    アプリケーションが開かれる際、現地値を知るために
    `app.getLoginItemStatus().wasOpenedAsHidden` をチェックするべきです。
    この設定は、macOSでのみサポートされています。

アプリケーションのログイン項目設定を設定します。

**注:** このAPIは[MASビルド](docs/tutorial/mac-app-store-submission-guide.md) では無効です。

### `app.isAccessibilitySupportEnabled()` _macOS_ _Windows_

`Boolean` - Chromeのアクセシビリティサポートが有効の場合は、`true`、そうでなければ
`false`。このAPIはスクリーンリーダーなどの支援テクノロジの使用が検知された場合、`true`を
返します。詳細は https://www.chromium.org/developers/design-documents/accessibility を参照して
ください。

### `app.setAboutPanelOptions(options)` _macOS_

* `options` Object
  * `applicationName` String (オプション) - アプリケーション名。
  * `applicationVersion` String (オプション) - アプリケーションのバージョン。
  * `copyright` String (オプション) - 著作権情報。
  * `credits` String (オプション) - 信用情報.
  * `version` String (オプション) - アプリケーションのビルドバージョン番号。

"このアプリケーションについて"パネルオプションを設定します。アプリケーションの `.plist`
で定義された値を上書きします。詳細は[Appleのドキュメント][about-panel-options]を参照して
ください。

### `app.commandLine.appendSwitch(switch[, value])`

* `switch` String - コマンドラインスイッチ
* `value` String (オプション) - 指定したスイッチの値

Chromiumのコマンドラインにスイッチ（ `value` はオプション）を追加します。

**注:** これは `process.argv` には影響しません。主に、開発者がChromiumの低レベルな
挙動をコントロールするのに使用します。

### `app.commandLine.appendArgument(value)`

* `value` String - コマンドラインに追加する引数。

Chromiumのコマンドラインに引数を追加します。引数は正しく引用符で囲まれます。

**注:** これは `process.argv` には影響しません。

### `app.dock.bounce([type])` _macOS_

* `type` String (オプション) - `critical` または `informational` を指定できます。
 既定値は `informational`。

`critical`を渡すと、アプリケーションがアクティブになるか、リクエストがキャンセルされる
まで、ドックアイコンがバウンスします。

`informational` を渡すと、ドックアイコンが1秒間バウンスします。しかし、アプリケーションが
アクティブになるか、リクエストがキャンセルされるまで、リクエストはアクティブのままです。

リクエストを表すIDを返します。

### `app.dock.cancelBounce(id)` _macOS_

* `id` Integer

`id`のバウンスをキャンセルします。

### `app.dock.downloadFinished(filePath)` _macOS_

* `filePath` String

filePathが"ダウンロード"フォルダにある場合、"ダウンロード"スタックをバウンスします。

### `app.dock.setBadge(text)` _macOS_

* `text` String

dockのバッジエリアに表示する文字列を設定します。

### `app.dock.getBadge()` _macOS_

`String` - dockのバッジ文字列を返します。

### `app.dock.hide()` _macOS_

ドックアイコンを隠します。

### `app.dock.show()` _macOS_

ドックアイコンを表示します。

### `app.dock.isVisible()` _macOS_

`Boolean` - ドックアイコンが可視か否かを返します。
`app.dock.show()` コールは非同期ですので、コール直後はこのメソッドが `true` を返さない場合があります。

### `app.dock.setMenu(menu)` _macOS_

* `menu` [Menu](menu.md)

アプリケーションの[ドックメニュー][dock-menu]を設定します。

### `app.dock.setIcon(image)` _macOS_

* `image` [NativeImage](native-image.md)

ドックアイコンに紐づく `image` を設定します。

[dock-menu]:https://developer.apple.com/library/mac/documentation/Carbon/Conceptual/customizing_docktile/concepts/dockconcepts.html#//apple_ref/doc/uid/TP30000986-CH2-TPXREF103
[tasks]:http://msdn.microsoft.com/en-us/library/windows/desktop/dd378460(v=vs.85).aspx#tasks
[app-user-model-id]: https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx
[CFBundleURLTypes]: https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/TP40009249-102207-TPXREF115
[LSCopyDefaultHandlerForURLScheme]: https://developer.apple.com/library/mac/documentation/Carbon/Reference/LaunchServicesReference/#//apple_ref/c/func/LSCopyDefaultHandlerForURLScheme
[handoff]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/Handoff/HandoffFundamentals/HandoffFundamentals.html
[activity-type]: https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSUserActivity_Class/index.html#//apple_ref/occ/instp/NSUserActivity/activityType
[unity-requiremnt]: ../tutorial/desktop-environment-integration.md#unity-launcher-shortcuts-linux
[JumpListBeginListMSDN]: https://msdn.microsoft.com/en-us/library/windows/desktop/dd378398(v=vs.85).aspx
[about-panel-options]: https://developer.apple.com/reference/appkit/nsapplication/1428479-orderfrontstandardaboutpanelwith?language=objc
