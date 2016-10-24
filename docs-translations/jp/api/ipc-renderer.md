# ipcRenderer

> レンダラプロセスからメインプロセスに非同期に通信します。

`ipcRenderer` モジュールは、
[EventEmitter](https://nodejs.org/api/events.html#events_class_eventemitter) クラスのインスタンスです。レンダラプロセス（ウェブページ）からメインプロセスにメッセージを同期的、非同期的に送信できるいくつかのメソッドを提供します。メインプロセスからの返信を受け取ることもできます。

コード例は [ipcMain](ipc-main.md) を参照してください。

## メッセージのリスニング

`ipcRenderer`モジュールは、イベントをリッスンする以下のメソッドを持っています。

### `ipcRenderer.on(channel, listener)`

* `channel` String
* `listener` Function

`channel` をリッスンし、新たなイベントが到着すると、`listener` が `listener(event, args...)` の形でコールされます。

### `ipcRenderer.once(channel, listener)`

* `channel` String
* `listener` Function

イベントに `listener` 関数を一度だけ追加します。この `listener` は、次回メッセージが
`channel` に送信された時にのみ実行され、その後削除されます。

### `ipcRenderer.removeListener(channel, listener)`

* `channel` String
* `listener` Function

指定した `channel` のリスナー配列から指定した `listener` を削除します。

### `ipcRenderer.removeAllListeners([channel])`

* `channel` String (オプション)

すべてのリスナー、または指定した `channel` のリスナーを削除します。

## メッセージの送信

`ipcRenderer` モジュールは、イベントを送信する以下のメソッドを持っています。

### `ipcRenderer.send(channel[, arg1][, arg2][, ...])`

* `channel` String
* `arg` (オプション)

`channel` 経由でメインプロセスに非同期にイベントを送信します。任意の引数を送信することも
できます。引数は内部でJSON形式にシリアル化されますので、複数の関数やプロトタイプチェインは
含まれません。

メインプロセスは、`ipcMain`モジュールで `channel` をリッスンすることによりこれを処理します。

### `ipcRenderer.sendSync(channel[, arg1][, arg2][, ...])`

* `channel` String
* `arg` (optional)

`channel` 経由でメインプロセスに同期的にイベントを送信します。任意の引数を送信することも
できます。引数は内部でJSON形式にシリアル化されますので、複数の関数やプロトタイプチェインは
含まれません。

メインプロセスは、`ipcMain`モジュールで `channel` をリッスンすることによりこれを処理し、
`event.returnValue` を設定することで返信します。

**注:**  同期メッセージの送信は、レンダラプロセス全体をブロックします。何をしているか理解
していない限り、決してこれを使うべきではありません。

### `ipcRenderer.sendToHost(channel[, arg1][, arg2][, ...])`

* `channel` String
* `arg` (optional)

`ipcRenderer.send` と同じですが、イベントはメインプロセスではなく、ホストページの
`<webview>`エレメントに送信されます。
