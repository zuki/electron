# ipcMain

> メインプロセスからレンダラプロセスに非同期に通信します。

`ipcMain`モジュールは[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter)クラスのインスタンスです。メインプロセスで使うと、レンダラプロセス（ウェブページ）から送られた非同期・同期メッセージを処理できます。レンダラから送信されたメッセージはこのモジュールに出力されます。

## メッセージの送信

メインプロレスからレンダラプロセスへメッセージを送信することも可能です。さらなる情報は、[webContents.send][web-contents-send] を参照してください。

* メッセージを送信するとき、イベント名は`channel`です。
* 同期的にメッセージを返信するには、`event.returnValue`を設定する必要があります。
* 送信者に非同期に返信するには、`event.sender.send(...)`が使えます。

レンダラプロセスとメインプロセス間でメッセージの送信とハンドリングをする例です:

```javascript
// メインプロセスにおいて
const {ipcMain} = require('electron')
ipcMain.on('asynchronous-message', (event, arg) => {
  console.log(arg)  // "ping" を出力
  event.sender.send('asynchronous-reply', 'pong')
})

ipcMain.on('synchronous-message', (event, arg) => {
  console.log(arg)  //  "ping" を出力
  event.returnValue = 'pong'
})
```

```javascript
// レンダラプロセス（ウェブページ）において
const {ipcRenderer} = require('electron')
console.log(ipcRenderer.sendSync('synchronous-message', 'ping')) // "ping" を出力

ipcRenderer.on('asynchronous-reply', (event, arg) => {
  console.log(arg) // "pong" を出力
})
ipcRenderer.send('asynchronous-message', 'ping')
```

## メッセージのリスニング

`ipcMain`モジュールはイベントをリッスンする以下のメソッドを持っています。

### `ipcMain.on(channel, listener)`

* `channel` String
* `listener` Function

`channel` をリッスンし、新たなイベントが到着すると、`listener` が `listener(event, args...)` の形でコールされます。

### `ipcMain.once(channel, listener)`

* `channel` String
* `listener` Function

イベントに `listener` 関数を一度だけ追加します。この `listener` は、次回メッセージが
`channel` に送信された時にのみ実行され、その後削除されます。

### `ipcMain.removeListener(channel, listener)`

* `channel` String
* `listener` Function

指定した `channel` のリスナー配列から指定した `listener` を削除します。

### `ipcMain.removeAllListeners([channel])`

* `channel` String (オプション)

すべてのリスナー、または指定した `channel` のリスナーを削除します。

## イベントオブジェクト

`callback`に渡される`event`オブジェクトは、次のメソッドを持ちます。

### `event.returnValue`

同期メッセージで返す値をこれに設定します。

### `event.sender`

メッセージを送信した `webContents`を返します。非同期にメッセージに返信するために `event.sender.send` をコールできます。さらなる情報は、[webContents.send][web-contents-send]を参照してください。

[web-contents-send]: web-contents.md#webcontentssendchannel-arg1-arg2-
