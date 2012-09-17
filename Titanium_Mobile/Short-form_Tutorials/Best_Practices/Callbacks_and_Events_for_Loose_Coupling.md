# 疎結合のコールバックとイベント

## 概要

Javascriptのベストプラクティスについて書かれたAppceleratorのWikiを読めば、保守可能な良いコードを書くにはグローバル変数やグローバルな名前空間を汚染することがやってはいけないことだと私たちは知っています。しかし一方で、古いクセを直すことは難しいことも分かっています。コードを書いていて、まさに「この」値を保存したい？誰も気にしませんよ、グローバル変数を作って使ってしまいましょう！

このアプローチの問題点は、それが結局は私たちに跳ね返ってくることです。私たちはそのグローバル変数のことを忘れ、他の誰も気付かず、その結果、同じ情報を得る他の方法を用意してしまう。何よりも悪いことには、それはしばしばうまくいきません。

以下の３つのメモは、明るい黄色の付箋に書いてディスプレイに貼っておくべきです。

1. モジュール構造のコードを書くこと（今からでも！）
2. イベントベースのプログラミングは良いこと
3. グローバル変数は最後の手段としてのみ使われるべき

## 位置情報を取得する

ではまず、課題を決めましょう。課題は、ユーザーの現在位置の取得です。
これは非同期に発生し、更新が必要なUI要素は別ファイルにあります。もちろんモジュールですよ！
APIの[Titanium.Geolocation.getCurrentPosition](http://docs.appcelerator.com/titanium/2.1/index.html#!/api/Titanium.Geolocation-method-getCurrentPosition "Titanium.Geolocation.getCurrentPosition")を呼び出すと、位置情報が見つかったときにコールバックで知らせてくれることはお分かりですね。

### part1.js

```javascript
Titanium.Geolocation.accuracy = Titanium.Geolocation.ACCURACY_BEST;
Titanium.Geolocation.distanceFilter = .25;
Ti.Geolocation.purpose = "Callbacks Are Your Friend";
 // APIを実行
Ti.Geolocation.getCurrentPosition(function(e) {
   // 位置があればこれを実行してエラー判定
  if (e.error) {
    Ti.API.error('geo - current position' + e.error);
    return;
  }
   // 位置情報を取得した
  Ti.App.info('got a location ',JSON.stringify(e));
});
```

## 便利な場所に置く

ここで、グローバル変数をコールバックの外に作成し、情報を取得するのに使うことも可能です。

### part2.js

```javascript
Titanium.Geolocation.accuracy = Titanium.Geolocation.ACCURACY_BEST;
Titanium.Geolocation.distanceFilter = .25;
Ti.Geolocation.purpose = "Callbacks Are Your Friend";

// コールバックで受け取った座標を保持するグローバル変数
var global_var_coords;

// APIを実行
Ti.Geolocation.getCurrentPosition(function(e) {

  // 位置があればこれを実行してエラー判定
  if (e.error) {
    Ti.API.error('geo - current position' + e.error);
    return;
  }

  // 位置情報を取得した
  Ti.App.info('got a location ',JSON.stringify(e));

  // グローバル変数にセット
  global_var_coords = e.coords
});
```

しかしこれは、更新が必要なUI要素がコールバックと同じJSファイルにある場合のみ役立ちます。
つまり、モジュール構造のコードを書いている場合には不可能です。
また、そもそもUIに関するコードは位置情報のロジックに混ぜるべきではありません。

## イベントリスナーを使ってより良い場所に置く

それではどうしましょうか？　まず、グローバル変数を取り除いて、位置情報が正常に受信できたことを示すイベントを発火します。

### part3.js

```javascript
Titanium.Geolocation.accuracy = Titanium.Geolocation.ACCURACY_BEST;
Titanium.Geolocation.distanceFilter = .25;
Ti.Geolocation.purpose = "Callbacks Are Your Friend";

// グローバルに登録するイベントリスナー
Ti.App.addEventListener('location.updated',function(coords){
   Ti.API.debug(JSON.stringify(coords));
}

// APIを実行
Ti.Geolocation.getCurrentPosition(function(e) {

  // 位置があればこれを実行してエラー判定
  if (e.error) {
    Ti.API.error('geo - current position' + e.error);
    return;
  }

  // 位置情報を取得した
  Ti.App.info('got a location ',JSON.stringify(e));

  // 位置情報を含むイベントを発火
  Ti.App.fireEvent('location.updated',e.coords);

});
```

これで、アプリケーション内で位置情報を必要とするところではどこでも、イベントのペイロードとして配信される座標を取得するためのイベントリスナーを定義することができるようになりました。これは素敵ですね。グローバル変数は一切不要になりました！

## しかし別の方法があります。コールバック内でコールバックを使いましょう。

位置情報を取得し終わったときに呼ばれる関数を渡しましょう。
イベントの発火に加えて、デバイスから取得した座標とともにパラメータとして受け取った関数を呼び出します。

### part4.js

```javascript
//
// file_with_location.js
//
Titanium.Geolocation.accuracy = Titanium.Geolocation.ACCURACY_BEST;
Titanium.Geolocation.distanceFilter = .25;
Ti.Geolocation.purpose = "Callbacks Are Your Friend";
/**
* @param {Object} _callback 位置情報の問い合わせが完了したときに呼ぶ
*/
function currentLocation(_callback) {
    // APIを実行
    Ti.Geolocation.getCurrentPosition(function(e) {

        // 位置があればこれを実行してエラー判定
        if (e.error) {
            Ti.API.error('geo - current position' + e.error);

            // 単純化のために、nullを返すことがエラーの意味とする
            if (_callback) {
                _callback(null);
            }
            return;
        }

        // 位置情報を取得した
        Ti.App.info('got a location ',JSON.stringify(e));

        // 位置情報を含むイベントを発火
        Ti.App.fireEvent('location.updated',e.coords);

        // 座標を引数にしてコールバックを呼び出し
        if (_callback) {
            _callback(e.coords);
        }

    });
}
```

コードのどこかで、位置情報を取得してその情報を元にUI要素を更新する必要があります。

### part5.js

```javascript
Ti.include('file_with_location.js');

// ウインドウをオープン
var window = Ti.UI.createWindow({
  backgroundColor:'white'
});

var coordsLbl = Titanium.UI.createLabel({
   text:"NO LOCATION",
   left: 10,
   width: 240,
   height: 'auto',
   top: 20,
   textAlign: 'left',
});

window.add(coordsLbl);
window.open();

// 現在位置を取得するコールバックを実行する。
// gpsCallbackメソッドは位置情報メソッドに渡され、
// 位置情報を受信するかエラーが発生したときに呼ばれる。
currentLocation(gpsCallback);


/**
* @param {Object} _coords 位置情報の緯度経度
*/
function gpsCallback(_coords) {
    if (_coords) {
       coordsLbl.text = String.format("lon: %s¥n lat: %s ", _coords.longitude + "", _coords.latitude + "");
    } else {
       coordsLbl.text = "NO LOCATION";
    }
}
```

## モジュールは善。それがKevinが言っていたことです。

Kevin Whinnery: Write Better JavaScript: [ビデオ](http://player.vimeo.com/video/30035164?title=0&byline=0&portrait=0&color=9a0707&autoplay=1) | [スライド](http://www.slideshare.net/slideshow/embed_code/9546534)

さて、これでGPSのコードがUIとは分離されたインクルードファイルに入りましたので、
プログラム中のどこからでも、イベントあるいはコールバックのどちらの方法でも
位置情報を得ることができるようになりました。
しかし、あと一息です。モジュール、CommonJSとその他``requires''関係は使わないのですか？

私たちは、キッチンシンクよりCodeStrongなコードを生み出すAppceleratorの良き住民でいたいのです。
何といっても、[KitchenSink](https://github.com/appcelerator/KitchenSink)はAPIの機能性を説明する目的で存在しているものであって、
ベストプラクティスの例としての役割ではないからです。

位置情報ファイルをモジュールに変換しましょう。
関数名の前に``exports''と追加してpublicにするだけです。

### maps.js

```javascript
//
Titanium.Geolocation.accuracy = Titanium.Geolocation.ACCURACY_BEST;
Titanium.Geolocation.distanceFilter = .25;
Ti.Geolocation.purpose = "Callbacks Are Your Friend";

// パブリック関数
/**
* @param {Object} _callback 位置情報の問い合わせが完了したときに呼ぶ
*/
exports.currentLocation = function(_callback) {
    Titanium.Geolocation.getCurrentPosition(function(e) {

        if(e.error) {
            alert('error ' + JSON.stringify(e.error));

            // 単純化のために、nullを返すことがエラーの意味とする
            if(_callback) {
                _callback(null);
            }
            return;
        }

        Ti.App.fireEvent('location.updated', e.coords);

        if(_callback) {
            _callback(e.coords);
        }
    });
};
```

windowsを作成しているui.jsファイルでは、currentLocation関数を含むモジュールを読み込む必要があります。
完全な例では、そのモジュールファイルをmaps.jsと名付けました。つまり、以下のようなrequire文を追加する必要があります。

*注意：requireを使うときは拡張子の``.js''を含めないでください。必要ありません。*

```javascript
var maps = require('lib/maps');
```

そして、そのメソッドを呼ぶときはこんな感じです。

```javascript
// 現在の位置情報を得るためにコールバックを実行する
maps.currentLocation(gpsCallback);
```

これが完全なui.jsファイルです。

### ui.js

```javascript
// プライベート変数
var mainWindow, callback_label_coords;
var styles = require('styles');
var maps = require('lib/maps');

/**
* @param {Object} args ウインドウのプロパティ
*/
exports.AppWindow = function(args) {
    var instance, event_label, callback_label;

    // ウインドウを作成
    instance = Ti.UI.createWindow(args);

    // コールバックのラベルを作成
    callback_label = Ti.UI.createLabel(styles.callback_label);
    instance.add(callback_label);
    callback_label_coords = Ti.UI.createLabel(styles.coords_label);
    instance.add(callback_label_coords);

    // イベントリスナーのラベルを作成
    event_label = Ti.UI.createLabel(styles.event_label);
    instance.add(event_label);
    event_label_coords = Ti.UI.createLabel(styles.coords_label);
    instance.add(event_label_coords);

    // イベントリスナーを作成
    Ti.App.addEventListener('location.updated', function(_coords) {
        event_label_coords.text = String.format("longitude: %s¥n latitude: %s ", _coords.longitude + "", _coords.latitude + "");
    });
    // 現在の位置情報を得るためにコールバックを実行する
    maps.currentLocation(gpsCallback);

    // ウインドウを保存
    mainWindow = instance;
    return instance;
};
/**
* @param {Object} _coords 位置情報の緯度経度
*/
function gpsCallback(_coords) {
    callback_label_coords.text = String.format("longitude: %s¥n latitude: %s ", _coords.longitude + "", _coords.latitude + "");
}
```

## 結論

コールバックやイベントを使うことは、あなたのコードにとって良いことです。
表現レイヤーからビジネスロジックを切り離して良い区別をすることを促します。
今後、私たちはアプリケーション開発をさらにモジュール化するアプローチを強力に促進します。
このことは近い将来、あなたへ提供する全ての内容に反映されます。

Keep coding strong!

## コード

[Github上の「CallbacksAreYourFriend」プロジェクトはこちら](https://github.com/appcelerator/Documentation-Examples/tree/master/CallbacksAreYourFriend)

