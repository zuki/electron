# サポートしているChromeコマンドラインスイッチ

> Electronでサポートされているコマンドラインスイッチ

アプリケーションのメインスクリプトで[app.commandLine.appendSwitch][append-switch]を使うことで、[app][app]モジュールの[ready][ready]イベントが発行される前にコマンドラインスイッチを追加できます。

```javascript
const {app} = require('electron')
app.commandLine.appendSwitch('remote-debugging-port', '8315')
app.commandLine.appendSwitch('host-rules', 'MAP * 127.0.0.1')

app.on('ready', () => {
  // Your code here
})
```


## --ignore-connections-limit=`domains`

カンマ区切りのリストで指定された `domains`で、接続数制限を無視します。

## --disable-http-cache

HTTPリクエストのディスクキャッシュを無効にします。

## --disable-http2

HTTP/2 と SPDY/3.1 プロトコルを無効にします。

## --debug=`port` と --debug-brk=`port`

デバッグ関連のフラグです。詳細は、[メインプロセスのデバッグ][debugging-main-process]
ガイドを参照してください。

## --remote-debugging-port=`port`

`port`で指定したHTTP越しのリモートデバッグを有効にします。

## --js-flags=`flags`

Node JSエンジンに渡されるフラグを指定します。メインプロセスで `flags` を有効化したい場合は、
Electronの開始時に渡される必要があります。

```bash
$ electron --js-flags="--harmony_proxies --harmony_collections" your-app
```
利用可能なフラグの一覧は[Nodeのドキュメント][node-cli]を参照するか、ターミナルで
`node --help` を実行してください。

## --proxy-server=`address:port`

システム設定を上書きし、指定したプロキシサーバーを使用します。HTTPS、WebSocketリクエストを含むHTTPプロトコルのリクエストのみに影響します。全てのプロキシサーバーがHTTPSとWebSocketリクエストに対応しているわけではないことにも注意してください。

## --proxy-bypass-list=`hosts`

プロキシサーバーを使用しないホストをセミコロンで区切って指定します。
このフラグは、`--proxy-server`と同時に使われる時にのみ効果があります。

例:

```javascript
const {app} = require('electron')
app.commandLine.appendSwitch('proxy-bypass-list', '<local>;*.google.com;*foo.com;1.2.3.4:5678')
```

ローカルアドレス（`localhost`や`127.0.0.1`など）、`google.com`サブドメイン、`foo.com` サフィックスを含むホスト、`1.2.3.4:5678`を除く、すべてのホストでプロキシサーバーが使われます。

## --proxy-pac-url=`url`

`url`で指定したPACスクリプトを使います。

## --no-proxy-server

プロキシサーバーを使わず、常に直接接続します。ほかのプロキシサーバーフラグをすべて上書きします。

## --host-rules=`rules`

ホスト名がどのようにマップされているかを制御する`rules` のカンマ区切りのリスト。

例:

* `MAP * 127.0.0.1` 全てのホスト名を127.0.0.1にマッピングするよう強制します。
* `MAP *.google.com proxy` すべてのgoogle.comサブドメインを "proxy"で解決するよう強制します。
* `MAP test.com [::1]:77` "test.com"をIPv6ループバックで解決するよう強制します。また、変換されたソケットアドレスのポートを77番にするよう強制します。
* `MAP * baz, EXCLUDE www.google.com` "www.google.com" を除く全てのホストを"baz"に再マッピングします。

これらのマッピングは、ネットリクエスト（直接接続でのTCP接続とホスト解決、HTTPプロキシ接続での`CONNECT`、`SOCKS`プロキシ接続でのエンドポイントホスト）のエンドポイントホストに適用されます。

## --host-resolver-rules=`rules`

`--host-rules` と同じですが、`rules` はホスト解決のみに適用されます。

## --auth-server-whitelist=`url`

統合認証を有効にするサーバーのカンマ区切りのリスト。

例:

```
--auth-server-whitelist='*example.com, *foobar.com, *baz'
```

この場合、`example.com`, `foobar.com`, `baz` の各々で終わるすべての `url` は
統合認証とみなされます。接頭辞 `*` なしだと、urlは完全一致でなければなりません。

## --auth-negotiate-delegate-whitelist=`url`

ユーザ証明書の委任を必要とするサーバのカンマ区切りのリスト。接頭辞 `*` なしだと、
urlは完全一致でなければなりません。

## --ignore-certificate-errors

証明書関連エラーを無視します。

## --ppapi-flash-path=`path`

pepper flash pluginの`path`を設定します。

## --ppapi-flash-version=`version`

pepper flash pluginの`version`を設定します。

## --log-net-log=`path`

ログを保存するようネットログイベントを有効化し、`path`に書き込みます。

## --ssl-version-fallback-min=`version`

TLSフォールバックを許可する最小のSSL/TLSバージョン ("tls1"や"tls1.1" 、 "tls1.2")を設定します。

## --cipher-suite-blacklist=`cipher_suites`

無効にするために、SSL暗号スイートのカンマ区切りのリストを指定します。

## --disable-renderer-backgrounding

不可視のページのレンダラープロセスの優先度を下げることからChromiumを防ぎます。

このフラグは、グローバルですべてのレンダラープロセスに有効です。一つのウィンドウだけで無効化したい場合は、[無音を再生する][play-silent-audio]というハックで対応します。

## --enable-logging

コンソールにChromiumのログを出力します。

ユーザーのアプリが読み込まれる前に解析されるため、`app.commandLine.appendSwitch`では使用できませんが、`ELECTRON_ENABLE_LOGGING`を環境変数に設定すると同じ効果を得ることができます。

## --v=`log_level`

規定の最大アクティブ V-loggingレベルを設定します。0が既定値です。通常、V-loggingレベルには正の値が使用されます。

`--enable-logging` が同時に渡された時だけ、このスイッチは動作します。

## --vmodule=`pattern`

`--v`で付与された値を上書きするために、モジュール毎の最大V-loggingレベルを付与します。例えば、 `my_module=2,foo*=3` は、`my_module.*` と `foo*.*`のソースファイル全てのロギングレベルを変更します。

前方または後方スラッシュを含む任意のパターンは、全体のパス名だけでなく、モジュールに対してもテストとされます。例えば、`*/foo/bar/*=2`は`foo/bar`ディレクトリ下のソースファイルのすべてのコードでロギングレベルを変更します。

このスイッチは、`--enable-logging`が渡された時のみ動作します。

[app]: app.md
[append-switch]: app.md#appcommandlineappendswitchswitch-value
[ready]: app.md#event-ready
[play-silent-audio]: https://github.com/atom/atom/pull/9485/files
[debugging-main-process]: ../tutorial/debugging-main-process.md
[node-cli]: https://nodejs.org/api/cli.html
