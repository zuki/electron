# Display オブジェクト

* `id` Number - このディスプレイに関連するユニーク識別子。
* `rotation` Number - 0, 90, 180, 270 のいずれかで、スクリーンの回転を
  統計回りの角度で表します。
* `scaleFactor` Number - 出力装置のピクセルスケール係数。
* `touchSupport` String - `available`, `unavailable`, `unknown` のいずれか。
* `bounds` [Rectangle](rectangle.md)
* `size` Object
  * `height` Number
  * `width` Number
* `workArea` [Rectangle](rectangle.md)
* `workAreaSize` Object
  * `height` Number
  * `width` Number

`Display` オブジェクトは、システムに接続された物理的なディスプレイを表します。ヘッドレス
システムでは見せかけの `Display` が存在する場合があります。また、`Display` が
リーモートの仮想ディスプレイに対応する場合もあります。
