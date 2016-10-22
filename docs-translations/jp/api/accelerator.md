# Accelerator

> キーボードショートカットを定義します。

アクセラレータは、`+` 文字で結合した複数の修飾語句とキーコードを含むことができます。

例:

* `CommandOrControl+A`
* `CommandOrControl+Shift+Z`

## プラットフォームの留意点

LinuxとWindowsでは `Command` キーは何の効果もありません。そのため、何らかの
アクセラレータを定義するには、macOSでは `Command` キーを、LinuxとWindowsでは
`Control` キーを意味する `CommandOrControl` を使用してください。

`Option` キーの代わりに `Alt` を使用してください。`Option` キーはmacOSにしか存在
しませんが、`Alt` キーはすべてのプラットフォームで利用できます。

 `Super` キーは、WindowsとLinuxでは `Windows` キーに、macOSでは、`Cmd` キーに
 関連付けられます。

## 利用可能な修飾語句

* `Command` (または、短く `Cmd`)
* `Control` (または、短く `Ctrl`)
* `CommandOrControl` (または、短く `CmdOrCtrl`)
* `Alt`
* `Option`
* `AltGr`
* `Shift`
* `Super`

## 利用可能なキーコード

* `0` to `9`
* `A` to `Z`
* `F1` to `F24`
* `~`, `!`, `@`, `#`, `$`などの記号
* `Plus`
* `Space`
* `Backspace`
* `Delete`
* `Insert`
* `Return` (またはエイリアスで `Enter`)
* `Up`と `Down`, `Left`, `Right`
* `Home` と `End`
* `PageUp` と `PageDown`
* `Escape` (または、短く `Esc`)
* `VolumeUp` と `VolumeDown`, `VolumeMute`
* `MediaNextTrack` と `MediaPreviousTrack`, `MediaStop` , `MediaPlayPause`
* `PrintScreen`
