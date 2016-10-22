# ShortcutDetails オブジェクト

* `target` String - このショートカットで起動する対象
* `cwd` String (オプション) - ワーキングディレクトリ。デフォルトは空。
* `args` String (オプション) - このショートカットで起動する際に `target` に適用される
  引数。デフォルトは空。
* `description` String (オプション) - ショートカットの説明。デフォルトは空。
* `icon` String (オプション) - アイコンのパス。DLL か EXEのパスを指定できます。
  `icon` と `iconIndex` はセットで設定されなければなりません。デフォルトは空で、
  起動対象のアイコンを使用します。
* `iconIndex` Number (オプション) - `icon` が DLL または EXE の時、アイコンの
  リソースIDを指定します。デフォルトは `0`。
* `appUserModelId` String (オプション) - アプリケーションユーザーモデルID。
  デフォルトは空。
