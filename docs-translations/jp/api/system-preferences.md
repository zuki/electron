# systemPreferences

> システムの環境設定を取得します。

```javascript
const {systemPreferences} = require('electron')
console.log(systemPreferences.isDarkMode())
```

## イベント

`systemPreferences` オブジェクトは以下のイベントを出力します。

### イベント: 'accent-color-changed' _Windows_

返り値:

* `event` Event
* `newColor` String - ユーザがシステムのアクセントカラーに指定した新たなRGBAカラー。

### イベント: 'color-changed' _Windows_

返り値:

* `event` Event

### イベント: 'inverted-color-scheme-changed' _Windows_

返り値:

* `event` Event
* `invertedColorScheme` Boolean - ハイコントラストテーマなどのインバーテッド
  カラースキームが使用されている場合は `true`、そうでない場合は `false`。

## メソッド

### `systemPreferences.isDarkMode()` _macOS_

`Boolean` - macOS がダークモードならば `true` を返し、通常モードなら `false` を返します。

### `systemPreferences.isSwipeTrackingFromScrollEventsEnabled()` _macOS_

Returns `Boolean` - ページ間のスワイプ設定がオンになっているか否かを返します。

### `systemPreferences.postNotification(event, userInfo)` _macOS_

* `event` String
* `userInfo` Object

macOSのネイティブな通知として `event` をポストします。`userInfo` は通知によって送られてくるユーザー情報のオブジェクトです。

### `systemPreferences.postLocalNotification(event, userInfo)` _macOS_

* `event` String
* `userInfo` Object

macOSのネイティブな通知として `event` をポストします。`userInfo` は通知によって送られてくるユーザー情報のオブジェクトです。

### `systemPreferences.subscribeNotification(event, callback)` _macOS_

* `event` String
* `callback` Function

macOS のネイティブな通知を購読します。 `callback` は `callback(event, userInfo)` として `event` の発生に対応して呼ばれます。`userInfo` は通知によって送られてくるユーザー情報のオブジェクトです。

この関数が返す `id` は `event`の購読をやめる際に使用します。

内部ではこの API は `NSDistributedNotificationCenter` を購読するので、`event` の例は以下のようなものがあります。

* `AppleInterfaceThemeChangedNotification`
* `AppleAquaColorVariantChanged`
* `AppleColorPreferencesChangedNotification`
* `AppleShowScrollBarsSettingChanged`

### `systemPreferences.unsubscribeNotification(id)` _macOS_

* `id` Integer

`id` の購読をやめます。

### `systemPreferences.subscribeLocalNotification(event, callback)` _macOS_

* `event` String
* `callback` Function

`subscribeNotification` と同じですが、ローカルデフォルトとして `NSNotificationCenter` を購読します。下記のような `event` を捕まえるために必要です。

* `NSUserDefaultsDidChangeNotification`

### `systemPreferences.unsubscribeLocalNotification(id)` _macOS_

* `id` Integer

`unsubscribeNotification` と同じですが、`NSNotificationCenter` による購読をやめます。

### `systemPreferences.getUserDefault(key, type)` _macOS_

* `key` String
* `type` String - 右記の値を入れられます `string`, `boolean`, `integer`, `float`, `double`,
  `url`, `array`, `dictionary`

システム環境設定の `key` の値を取得します。

この API は macOS の `NSUserDefaults` から情報を取得します。よく使われる `key` 及び `type` には下記のものがあります。

* `AppleInterfaceStyle: string`
* `AppleAquaColorVariant: integer`
* `AppleHighlightColor: string`
* `AppleShowScrollBars: string`
* `NSNavRecentPlaces: array`
* `NSPreferredWebServices: dictionary`
* `NSUserDictionaryReplacementItems: array`

### `systemPreferences.isAeroGlassEnabled()` _Windows_

[DWM composition][dwm-composition] (Aero Glass) が有効だと `true` を返し、そうでないと `false` を返します。

使用例として、例えば透過ウィンドウを作成するかしないか決めるときに使います(DWM composition が無効だと透過ウィンドウは正常に動作しません)

```javascript
const {BrowserWindow, systemPreferences} = require('electron')
let browserOptions = {width: 1000, height: 800}

// プラットフォームがサポートしている場合に限り透過ウィンドウを作成します。
if (process.platform !== 'win32' || systemPreferences.isAeroGlassEnabled()) {
  browserOptions.transparent = true
  browserOptions.frame = false
}

// ウィンドウを作成
let win = new BrowserWindow(browserOptions)

// 分岐
if (browserOptions.transparent) {
  win.loadURL(`file://${__dirname}/index.html`)
} else {
  // 透過がサポートされてないなら、通常のスタイルの html を表示する
  win.loadURL(`file://${__dirname}/fallback.html`)
}
```

[dwm-composition]:https://msdn.microsoft.com/en-us/library/windows/desktop/aa969540.aspx

### `systemPreferences.getAccentColor()` _Windows_

Returns `String` - The users current system wide accent color preference in RGBA
hexadecimal form.

```js
const color = systemPreferences.getAccentColor() // `"aabbccdd"`
const red = color.substr(0, 2) // "aa"
const green = color.substr(2, 2) // "bb"
const blue = color.substr(4, 2) // "cc"
const alpha = color.substr(6, 2) // "dd"
```

### `systemPreferences.getColor(color)` _Windows_

* `color` String - One of the following values:
  * `3d-dark-shadow` - Dark shadow for three-dimensional display elements.
  * `3d-face` - Face color for three-dimensional display elements and for dialog
    box backgrounds.
  * `3d-highlight` - Highlight color for three-dimensional display elements.
  * `3d-light` - Light color for three-dimensional display elements.
  * `3d-shadow` - Shadow color for three-dimensional display elements.
  * `active-border` - Active window border.
  * `active-caption` - Active window title bar. Specifies the left side color in
    the color gradient of an active window's title bar if the gradient effect is
    enabled.
  * `active-caption-gradient` - Right side color in the color gradient of an
    active window's title bar.
  * `app-workspace` - Background color of multiple document interface (MDI)
    applications.
  * `button-text` - Text on push buttons.
  * `caption-text` - Text in caption, size box, and scroll bar arrow box.
  * `desktop` - Desktop background color.
  * `disabled-text` - Grayed (disabled) text.
  * `highlight` - Item(s) selected in a control.
  * `highlight-text` - Text of item(s) selected in a control.
  * `hotlight` - Color for a hyperlink or hot-tracked item.
  * `inactive-border` - Inactive window border.
  * `inactive-caption` - Inactive window caption. Specifies the left side color
    in the color gradient of an inactive window's title bar if the gradient
    effect is enabled.
  * `inactive-caption-gradient` - Right side color in the color gradient of an
    inactive window's title bar.
  * `inactive-caption-text` - Color of text in an inactive caption.
  * `info-background` - Background color for tooltip controls.
  * `info-text` - Text color for tooltip controls.
  * `menu` - Menu background.
  * `menu-highlight` - The color used to highlight menu items when the menu
    appears as a flat menu.
  * `menubar` - The background color for the menu bar when menus appear as flat
    menus.
  * `menu-text` - Text in menus.
  * `scrollbar` - Scroll bar gray area.
  * `window` - Window background.
  * `window-frame` - Window frame.
  * `window-text` - Text in windows.

Returns `String` - The system color setting in RGB hexadecimal form (`#ABCDEF`).
See the [Windows docs][windows-colors] for more details.

### `systemPreferences.isInvertedColorScheme()` _Windows_

Returns `Boolean` - `true` if an inverted color scheme, such as a high contrast
theme, is active, `false` otherwise.

[windows-colors]:https://msdn.microsoft.com/en-us/library/windows/desktop/ms724371(v=vs.85).aspx
