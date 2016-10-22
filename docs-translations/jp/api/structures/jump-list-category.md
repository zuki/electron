# JumpListCategory オブジェクト

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
* `items` JumpListItem[] - Array of [`JumpListItem`](jump-list-item.md) オブジェクトの配列で、`type` が `tasks` または `custom` の場合、設定されなければなりません。それ以外の場合は、省略されるべきです。

**注:** If a `JumpListCategory` オブジェクトが `type` プロパティも `name` プロパティも
設定されていない場合は、その `type` は `tasks` だと仮定されます。`name` プロパティが
設定され、`type` プロパティが省略されている場合は、`type` は `custom` だと仮定されます。
