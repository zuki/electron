# BrowserWindow

> ブラウザウィンドウを作成・制御します。

```javascript
// メインプロセスで
const {BrowserWindow} = require('electron')

// あるいは、レンダラプロセスで `remote` を使って
// const {BrowserWindow} = require('electron').remote

let win = new BrowserWindow({width: 800, height: 600})
win.on('closed', () => {
  win = null
})

// リモートURLをロードする
win.loadURL('https://github.com')

// あるいは、ローカルHTMLファイルをロードする
win.loadURL(`file://${__dirname}/app/index.html`)
```

## フレームレスウィンドウ

クロームを持たないウィンドウ、または、任意の形の透明なウィンドウを作成するには、[フレームレスウィンドウ](frameless-window.md) APIが使用できます。

## ウィンドウをグレイスフルに表示する

ページをウィンドウに直接ロードする際、ユーザがページのローディングの進捗を見ることになります。これはネイティブアプリには良い体験ではありません。チラツキなしにウィンドウを表示するには、状況に応じて2つの解決法があります。

### `ready-to-show` イベントを使用する

ページをロード中、レンダラプロセスが初めて描画を終えると`ready-to-show`イベントが発火されます。このイベント後にウィンドウを表示するとチラツキはおきません。

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow({show: false})
win.once('ready-to-show', () => {
  win.show()
})
```

このイベントは通常`did-finish-load`イベントの後に発火しますが、多くのリモート資源を持つページの場合は、`did-finish-load`イベント前に発火することもあります。

### `backgroundColor`を設定する

複雑なアプリでは`ready-to-show`の発火が非常に遅くなり、アプリが遅いと感じさせる可能性があります。この場合は、ウィンドウを直ちに表示させ、アプリの背景に近い`backgroundColor`を使用します。

```javascript
const {BrowserWindow} = require('electron')

let win = new BrowserWindow({backgroundColor: '#2e2c29'})
win.loadURL('https://github.com')
```

`ready-to-show`イベントを使用するアプリであっても、よりネイティブアプリらしく感じさせるために、`backgroundColor`の設定が推奨されることに注意してください。

## 親ウィンドウと子ウィンドウ

`parent`オプションを使うことで、子ウィンドウを作成することができます。

```javascript
const {BrowserWindow} = require('electron')

let top = new BrowserWindow()
let child = new BrowserWindow({parent: top})
child.show()
top.show()
```

`child`ウィンドウは常に`top`ウィンドウの上に表示されます。

### モーダルウィンドウ

モーダルウィンドウは親ウィンドウを無効にする子ウィンドウです。モーダルウィンドウを作成するには、`parent`オプションと `modal`オプションの両方をセットしなければなりません。

```javascript
const {BrowserWindow} = require('electron')

let child = new BrowserWindow({parent: top, modal: true, show: false})
child.loadURL('https://github.com')
child.once('ready-to-show', () => {
  child.show()
})
```

### プラットフォーム留意点

* macOSでは、親ウィンドウが動いた場合、子ウィンドウは親ウィンドウとの相対位置をキープします。一方、WindowsとLinuxでは子ウィンドウは動きません。
* Windowsでは、親ウィンドウの動的変更はサポートされていません。
* Linuxでは、モーダルウィンドウのタイプは`dialog`に変更されます。
* Linuxでは、多くのデスクトップ環境でモーダルウィンドウを隠す機能がサポートされていません。

## クラス: BrowserWindow

`BrowserWindow` は [EventEmitter](http://nodejs.org/api/events.html#events_class_events_eventemitter)です。

`options`により設定されるネイティブプロパティを持つ`BrowserWindow`を新たに作成します。

### `new BrowserWindow([options])`

* `options` Object
  * `width` Integer - ウィンドウのピクセル単位の幅。デフォルトは `800`。
  * `height` Integer - ウィンドウのピクセル単位の高さ。デフォルトは `600`。
  * `x` Integer (yが指定された場合は**必須**) - ウィンドウのスクリーンからの左オフセット。
  　デフォルトはウィンドウの中心。
  * `y` Integer (xが指定された場合は**必須**) - ウィンドウのスクリーンからの上オフセット。
    デフォルトはウィンドウの中心。
  * `useContentSize` Boolean - `width` と `height` はウェブページサイズが使用されます。
    これは実際のウィンドウサイズがウィンドウのフレームサイズを含むので、いくらか大きくなる
    ことを意味します。デフォルトは `false`。
  * `center` Boolean - ウィンドウをスクリーンの中心に表示します。
  * `minWidth` Integer - ウィンドウの最小幅。デフォルトは `0`。
  * `minHeight` Integer - ウィンドウの最小高さ。デフォルトは `0`。
  * `maxWidth` Integer - ウィンドウの最大幅。デフォルトは制限なし。
  * `maxHeight` Integer - ウィンドウの最大高さ。デフォルトは制限なし。
  * `resizable` Boolean - ウィンドウサイズを可変にするか否か。デフォルトは `true`。
  * `movable` Boolean - ウィンドウを移動可動にするか否か。これはLinuxでは
    実装されていません。デフォルトは `true`。
  * `minimizable` Boolean - ウィンドウを最小化可能にするか否か。これはLinuxでは
    実装されていません。デフォルトは `true`。
  * `maximizable` Boolean - ウィンドウを最大化可能にするか否か。これはLinuxでは
    実装されていません。デフォルトは `true`。
  * `closable` Boolean - ウィンドウを閉じることを可能にするか否か。これはLinuxでは
    実装されていません。デフォルトは `true`。
  * `focusable` Boolean - ウィンドウにフォーカスすることを可能にするか否か。
    デフォルトは `true`。Windowsでは、`focusable: false`の設定は、
    `skipTaskbar: true`の設定も意味します。 Linuxでは、`focusable: false`の設定は
    ウィンドウとwmとの相互作用を止めることになります。そのため、すべてのワークスペースで
    そのウィンドウが常に一番上に表示されます。
  * `alwaysOnTop` Boolean - ウィンドウが常にほかのウィンドウの上に表示される
    ようにするか否か。デフォルトは `false`。
  * `fullscreen` Boolean - ウィンドウをフルスクリーンで表示するべきか否か。
    明示的に`false`を設定すると、フルスクリーンボタンが隠されるか、macOSでは無効になります。
    デフォルトは `false`。
  * `fullscreenable` Boolean - ウィンドウをフルスクリーンモードに入れられるか否か。W
    macOSでは、最大化/ズームボタンがフルスクリーンモードをトグルするかウィンドウを
    最大化するかも同時に指定します。デフォルトは `true`。
  * `skipTaskbar` Boolean - タスクバーにウィンドウを表示するか否か。
    デフォルトは `false`。
  * `kiosk` Boolean - キオスクモード。デフォルトは `false`。
  * `title` String - デフォルトウィンドウタイトル。デフォルトは `"Electron"`。
  * `icon` [NativeImage](native-image.md) - ウィンドウアイコン。Windowsでは
    最適なビジュアル効果を得るために`ICON`アイコンの使用を勧めます。未定義のままにして
    おくことも可能で、その場合は実行ファイルのアイコンが使用されます。
  * `show` Boolean - ウィンドを作成時に表示するか否か。デフォルトは `true`。
  * `frame` Boolean - [フレームレスウィンドウ](frameless-window.md)を作成する
    場合に `false` を指定します。デフォルトは `true`。
  * `parent` BrowserWindow - 親ウィンドウを指定します。デフォルトは `null`。
  * `modal` Boolean - モーダルウィンドウか否か。このウィンドウが子ウインドウの
    場合のみ機能します。デフォルトは `false`、
  * `acceptFirstMouse` Boolean - ウィンドウを一斉にアクティブにするシングルマウスダウン
    イベントをウィブビューが受け付けるか否か。デフォルトは `false`。
  * `disableAutoHideCursor` Boolean - 入力時にカーソルを隠すか否か。
    デフォルトは `false`。
  * `autoHideMenuBar` Boolean - `Alt`キーが押されていない限り、メニューバーを
    自動的に隠します。デフォルトは `false`。
  * `enableLargerThanScreen` Boolean - ウィンドウをスクリーンより大きく拡大することを
    可能にします。デフォルトは `false`。
  * `backgroundColor` String -ウィンドウの背景色。 `#66CD00`、`#FFF`、`#80FFFFFF`
    のような16進数の値で指定します（アルファ値もサポートしています）。
    デフォルトは `#FFF` (白)。
  * `hasShadow` Boolean - ウィンドウが影を持つか否か。実装されているのはmacOSのみです。
    デフォルトは `true`。
  * `darkTheme` Boolean - ダークテーマのウィンドウの使用を強制します。いくつかの
    GTK+3デスクトップ環境でのみ機能します。デフォルトは `false`。
  * `transparent` Boolean - ウィンドウを [透明](frameless-window.md)にします。
    デフォルトは `false`。
  * `type` String - ウィンドウのタイプ。デフォルトはノーマルウィンドウ。
    詳細は下記を参照してください。
  * `titleBarStyle` String - ウィンドウタイトルバーのスタイル。詳細は下記を参照してください。
  * `thickFrame` Boolean - Windowsで、フレームレスウィンドウに標準的なウィンドウフレームを
  　追加する `WS_THICKFRAME` スタイルを使用します。`falase` を指定すると、
    ウィンドウの影とウィンドウアニメーションを削除します。デフォルトは `true`。
  * `webPreferences` Object - ウェブページの外観を設定します。詳細は下記を参照してください。

`minWidth`/`maxWidth`/`minHeight`/`maxHeight`で最小または最大のウィンドウサイズを指定する際に
制約するのはユーザだけです。`setBounds`/`setSize`や`BrowserWindow`のコンストラクタに
サイズ制約に従わないサイズを渡すことは妨げません。

`type` オプションに設定可能な値とふるまいはプラットフォームに依存します。設定可能な値は
次のとおりです。

* Linuxで指定可能なタイプは、`desktop`, `dock`, `toolbar`, `splash`, `notification`です。
* macOSで指定可能なタイプは、`desktop`, `textured`です。
  * `textured` タイプは、金属調のアピアランスを追加します
    (`NSTexturedBackgroundWindowMask`)。
  * `desktop` タイプはウィンドウをデスクトップバックグラウンドウィンドウレベルに置きます
    (`kCGDesktopWindowLevel - 1`)。デスクトップウィンドウは、focus, keyboard, mouseの
    各イベントは受け取らないことに注意してください。ただし、`globalShortcut` を使うことで
    入力を慎重に受け取ることが可能です。
* Windowsで指定可能なタイプは、`toolbar`です。

`titleBarStyle` オプションに設定可能な値は次のとおりです。

* `default`または設定しない。標準的なグレイで不透明なMacのタイトルバーになります。
* `hidden` は、タイトルバーは隠され、フルサイズのコンテンツウィンドウになります。ただし、
  タイトルバーは依然として左上部に標準的なウィンドウコントール("信号機")を持ちます。
* `hidden-inset` は、ウィンドウの端に信号機ボタンがわずかに埋め込まれた別の形態の隠された
  タイトルバーになります。

`webPreferences` オプションは以下のプロパティを持つことができるオブジェクトです。

* `devTools` Boolean - Whether to enable DevToolsを有効にするか否か。`false`を
  設定すると `BrowserWindow.webContents.openDevTools()` を使ってDevToolsを
  開くことができなくなります。 デフォルトは `true`。
* `nodeIntegration` Boolean - Nodeの統合を有効にするか否か。デフォルトは `true`。
* `preload` String - そのページで他のスクリプトが実行される前にロードされるスクリプトを
  指定します。このスクリプトはNode統合オプションの設定にかかわらず常にNode APIにアクセス
  できます。値はスクリプトの完全ファイルパスでなければなりません。Node統合が無効になって
  いる場合、プリロードするスクリプトは、Nodeのグローバルシンボルをグローバルスコープに
  再投入することができます。[この](process.md#event-loaded)例を参照してください。
* `session` [Session](session.md#class-session) - ページで使用するセッションを
  設定します。Sessionオブジェクトを直接渡す代わりに、文字列を受け取る`partition`
  オプションを使用することもできます。`session` と `partition` の両方が設定された
  場合は、`session` が優先されます。デフォルトは、デフォルトセッションです。
* `partition` String - セッションのパーティション文字列によりページで使用するセッションを
  設定します。`partition`が`persist:`で始まっている場合、同じ`partition`を持つ
  アプリケーションのすべてのページで利用可能は永続的セッションをページは使用します。
  `persist:`で始まらない場合、ページはインメモリセッションを使用します。同じ`partition`を
  与えることにより、複数のページで同じセッションを共有することができます。デフォルトは
  デフォルトセッションです。
* `zoomFactor` Number - ページのデフォルトズームファクタで、`3.0` は `300%` を
  表します。デフォルトは `1.0` です。
* `javascript` Boolean - JavaScriptのサポートを有効にします。デフォルトは `true`。
* `webSecurity` Boolean - `false`にすると、同一生成元ポリシーを無効にします（通常テスト
  用ウェブサイトで使用）。また、`allowDisplayingInsecureContent` と
  `allowRunningInsecureContent` がユーザにより設定されていない場合、両者を `true` に
  設定します。デフォルトは `true`。
* `allowDisplayingInsecureContent` Boolean - httpsページで、http URLからの画像などの
  コンテンツの表示を可能にします。デフォルトは `false`。
* `allowRunningInsecureContent` Boolean - httpsページで、http URLからのJavaScriptや
  CSS、プラグインの実行を可能にします。デフォルトは `false`。
* `images` Boolean - 画像のサポートを有効にします。デフォルトは `true`。
* `textAreasAreResizable` Boolean - Make TextArea要素をサイズ変更可能にします。
  デフォルトは `true`。
* `webgl` Boolean - WebGLのサポートを有効にします。デフォルトは `true`。
* `webaudio` Boolean - WebAudioのサポートを有効にします。デフォルトは `true`。
* `plugins` Boolean - プラグインを有効にするか否か。デフォルトは `false`。
* `experimentalFeatures` Boolean - Chromiumの実験的機能を有効にします。
  デフォルトは `false`。
* `experimentalCanvasFeatures` Boolean - Chromiumの実験的canvas機能を有効にします。
  デフォルトは `false`。
* `scrollBounce` Boolean - macOSでスクロールのバウンス（輪ゴム）効果を有効にします。
  デフォルトは `false`。
* `blinkFeatures` String - 有効化する機能名のリスト。`CSSVariables,KeyboardEventKey`
  のように、機能名を`,`で区切って設定します。サポートされている機能名の完全なリストは
  [RuntimeEnabledFeatures.in][blink-feature-string]ファイルにあります。
* `disableBlinkFeatures` String - 無効化する機能名のリスト。`CSSVariables,KeyboardEventKey`
  のように、機能名を`,`で区切って設定します。サポートされている機能名の完全なリストは
  [RuntimeEnabledFeatures.in][blink-feature-string]ファイルにあります。
* `defaultFontFamily` Object - フォントファミリーのデフォルトフォントを設定します。
  * `standard` String - デフォルトは `Times New Roman`。
  * `serif` String - デフォルトは `Times New Roman`。
  * `sansSerif` String - デフォルトは `Arial`。
  * `monospace` String - デフォルトは `Courier New`。
* `defaultFontSize` Integer - デフォルトは `16`。
* `defaultMonospaceFontSize` Integer - デフォルトは `13`。
* `minimumFontSize` Integer - デフォルトは `0`。
* `defaultEncoding` String - デフォルトは `ISO-8859-1`。
* `backgroundThrottling` Boolean - ページがバックグラウンドに移行する際、アニメーションと
  タイマーを抑えるか否か。デフォルトは `true`。
* `offscreen` Boolean - ブラウザウィンドウでオフスクリーンレンダリングを有効にするか否か。
  デフォルトは `false`。
* `sandbox` Boolean - ChromiumのOSレベルのサンドボックスを有効にするか否か。

### インスタンスイベント

`new BrowserWindow` で作成されたオブジェクトは以下のイベントを出力します。

**注:** 特定のオペレーションシステムでしか利用できないイベントがあります。その場合は、
ラベルで示してあります。

#### イベント: 'page-title-updated'

戻り値:

* `event` Event
* `title` String

ドキュメントがそのタイトルを変更した時に出力されます。`event.preventDefault()`を呼び出すと、
ネイティブウィンドウのタイトルを変更できなくします。

#### イベント: 'close'

戻り値:

* `event` Event

ウィンドウがクローズされようとしている時に出力されます。このイベントは、DOMの`beforeunload`
イベントと`unload`イベントの前に出力されます。`event.preventDefault()`を呼び出すと
ウィンドウのクローズをキャンセルします。

通常、ウィンドウをクローズするか否かの決定には ウィンドウがリロードされる時にも呼び出される
`beforeunload` ハンドラの使用を考えるでしょう。Electronでは、`undefined` 以外の
値を返すとクローズをキャンセルします。以下は例です。

```javascript
window.onbeforeunload = (e) => {
  console.log('I do not want to be closed')

  // メッセージボックスがユーザに表示される通常のブラウザとは異なり、
  // 非voidの値を返すと黙ってクローズをキャンセルします。
  // ユーザにアプリケーションのクローズを確認させるためにはを
  // dialog APIの使用を勧めます。
  e.returnValue = false
}
```

#### イベント: 'closed'

ウィンドウが閉じられる時に出力されます。このイベントを受け取った後は、このウィンドウへの
参照を削除し、それ以上の使用を避けなければなりません。

#### イベント: 'unresponsive'

ウェブページが反応不能になった時に出力されます。

#### イベント: 'responsive'

反応不能のウェブページが再び反応可能になった時に出力されます。

#### イベント: 'blur'

ウィンドウがフォーカスを失った時に出力されます。

#### イベント: 'focus'

ウィンドウがフォーカスを得た時に出力されます。

#### イベント: 'show'

ウィンドウが表示された時に出力されます。

#### イベント: 'hide'

ウィンドウが非表示になった時に出力されます。

#### イベント: 'ready-to-show'

ウェブページがレンダリングされ、ウィンドウがチラツキなしに表示できるようになった時に
出力されます。

#### イベント: 'maximize'

ウィンドウが最大化された時に出力されます。

#### イベント: 'unmaximize'

ウィンドウが最大化状態から脱した時に出力されます。

#### イベント: 'minimize'

ウィンドウが最小化された時に出力されます。

#### イベント: 'restore'

ウィンドウが最小化状態から回復した時に出力されます。

#### イベント: 'resize'

ウィンドウがリサイズされている時に出力されます。

#### イベント: 'move'

ウィンドウが新たな位置に移動されている時に出力されます。

__注__: macOSでは、このイベントは単に`moved`の別名です。

#### イベント: 'moved' _macOS_

ウィンドウが新たな位置に移動された時に一度出力されます。

#### イベント: 'enter-full-screen'

ウィンドウがフルスクリーン状態に入った時に出力されます。

#### イベント: 'leave-full-screen'

ウィンドウがフルスクリーン状態を離れた時に出力されます。

#### イベント: 'enter-html-full-screen'

HTML APIが引き金となり、ウィンドウがフルスクリーン状態に入った時に出力されます。

#### イベント: 'leave-html-full-screen'

HTML APIが引き金となり、ウィンドウがフルスクリーン状態を離れた時に出力されます。

#### イベント: 'app-command' _Windows_

戻り値:

* `event` Event
* `command` String

[アプリケーションコマンド](https://msdn.microsoft.com/en-us/library/windows/desktop/ms646275(v=vs.85).aspx)が起動された時に
出力されます。これは通常、キーボードのメディアキーやブラウザコマンドに関係しますが、
Windowsのある種のマウスに組み込まれれている"Back"ボタンにも関係します。

コマンドは小文字化され、ハイフンはアンダースコアに変換されます。また、接頭辞 `APPCOMMAND_` は
取り除かれます。たとえば、`APPCOMMAND_BROWSER_BACKWARD` は `browser-backward` として
出力されます。

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()
win.on('app-command', (e, cmd) => {
  // ユーザがマウスのbackボタンを押した時に元いたページに戻す
  if (cmd === 'browser-backward' && win.webContents.canGoBack()) {
    win.webContents.goBack()
  }
})
```

#### イベント: 'scroll-touch-begin' _macOS_

スクロールホイールイベントが始まった時に出力されます。

#### イベント: 'scroll-touch-end' _macOS_

スクロールホイールイベントが終わった時に出力されます。

#### イベント: 'scroll-touch-edge' _macOS_

スクロールホイールイベントが要素の橋に到達した報告した時に出力されます。

#### イベント: 'swipe' _macOS_

戻り値:

* `event` Event
* `direction` String

3本指スワイプされると出力されます。あり得る`direction`値は、`up`, `right`, `down`, `left`です。

### スタティックメソッド

`BrowserWindow` クラスは次のスタティックメソッドを持っています。

#### `BrowserWindow.getAllWindows()`

`BrowserWindow[]` - 開かれているすべてのブラウザウィンドウの配列を返します。

#### `BrowserWindow.getFocusedWindow()`

`BrowserWindow` - このアプリケーションでフォーカスされているウィンドウを返します。そうでない場合は、`null`を返します。

#### `BrowserWindow.fromWebContents(webContents)`

* `webContents` [WebContents](web-contents.md)

`BrowserWindow` - 指定された `webContents` を所有するウィンドウを返します.

#### `BrowserWindow.fromId(id)`

* `id` Integer

`BrowserWindow` - 指定された `id` のウィンドウを返します。

#### `BrowserWindow.addDevToolsExtension(path)`

* `path` String

Adds DevTools extension located at `path` にあるDevToolエクステンションを追加し、
エクステンション名を返します。

エクステンションは記憶されますので、このAPIの呼び出しが必要なのは一回だけです。
このAPIはプログラミングで使用するものではありません。すでにロードされているエクステンションを
追加しようとすると、このメソッドはエクステンション名を返さず、コンソールに警告ログを出力します。

また、エクステンションのマニュフェストがない、または不完全な場合も、このメソッドはエクステンション名を
返しません。

**注:** このAPIは `app` モジュールの `ready` イベントが出力される前には呼び出すことができません。

#### `BrowserWindow.removeDevToolsExtension(name)`

* `name` String

指定した名前を持つDevToolsエクステンションを削除します。

**注:** このAPIは `app` モジュールの `ready` イベントが出力される前には呼び出すことができません。

#### `BrowserWindow.getDevToolsExtensions()`

`Object` - キーがエクステンション名、値が `name` プロパティと `version` プロパティを
持つオブジェクトであるオブジェクトを返します。

次のコードを実行すると、DevToolsエクステンションがインストールされているかチェックできます。

```javascript
const {BrowserWindow} = require('electron')

let installed = BrowserWindow.getDevToolsExtensions().hasOwnProperty('devtron')
console.log(installed)
```

**注:** このAPIは `app` モジュールの `ready` イベントが出力される前には呼び出すことができません。

### インスタンスプロパティ

`new BrowserWindow` で作成されたオブジェクトは次のプロパティを持っています。

```javascript
const {BrowserWindow} = require('electron')
// In this example `win` is our instance
let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('https://github.com')
```

#### `win.webContents`

このウィンドウが所有する `WebContents` オブジェクト。ウェブページに関連するすべてのイベントと
操作は、このプロパティ経由で実行されます。

そのメソッドとイベントについては [`webContents` documentation](web-contents.md) を
参照してください。

#### `win.id`

ウィンドウのユニークIDを表す `Integer` です。

### インスタンスメソッド

`new BrowserWindow` で作成されたオブジェクトは次のインスタンスメソッドを持っています。

**注:** 特定のオペレーションシステムでしか利用できないメソッドがあります。その場合は、
ラベルで示してあります。

#### `win.destroy()`

ウィンドウを強制的にクローズします。ウェブページに対する `unload` イベントも `beforeunload`
イベントも出力されません。また、このウィンドウに対する `close` イベントも出力されません。しかし、
`closed` イベントが出力されることは保証します。

#### `win.close()`

ウィンドウをクローズしようと試みます。これはユーザが手作業でウィンドウのクローズボタンをクリックする
のと同じ効果を持ちます。しかし、ウェブページはクローズをキャンセルする場合があります。
[close イベント](#event-close)を参照してください。

#### `win.focus()`

ウィンドウをフォーカスします。

#### `win.blur()`

ウィンドウからフォーカスを削除します。

#### `win.isFocused()`

`Boolean` - ウィンドウがフォーカスされているか否かを返します。

#### `win.isDestroyed()`

`Boolean` - ウィンドウが破棄されたか否かを返します。

#### `win.show()`

ウィンドウを表示し、フォーカスします。

#### `win.showInactive()`

ウィンドウを表示しますが、フォーカスはしません。

#### `win.hide()`

ウィンドウを隠します。

#### `win.isVisible()`

`Boolean` - ウィンドウがユーザに見えるか否かを返します。

#### `win.isModal()`

`Boolean` - カレントウィンドウがモーダルウィンドウであるか否かを返します。

#### `win.maximize()`

ウィンドウを最大化します。

#### `win.unmaximize()`

ウィンドウの最大化を解除します。

#### `win.isMaximized()`

`Boolean` - ウィンドウが最大化されているか否かを返します。

#### `win.minimize()`

ウィンドウを最小化します。プラットフォームによっては最小化されたウィンドウはドックに表示されます。

#### `win.restore()`

ウィンドウを最小化された状態から元の状態に戻します。

#### `win.isMinimized()`

`Boolean` - `Boolean` - ウィンドウが最小化されているか否かを返します。

#### `win.setFullScreen(flag)`

* `flag` Boolean

ウィンドウをフルスクリーンモードにするか否かを設定します。

#### `win.isFullScreen()`

Returns `Boolean` - ウィンドウがフルスクリーンモードであるか否かを返します。

#### `win.setAspectRatio(aspectRatio[, extraSize])` _macOS_

* `aspectRatio` Float - コンテンツビューの一部で保持するアスペクト比。
* `extraSize` Object (オプション) - アスペクト比を保持する際に含めないエクストラサイズ。
  * `width` Integer
  * `height` Integer

これはウィンドウにアスペクト比を保持させます。エクストラサイズは、開発者がアスペクト比の計算に
含めないスペース（ピクセルで指定）を持てるようにします。このAPIは、ウィンドウサイズとその
コンテンツのサイズの違いは織り込み済みです。

HDビデオプレーヤーとそのコントロールを持つ通常のウィンドウを考えます。おそらくプレーヤーの
左端には15ピクセルのコントロールが、右端には25ピクセルのコントロールが、下には50ピクセルの
コントロールがあります。プレーヤー自身の16:9のアスペクト比（HD@1920x1080で標準的なアスペクト比）
を保持するためには、引数を 16/9 と [ 40, 50 ] としてこの関数を呼ぶことになります。第2引数は
余分な幅と高さがコンテンツビュー内のどこにあるかは気にしません。それらがあることだけに関心があります。
コンテンツビュー全体の中にある余分な幅と高さを合計するだけで構いません。

#### `win.setBounds(bounds[, animate])`

* `bounds` [Rectangle](structures/rectangle.md)
* `animate` Boolean (オプション) _macOS_

ウィンドウを指定されたboundsにリサイズして移動します。

#### `win.getBounds()`

[`Rectangle`](structures/rectangle.md)を返します。

#### `win.setContentBounds(bounds[, animate])`

* `bounds` [Rectangle](structures/rectangle.md)
* `animate` Boolean (オプション) _macOS_

ウィンドウのクライアント領域（たとえば、ウェブページ）を指定されたboundsに
リサイズして移動します。

#### `win.getContentBounds()`

[`Rectangle`](structures/rectangle.md)を返します。

#### `win.setSize(width, height[, animate])`

* `width` Integer
* `height` Integer
* `animate` Boolean (オプション) _macOS_

ウィンドウの幅を `width`、高さを `height` にリサイズします。

#### `win.getSize()`

`Integer[]` - ウィンドウの幅と高さを含む配列を返します。

#### `win.setContentSize(width, height[, animate])`

* `width` Integer
* `height` Integer
* `animate` Boolean (オプション) _macOS_

ウィンドウのクライアント領域（たとえば、ウェブページ）の幅を `width`、高さを `height` に
リサイズします。

#### `win.getContentSize()`

`Integer[]` - ウィンドウのクライアント領域の幅と高さを含む配列を返します。

#### `win.setMinimumSize(width, height)`

* `width` Integer
* `height` Integer

ウィンドウの最小サイズの幅を `width`、高さを `height` に設定します。

#### `win.getMinimumSize()`

`Integer[]` - ウィンドウの最小サイズの幅と高さを含む配列を返します。

#### `win.setMaximumSize(width, height)`

* `width` Integer
* `height` Integer

ウィンドウの最大サイズの幅を `width`、高さを `height` に設定します。

#### `win.getMaximumSize()`

`Integer[]` - ウィンドウの最大サイズの幅と高さを含む配列を返します。

#### `win.setResizable(resizable)`

* `resizable` Boolean

ウィンドウをユーザが手作業でリサイズできるか否かを設定します。

#### `win.isResizable()`

`Boolean` - ウィンドウをユーザが手作業でリサイズできるか否かを返します。

#### `win.setMovable(movable)` _macOS_ _Windows_

* `movable` Boolean

ウィンドウをユーザが移動できるか否かを設定します。Linuxでは何もしません。

#### `win.isMovable()` _macOS_ _Windows_

`Boolean` - ウィンドウをユーザが移動できるか否かを返します。

Linuxでは常に `true` を返します。

#### `win.setMinimizable(minimizable)` _macOS_ _Windows_

* `minimizable` Boolean

ウィンドウをユーザが手作業で最小化できるか否かを設定します。Linuxでは何もしません。

#### `win.isMinimizable()` _macOS_ _Windows_

`Boolean` - ウィンドウをユーザが手作業で最小化できるか否かを返します。

Linuxでは常に `true` を返します。

#### `win.setMaximizable(maximizable)` _macOS_ _Windows_

* `maximizable` Boolean

ウィンドウをユーザが手作業で最大化できるか否かを設定します。Linuxでは何もしません。

#### `win.isMaximizable()` _macOS_ _Windows_

`Boolean` - ウィンドウをユーザが手作業で最大化できるか否かを返します。

Linuxでは常に `true` を返します。

#### `win.setFullScreenable(fullscreenable)`

* `fullscreenable` Boolean

ウィンドウを最大化/ズームボタンがフルスクリーンモードをトグル、またはウィンドウを最大化
するか否かを設定します。

#### `win.isFullScreenable()`

`Boolean` - ウィンドウを最大化/ズームボタンがフルスクリーンモードをトグル、
またはウィンドウを最大化するか否かを返します。

#### `win.setClosable(closable)` _macOS_ _Windows_

* `closable` Boolean

ウィンドウをユーザが手作業でクローズできるか否かを設定します。Linuxでは何もしません。

#### `win.isClosable()` _macOS_ _Windows_

`Boolean` - ウィンドウをユーザが手作業でクローズできるか否かを返します。

Linuxでは常に `true` を返します。

#### `win.setAlwaysOnTop(flag[, level])`

* `flag` Boolean
* `level` String (オプション) _macOS_ - 指定できる値は、`normal`, `floating`,
  `torn-off-menu`, `modal-panel`, `main-menu`, `status`, `pop-up-menu`,
  `screen-saver`, `dock` です。デフォルトは `floating` です。詳細は
  [macOSのドキュメント][window-levels]を参照してください。

ウィンドウを常に他のウィンドウの上に表示するか否かを設定します。これを設定しても、
ウィンドウは通常のウィンドウであり、フォーカスできないトールボックウィンドウには
なりません。

#### `win.isAlwaysOnTop()`

`Boolean` - ウィンドウが常に他のウィンドウの上にあるか否かを返します。

#### `win.center()`

ウィンドウをスクリーンの中心に移動します。

#### `win.setPosition(x, y[, animate])`

* `x` Integer
* `y` Integer
* `animate` Boolean (オプション) _macOS_

ウィンドウを座標（`x`, `y`）に移動します。

#### `win.getPosition()`

`Integer[]` - ウィンドウの現在位置を含む配列路返します。

#### `win.setTitle(title)`

* `title` String

ネイティブウインドウのタイトルを `title` に変更します。

#### `win.getTitle()`

`String` - ネイティブウインドウのタイトルを返します。

**注:** ウェブページのタイトルとネイティブウィンドウのタイトルは異なる場合があります。

#### `win.setSheetOffset(offsetY[, offsetX])` _macOS_

* `offsetY` Float
* `offsetX` Float (オプション)

macOSにおいてシートの貼り付け位置を変更します。デフォルトでは、シートはウィンドウ枠の
直下に貼り付けられますが、これをHTMLで描画したツールバーの真下に表示したい場合がある
かもしれません。その場合は、たとえば次のようにします。
Changes the attachment point for sheets on macOS. By default, sheets are
attached just below the window frame, but you may want to display them beneath
a HTML-rendered toolbar. For example:

```javascript
const {BrowserWindow} = require('electron')
let win = new BrowserWindow()

let toolbarRect = document.getElementById('toolbar').getBoundingClientRect()
win.setSheetOffset(toolbarRect.height)
```

#### `win.flashFrame(flag)`

* `flag` Boolean

ユーザの注意をひくためのウィンドウのフラッシュを開始または停止します。

#### `win.setSkipTaskbar(skip)`

* `skip` Boolean

ウィンドウをタスクバーに表示させないようにします。

#### `win.setKiosk(flag)`

* `flag` Boolean

キオスクモードに入る、または出ます。

#### `win.isKiosk()`

`Boolean` - ウィンドウがキオスクモードであるか否かを返します。

#### `win.getNativeWindowHandle()`

`Buffer` - ウィンドウのプラットフォーム固有のハンドルを返します。

ハンドルのネイティブタイプは、Windowsは `HWND`、macOSは `NSView*`、Linuxは
`Window` (`unsigned long`) です。

#### `win.hookWindowMessage(message, callback)` _Windows_

* `message` Integer
* `callback` Function

ウィンドウメッセージをフックします。`callback` は、メッセージがWndProcに受け取られた
際に呼び出されます。

#### `win.isWindowMessageHooked(message)` _Windows_

* `message` Integer

`Boolean` - メッセージがフックされているか否かにより、`true` または `false` が返されます。

#### `win.unhookWindowMessage(message)` _Windows_

* `message` Integer

指定のウィンドウメッセージのフックを解除します。

#### `win.unhookAllWindowMessages()` _Windows_

すべてのウィンドウメッセージのフックを解除します。

#### `win.setRepresentedFilename(filename)` _macOS_

* `filename` String

ウィンドウが表すファイルのパス名を設定し、ファイルのアイコンをウィンドウのタイトルバーに
表示します。

#### `win.getRepresentedFilename()` _macOS_

`String` - ウィンドウが表すファイルのパス名を返します。

#### `win.setDocumentEdited(edited)` _macOS_

* `edited` Boolean

ウィンドウのドキュメントが編集されたか否かを指定します。`true` を設定すると、
タイトルバーのアイコンがグレーになります。

#### `win.isDocumentEdited()` _macOS_

`Boolean` - ウィンドウのドキュメントが編集されたか否かを返します。

#### `win.focusOnWebView()`

#### `win.blurWebView()`

#### `win.capturePage([rect, ]callback)`

* `rect` [Rectangle](structures/rectangle.md) (オプション) - キャプチャする領域
* `callback` Function

`webContents.capturePage([rect, ]callback)`と同じです。

#### `win.loadURL(url[, options])`

* `url` URL
* `options` Object (オプション)
  * `httpReferrer` String - HTTPリファラーURL.
  * `userAgent` String - リクエストを発行したユーザエージェント
  * `extraHeaders` String - "\n"区切りの追加ヘッダー

`webContents.loadURL(url[, options])` と同じです。

`url` には、リモートアドレス（たとえば、`http://`）、または、`file://` プロトコルを使った
ローカルHTMLファイルのパスを指定できます。

ファイルURLが適切にフォーマットされていることを保証するために、Nodeの
[`url.format`](https://nodejs.org/api/url.html#url_url_format_urlobject)
メソッドの使用を勧めます。

```javascript
let url = require('url').format({
  protocol: 'file',
  slashes: true,
  pathname: require('path').join(__dirname, 'index.html')
})

win.loadURL(url)
```

#### `win.reload()`

`webContents.reload` と同じです。

#### `win.setMenu(menu)` _Linux_ _Windows_

* `menu` Menu

ウィンドウのメニューバーとして `menu` を設定します。`null` を指定するとメニューバーを
取り除きます。

#### `win.setProgressBar(progress[, options])`

* `progress` Double
* `options` Object (オプション)
  * `mode` String _Windows_ - プログレスバーのモード。 (指定可能な値は、`none`, `normal`, `indeterminate`, `error`, `paused`)

プログレスバーの進捗値を設定します。有効な値の範囲は [0, 1.0] です。

progress < 0 を指定すると、プログレスバーを除去します。
progress > 1 を指定すると、イミディエイトモードに変わります。

Linuxでは、Unityデスクトップ環境だけがサポートしており、`package.json` で
`desktopName` フィールドに `*.desktop` ファイル名を指定する必要があります。
デフォルトでは `app.getName().desktop` が仮定されます。

Windowsでは、モードを渡すことができます。受け付ける値は、`none`, `normal`,
`indeterminate`, `error`, `paused` のいずれかです。モードを指定せずに（ただし、
有効な範囲の値は指定）`setProgressBar` を呼び出すと、`normal` が仮定されます。

#### `win.setOverlayIcon(overlay, description)` _Windows_

* `overlay` [NativeImage](native-image.md) - タスクバーアイコンの下右角に表示するアイコン。このパラメタが `null` の場合は、オーバーレイがクリアされます。
* `description` String - Accessibilityスクリーンリーダーに渡される記述文。

現在のタスクバーアイコンにオーバーレイ表示する 16 x 16 ピクセルのアイコンを設定します。
通常、何からのアプリケーションの状態を伝えたり、ユーザに受動的な通知を行うために使用します。

#### `win.setHasShadow(hasShadow)` _macOS_

* `hasShadow` Boolean

ウィンドウが影を持つか否かを設定します。WindowsとLinuxでは何もしません。

#### `win.hasShadow()` _macOS_

`Boolean` - ウィンドウが影を持っているか否かを返します。

WindowsとLinuxでは常に `true` を返します。

#### `win.setThumbarButtons(buttons)` _Windows_

* `buttons` [ThumbarButton[]](structures/thumbar-button.md)

`Boolean` - ボタンが首尾よく追加されたか否かを返します。

指定した一連のウィンドウサムネイル画像へのボタンを持つサムネールツールバーを
タスクバーボタンレイアウトに追加します。サムネイルが首尾よく追加されたか否かを示す
`Boolean` オブジェクトを返します。

サムネイルツールバーのボタンの数はスペースの制限により7個以下でなければなりません。
一旦サムネイルツールバーを設定すると、プラットフォームの制限により、このツールバーを
取り除くことはできません。ただし、空の配列でこのAPIを呼び出すとボタンをクリアできます。

`buttons` は `Button` オブジェクトの配列です。

* `Button` Object
  * `icon` [NativeImage](native-image.md) - サムネイルツールバーに表示するアイコン
  * `click` Function
  * `tooltip` String (オプション) - ボタンのツールチップに表示するテキスト
  * `flags` String[] (オプション) - ボタンの特定の状態とふるまいをコントールします。
    デフォルトは `['enabled']` です。

`flags` は `String` の配列で、以下の文字列を含めることができます。

* `enabled` - ボタンは有効で、ユーザは利用可能です。
* `disabled` - ボタンは無効です。ボタンは表示されますが、ユーザのアクションに反応しない
  ことを示す視覚状態になります。
* `dismissonclick` - ボタンがクリックされると、サムネイルウィンドウが直ちにクローズします。
* `nobackground` - ボタンの境界線を描画しません。画像のみに使用します。
* `hidden` - ボタンはユーザには表示されません。
* `noninteractive` - ボタンは有効ですが、インターラクティブではありません。
  ボタンが押下された状態は描画されません。この値はボタンが通知に使用されるような場合を
  想定しています。

#### `win.setThumbnailClip(region)` _Windows_

* `region` [Rectangle](structures/rectangle.md) - ウィンドウの領域

タスクバーのウィンドウサムネイルにマウスポインタがのった時に表示されるサムネイル画像を
表示するウィンドウの領域を設定します。空の領域 `{x: 0, y: 0, width: 0, height: 0}` を
指定することでサムネイルをウィンドウ全体にリセットすることができます。

#### `win.setThumbnailToolTip(toolTip)` _Windows_

* `toolTip` String

タスクバーのウィンドウサムネイルにマウスポインタがのった時に表示されるツールチップを
設定します。

#### `win.showDefinitionForSelection()` _macOS_

`webContents.showDefinitionForSelection()` と同じです。

#### `win.setIcon(icon)` _Windows_ _Linux_

* `icon` [NativeImage](native-image.md)

ウィンドウアイコンを変更します。

#### `win.setAutoHideMenuBar(hide)`

* `hide` Boolean

ウィンドウのメニューバーを自動的に隠すか否かを設定します。一旦設定すると、メニューバーは
ユーザが `Alt` キーを押した時のみ表示されます。

メニューバーがすでに可視の場合、`setAutoHideMenuBar(true)` を呼び出しても
直ちに隠すことはありません。

#### `win.isMenuBarAutoHide()`

`Boolean` - メニューバーを自動的に隠すか否かを返します。

#### `win.setMenuBarVisibility(visible)`

* `visible` Boolean

メニューバーを可視にするか否かを設定します。メニューバーが自動的に隠される場合も、
ユーザは `Alt` キーを押すことでメニューバーを表示することができます。

#### `win.isMenuBarVisible()`

`Boolean` - メニューバーが可視が否かを返します。

#### `win.setVisibleOnAllWorkspaces(visible)`

* `visible` Boolean

すべてのワークスペースでウィンドウを可視にするか否かを設定します。

**注:** このAPIはWindowsでは何もしません。

#### `win.isVisibleOnAllWorkspaces()`

`Boolean` - すべてのワークスペースでウィンドウが可視か否かを返します。

**注:** このAPIはWindowsでは常に `false` を返します。

#### `win.setIgnoreMouseEvents(ignore)`

* `ignore` Boolean

ウィンドウがすべてのマウスイベントを無視するようにします。

このウィンドウで生じたすべてのマウスイベントはこのウィンドウの下にあるウィンドウに渡されます。
ただし、このウィンドウにフォーカスがある場合、キーボードイベントは受け取ります。

#### `win.setContentProtection(enable)` _macOS_ _Windows_

* `enable` Boolean

他のアプリケーションがウィンドウコンテンツをキャプチャできないようにします。

macOSでは、 NSWindowのsharingType を NSWindowSharingNone に設定します。
On Windowsでは、`WDA_MONITOR` を引数に、SetWindowDisplayAffinity を
呼び出します。

#### `win.setFocusable(focusable)` _Windows_

* `focusable` Boolean

ウィンドウをフォーカスできるか否かを変更します。

[blink-feature-string]: https://cs.chromium.org/chromium/src/third_party/WebKit/Source/platform/RuntimeEnabledFeatures.in

#### `win.setParentWindow(parent)` _Linux_ _macOS_

* `parent` BrowserWindow

`parent` をカレントウィンドウの親ウィンドウに設定します。`null` を渡すと、
カレントウィンドウをトップレベルウィンドウに変えます。

#### `win.getParentWindow()`

`BrowserWindow` - 親ウィンドウを返します。

#### `win.getChildWindows()`

`BrowserWindow[]` - すべての子ウィンドウを返します。

[window-levels]: https://developer.apple.com/reference/appkit/nswindow/1664726-window_levels
