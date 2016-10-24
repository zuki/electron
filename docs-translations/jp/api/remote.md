# remote

> レンダラプロセスからメインプロセスモジュールを使用する。

 `remote`モジュールは、レンダラプロセス（ウェブページ）とメインプロセス間でインタープロセスコミュニケーション（IPC）をする簡単な方法を提供します。

Electronでは、GUI関連モジュール（`dialog`や`menu`など）はメインプロセスのみに提供されており、レンダラプロセスには提供されていません。レンダラプロセスからそれらを使うためには、メインプロセスにインタープロセスメッセージを送信する`ipc`モジュールが必要です。`remote`モジュールを使うと、Javaの[RMI][rmi]と同じように、明示的にインタープロセスメッセージを送信することなく、メインプロセスオブジェクトのメソッドを呼び出すことができます。レンダラプロセスからブラウザーウィンドウを作成する例を示します。

```javascript
const {BrowserWindow} = require('electron').remote
let win = new BrowserWindow({width: 800, height: 600})
win.loadURL('https://github.com')
```

**注:** 逆のケース（メインプロセスからレンダラプロセスへのアクセス）には、
[webContents.executeJavascript](web-contents.md#contentsexecutejavascriptcode-usergesture-callback)が使えます。

## Remote オブジェクト

`remote` モジュールから返される各オブジェクト（関数を含む）は、メインプロセスのオブジェクトを表します（リモートオブジェクトまたはリモート関数と呼びます）。リモートプロジェクトのメソッドを実行したり、リモート関数をコールしたり、リモートコンストラクタ（関数）で新しいオブジェクトを生成したりしたとき、実際に非同期なインタープロセスメッセージを送信していることになります。

上の例では、`BrowserWindow` と `win` はリモートオブジェクトであり、
`new BrowserWindow` は `BrowserWindow` オブジェクトをレンダラプロセスで作成しません。
代わりに、`BrowserWindow` オブジェクトをメインプロセスで作成し、レンダラプロセスにおける
対応するリモートオブジェクト、すなわち`win`オブジェクトを返します。

**注:** リモートオブジェクトが最初に参照された時に存在する [可算プロパティ][enumerable-properties]のみがリモート経由でアクセス可能です。

**注:** 配列とバッファは `remote` モジュール経由でアクセスされた時にIPC越しにコピーされます。レンダラプロセスでそれらを変更しても、メインプロセスのそれらは変更されません。逆の場合も同じです。

## Remote オブジェクトのライフタイム

Electronは、レンダラプロセスのリモートオブジェクトが生きている限り（言い換えれば、
ガベージコレクションされない限り）、対応するメインプロセスのオブジェクトは解放されない
ことを保証します。リモートオブジェクトがガベージコレクションされたとき、対応するメイン
プロセスのオブジェクトがデリファレンスされます。

レンダラプロセスでリモートオブジェクトがリークした場合（マップに格納されていたが解放されていない）、メインプロセスの対応するオブジェクトもリークするので、リモートオブジェクトがリークしないように細心の注意を払うべきです。

文字列や数字のような基本データ型は、コピーして送信されます。

## メインプロセスにコールバックを渡す

メインプロセスのコードは、レンダラからコールバックを受け取ることができます。例えば、`remote`モジュールです。しかし、この機能を使う際には極めて慎重に行うべきです。

第一に、デッドロックを避けるために、メインプロセスに渡されたコールバックは非同期に呼び出されます。渡されたコールバックの返り値をメインプロセスが取得することを期待すべきではありません。

例えば、レンダラプロセスから渡された関数をメインプロセスでコールされた`Array.map`で使用することはできません。:

```javascript
// メインプロセス mapNumbers.js
exports.withRendererCallback = (mapper) => {
  return [1, 2, 3].map(mapper)
}

exports.withLocalCallback = () => {
  return [1, 2, 3].map(x => x + 1)
}
```

```javascript
// レンダラプロセス
const mapNumbers = require('electron').remote.require('./mapNumbers')
const withRendererCb = mapNumbers.withRendererCallback(x => x + 1)
const withLocalCb = mapNumbers.withLocalCallback()

console.log(withRendererCb, withLocalCb)
// [undefined, undefined, undefined], [2, 3, 4]
```

ご覧のように、レンダラコールバックの同期的な返り値は予想したものではなく、メインプロセスにある
同一のコールバックの返り値とは一致しません。

第二に、メインプロセスに渡されたコールバックは、メインプロセスがガベージコレクションするまで存続します。

例えば、次のコードは一見無害なコードのように思えます。リモートオブジェクト上で`close`イベントのコールバックをインストールしています。

```javascript
require('electron').remote.getCurrentWindow().on('close', () => {
  // window was closed...
})
```

しかし、このコールバックは明示的にアンインストールするまでメインプロセスにより参照されることを
思い出してください。アンインストールしない場合、ウィンドウをリロードするたびにコールバックが
再インストールされ、再起動毎に1つコールバックをリークします。

さらに悪いことに、前にインストールされたコールバックのコンテキストは解放されているので、
`close`イベントが出力されると、メインプロセスで例外が発生します。

この問題を避けるために、メインプロセスに渡されたレンダラコールバックへの参照を確実に処分
してください。これにはイベントハンドラを解除すること、または、終了するレンダラプロセスから来た
コールバックをデリファレンスするようメインプロセスが明示的に伝えられるよう保証することを
含みます。

## メインプロセスの組み込みモジュールにアクセスする

メインプロセスの組み込みモジュールは、`remote`モジュールではゲッターとして追加されている
ので、`electron`モジュールのように直接それらを使用できます。

```javascript
const app = require('electron').remote.app
console.log(app)
```

## メソッド

`remote`モジュールは次のメソッドを持っています。

### `remote.require(module)`

* `module` String

`Object` - メインプロセスで `require(module)` により返されるオブジェクトを返します。

### `remote.getCurrentWindow()`

`BrowserWindow` - このウェブページが属する[`BrowserWindow`](browser-window.md) オブジェクトを返します。

### `remote.getCurrentWebContents()`

`WebContents` - このウェブページの[`WebContents`](web-contents.md) オブジェクトを返します。

### `remote.getGlobal(name)`

* `name` String

`any` - メインプロセスのグローバル変数 `name`（例えば、`global[name]`）を返します。

## プロパティ

### `remote.process`

メインプロセスの `process` オブジェクトを返します。これは`remote.getGlobal('process')`
と同じが、キャッシュされます。

[rmi]: http://en.wikipedia.org/wiki/Java_remote_method_invocation
[enumerable-properties]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Enumerability_and_ownership_of_properties
