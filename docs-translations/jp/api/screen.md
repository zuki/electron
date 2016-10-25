# screen

> 画面サイズ、ディスプレイ、カーソル位置などの情報を読み取ります。

`app`モジュールの`ready`イベントが出力されるまで、このモジュールをrequireしたり、使うことはできません。

`screen`は [EventEmitter](http://nodejs.org/api/events.html#events_class_events_eventemitter)です。

**注:** レンダラ―/デベロッパーツールでは、`window.screen`は予約済みのDOMプロパティですので、`let {screen} = require('electron')`と書いても動作しません。

画面全体に広がるウィンドウを作成する例：

```javascript
const electron = require('electron')
const {app, BrowserWindow} = electron

let win

app.on('ready', () => {
  const {width, height} = electron.screen.getPrimaryDisplay().workAreaSize
  win = new BrowserWindow({width, height})
  win.loadURL('https://github.com')
})
```

外部ディスプレイにウィンドウを作成する別の例：

```javascript
const electron = require('electron')
const {app, BrowserWindow} = require('electron')

let win

app.on('ready', () => {
  let displays = electron.screen.getAllDisplays()
  let externalDisplay = displays.find((display) => {
    return display.bounds.x !== 0 || display.bounds.y !== 0
  })

  if (externalDisplay) {
    win = new BrowserWindow({
      x: externalDisplay.bounds.x + 50,
      y: externalDisplay.bounds.y + 50
    })
    win.loadURL('https://github.com')
  }
})
```

## イベント

`screen`モジュールは以下のイベントを出力します:

### イベント: 'display-added'

返り値:

* `event` Event
* `newDisplay` [Display](structures/display.md)

`newDisplay`が追加された時に、出力されます。

### イベント: 'display-removed'

返り値:

* `event` Event
* `oldDisplay` [Display](structures/display.md)

`oldDisplay`が削除された時に、出力されます。

### イベント: 'display-metrics-changed'

返り値:

* `event` Event
* `display` [Display](structures/display.md)
* `changedMetrics` String[]

`display`で1つ以上のメトリックが変わった時に、出力されます。`changedMetrics`は、変更を説明する文字列の配列です。変更内容には`bounds`と`workArea`, `scaleFactor`、 `rotation`があり得ます。

## メソッド

`screen`モジュールは以下のメソッドを持っています:

### `screen.getCursorScreenPoint()`

`Object`:
* `x` Integer
* `y` Integer

現在のマウスの絶対位置を返します。

### `screen.getPrimaryDisplay()`

`Display` - プライマリディスプレイを返します。

### `screen.getAllDisplays()`

`Display[]` - 現在利用可能なディスプレイの配列を返します。

### `screen.getDisplayNearestPoint(point)`

* `point` Object
  * `x` Integer
  * `y` Integer

`Display` - 指定したポイントにもっとも近いディスプレイを返します。

### `screen.getDisplayMatching(rect)`

* `rect` [Rectangle](structures/rectangle.md)

`Display` - 指定した範囲ともっとも交差しているディスプレイを返します。
